# CLAUDE.MD - Enterprise Development Standards

## Purpose

This document defines enterprise-grade standards for building production-ready applications. These guidelines ensure security, performance, maintainability, and scalability across projects.

**Target Stack:** React/Node.js/TypeScript with PostgreSQL (adaptable to other enterprise stacks)

---

## Table of Contents

1. [Security Requirements](#security-requirements)
2. [Architecture Standards](#architecture-standards)
3. [Database Patterns](#database-patterns)
4. [Frontend Development (React)](#frontend-development-react)
5. [Backend Development (Node.js)](#backend-development-nodejs)
6. [Testing Requirements](#testing-requirements)
7. [Observability & Monitoring](#observability--monitoring)
8. [Performance Optimization](#performance-optimization)
9. [Accessibility & Internationalization](#accessibility--internationalization)
10. [Deployment & DevOps](#deployment--devops)
11. [Code Quality Standards](#code-quality-standards)

---

## Security Requirements

### Critical Security Practices

**Authentication & Authorization:**
- Implement JWT or OAuth2 for authentication
- Use secure session management with appropriate timeouts
- Validate tokens on every protected endpoint
- Implement role-based access control (RBAC) where needed

**Input Validation:**
- Validate and sanitize all user inputs (use libraries like Zod, Joi, or Yup)
- Implement server-side validation (never trust client-side only)
- Use parameterized queries to prevent SQL injection
- Sanitize data before rendering to prevent XSS

**Credential Management:**
- Store all secrets in environment variables or secure vaults
- Never commit credentials to version control
- Use `.env` files locally (add to `.gitignore`)
- Rotate secrets regularly in production

**Security Headers & Rate Limiting:**
- Implement security headers (use Helmet.js for Express)
- Add rate limiting to prevent abuse
- Use HTTPS in production (enforce with HSTS)
- Implement CORS policies appropriately

### Example: Environment Configuration

```typescript
// ✅ Recommended approach
import dotenv from 'dotenv';
dotenv.config();

interface Config {
  database: {
    host: string;
    port: number;
    user: string;
    password: string;
    database: string;
  };
  jwt: {
    secret: string;
    expiresIn: string;
  };
  redis?: {
    host: string;
    port: number;
  };
}

const config: Config = {
  database: {
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT || '5432'),
    user: process.env.DB_USER!,
    password: process.env.DB_PASSWORD!,
    database: process.env.DB_NAME!,
  },
  jwt: {
    secret: process.env.JWT_SECRET!,
    expiresIn: process.env.JWT_EXPIRES_IN || '24h',
  },
};

// Validate required env vars on startup
const requiredEnvVars = ['DB_USER', 'DB_PASSWORD', 'DB_NAME', 'JWT_SECRET'];
for (const envVar of requiredEnvVars) {
  if (!process.env[envVar]) {
    throw new Error(`Missing required environment variable: ${envVar}`);
  }
}

export default config;
```

---

## Architecture Standards

### Recommended Project Structure

```
/project-root/
├── /infrastructure/          # DevOps and deployment configs
│   ├── /docker/
│   ├── /kubernetes/
│   └── /scripts/
├── /backend/
│   ├── /src/
│   │   ├── /controllers/    # Route handlers
│   │   ├── /services/       # Business logic
│   │   ├── /models/         # Data models & schemas
│   │   ├── /middleware/     # Express middleware
│   │   ├── /utils/          # Utility functions
│   │   ├── /config/         # Configuration
│   │   └── /types/          # TypeScript types
│   ├── /tests/
│   └── index.ts             # Application entry point
└── /frontend/
    ├── /src/
    │   ├── /components/     # React components
    │   ├── /hooks/          # Custom hooks
    │   ├── /services/       # API clients
    │   ├── /store/          # State management
    │   ├── /utils/          # Utilities
    │   ├── /types/          # TypeScript types
    │   └── /styles/         # Global styles
    ├── /public/
    └── index.tsx            # Application entry point
```

### File Naming Conventions

- **React Components:** PascalCase (`UserProfile.tsx`, `Button.tsx`)
- **Functions/Variables:** camelCase (`getUserById`, `isAuthenticated`)
- **Constants:** UPPER_SNAKE_CASE (`MAX_RETRY_COUNT`, `API_BASE_URL`)
- **Types/Interfaces:** PascalCase (`User`, `ApiResponse`)
- **Entry Points:** Use `index.ts/tsx` for main entry files

### Layered Architecture Principles

1. **Separation of Concerns:** Keep controllers thin, services focused
2. **Dependency Injection:** Make dependencies explicit and testable
3. **Single Responsibility:** Each module should have one clear purpose
4. **DRY Principle:** Extract reusable logic into utilities or services

---

## Database Patterns

### Recommended Databases by Use Case

- **Relational Data:** PostgreSQL 15+ (preferred), MySQL 8+, or SQL Server
- **Caching:** Redis 7+ or Memcached
- **Time-Series:** TimescaleDB or InfluxDB
- **Document Store:** MongoDB (when schema flexibility is needed)
- **Search:** Elasticsearch or OpenSearch

### Database Connection Management

```typescript
// Recommended: Connection pooling with PostgreSQL
import { Pool, PoolConfig } from 'pg';
import { logger } from './logger';

const poolConfig: PoolConfig = {
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  max: parseInt(process.env.DB_POOL_SIZE || '20'),
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
};

class DatabaseService {
  private static pool: Pool;

  static initialize(): void {
    this.pool = new Pool(poolConfig);

    this.pool.on('error', (err) => {
      logger.error('Unexpected database pool error', { error: err });
    });

    this.pool.on('connect', () => {
      logger.debug('New database connection established');
    });
  }

  static async query<T>(sql: string, params: any[] = []): Promise<T[]> {
    const client = await this.pool.connect();
    try {
      const result = await client.query(sql, params);
      return result.rows;
    } catch (error) {
      logger.error('Database query failed', { sql, error });
      throw error;
    } finally {
      client.release();
    }
  }

  static async transaction<T>(
    callback: (client: any) => Promise<T>
  ): Promise<T> {
    const client = await this.pool.connect();
    try {
      await client.query('BEGIN');
      const result = await callback(client);
      await client.query('COMMIT');
      return result;
    } catch (error) {
      await client.query('ROLLBACK');
      logger.error('Transaction failed', { error });
      throw error;
    } finally {
      client.release();
    }
  }

  static async close(): Promise<void> {
    await this.pool.end();
  }
}

export default DatabaseService;
```

### SQL Best Practices

- **Use Parameterized Queries:** Prevents SQL injection
- **Implement Transactions:** For multi-step operations that must succeed/fail together
- **Add Database Indexes:** On frequently queried columns
- **Implement Pagination:** Never return unbounded result sets
- **Use Connection Pooling:** Reuse connections for better performance

---

## Frontend Development (React)

### Component Development Standards

```tsx
// Recommended React component pattern
import React, { useState, useCallback, useMemo } from 'react';
import { useQuery, useMutation } from '@tanstack/react-query';
import { z } from 'zod';

// Define prop types
interface UserProfileProps {
  userId: string;
  onUpdate?: (user: User) => void;
}

// Define data schema
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  name: z.string().min(1),
  role: z.enum(['admin', 'user', 'viewer']),
  createdAt: z.string().datetime(),
});

type User = z.infer<typeof UserSchema>;

export const UserProfile: React.FC<UserProfileProps> = ({ userId, onUpdate }) => {
  const [isEditing, setIsEditing] = useState(false);

  // Use React Query for data fetching
  const { data: user, isLoading, error, refetch } = useQuery({
    queryKey: ['user', userId],
    queryFn: async () => {
      const response = await fetch(`/api/users/${userId}`, {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
        },
      });

      if (!response.ok) {
        throw new Error(`Failed to fetch user: ${response.statusText}`);
      }

      const data = await response.json();
      return UserSchema.parse(data); // Validate response
    },
    retry: 2,
    staleTime: 5 * 60 * 1000,
  });

  // Update mutation
  const updateMutation = useMutation({
    mutationFn: async (updatedUser: Partial<User>) => {
      const response = await fetch(`/api/users/${userId}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
        },
        body: JSON.stringify(updatedUser),
      });

      if (!response.ok) {
        throw new Error('Failed to update user');
      }

      return response.json();
    },
    onSuccess: (data) => {
      onUpdate?.(data);
      refetch();
    },
  });

  // Memoize expensive computations
  const displayName = useMemo(() => {
    return user?.name || 'Anonymous User';
  }, [user?.name]);

  // Memoize callbacks
  const handleEdit = useCallback(() => {
    setIsEditing(true);
  }, []);

  // Handle loading state
  if (isLoading) {
    return (
      <div className="flex items-center justify-center p-8" role="status">
        <span className="loading-spinner" aria-label="Loading user profile" />
      </div>
    );
  }

  // Handle error state
  if (error) {
    return (
      <div className="error-container p-4" role="alert">
        <p className="text-red-600">Error: {error.message}</p>
        <button onClick={() => refetch()} className="btn-primary mt-2">
          Retry
        </button>
      </div>
    );
  }

  // Main render
  return (
    <div className="user-profile">
      <h2 className="text-2xl font-bold">{displayName}</h2>
      <p className="text-gray-600">{user?.email}</p>
      <button onClick={handleEdit} className="btn-secondary">
        Edit Profile
      </button>
    </div>
  );
};
```

### React Best Practices

**State Management:**
- Use local state for UI-only concerns
- Consider React Query for server state
- Use Context API or Redux/Zustand for global app state
- Avoid prop drilling with composition or context

**Performance:**
- Use `useMemo` for expensive calculations
- Use `useCallback` for callbacks passed to children
- Implement lazy loading for routes and large components
- Use React.memo() for expensive components that re-render often

**Hooks:**
- Extract reusable logic into custom hooks
- Implement proper cleanup in useEffect
- List all dependencies in dependency arrays
- Avoid infinite loops (be careful with object/array dependencies)

**Error Handling:**
- Implement Error Boundaries for component trees
- Handle loading and error states in UI
- Provide fallback UI for errors
- Log errors to monitoring service

---

## Backend Development (Node.js)

### Express.js Application Structure

```typescript
// Recommended Express.js setup
import express, { Request, Response, NextFunction } from 'express';
import helmet from 'helmet';
import cors from 'cors';
import rateLimit from 'express-rate-limit';
import { z } from 'zod';
import { logger } from './config/logger';
import DatabaseService from './services/database';

// Extend Request type for authenticated routes
interface AuthRequest extends Request {
  user?: {
    id: string;
    email: string;
    role: string;
  };
}

// Initialize database
DatabaseService.initialize();

// Create Express app
const app = express();

// Security middleware
app.use(helmet());

// CORS configuration
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
  credentials: true,
}));

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // per IP
  message: 'Too many requests from this IP, please try again later',
});
app.use('/api', limiter);

// Body parser with size limit
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// Request logging middleware
app.use((req: Request, res: Response, next: NextFunction) => {
  const start = Date.now();
  const requestId = req.headers['x-request-id'] || `req_${Date.now()}`;

  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.info('Request processed', {
      requestId,
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration,
      ip: req.ip,
    });
  });

  next();
});

// Health check endpoint
app.get('/health', (req: Request, res: Response) => {
  res.json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    version: process.env.APP_VERSION || '1.0.0',
  });
});

// Authentication middleware
const authenticate = async (
  req: AuthRequest,
  res: Response,
  next: NextFunction
) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');

    if (!token) {
      return res.status(401).json({ error: 'Authentication required' });
    }

    const jwt = require('jsonwebtoken');
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as any;

    req.user = {
      id: decoded.id,
      email: decoded.email,
      role: decoded.role,
    };

    next();
  } catch (error) {
    logger.warn('Authentication failed', { error });
    res.status(401).json({ error: 'Invalid or expired token' });
  }
};

// Example controller with validation
const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).max(100),
  name: z.string().min(1).max(100),
});

app.post('/api/users', async (req: Request, res: Response, next: NextFunction) => {
  try {
    // Validate input
    const validatedData = CreateUserSchema.parse(req.body);

    // Hash password
    const bcrypt = require('bcrypt');
    const hashedPassword = await bcrypt.hash(validatedData.password, 12);

    // Create user in database
    const result = await DatabaseService.query(
      `INSERT INTO users (email, password, name)
       VALUES ($1, $2, $3)
       RETURNING id, email, name, created_at`,
      [validatedData.email, hashedPassword, validatedData.name]
    );

    const user = result[0];

    logger.info('User created', { userId: user.id, email: user.email });

    res.status(201).json(user);
  } catch (error) {
    if (error instanceof z.ZodError) {
      return res.status(400).json({
        error: 'Validation failed',
        details: error.errors,
      });
    }
    next(error);
  }
});

// Global error handler (must be last)
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  logger.error('Unhandled error', {
    error: err.message,
    stack: err.stack,
    url: req.url,
    method: req.method,
  });

  res.status(500).json({
    error: 'Internal server error',
    requestId: req.headers['x-request-id'],
  });
});

// Graceful shutdown
process.on('SIGTERM', async () => {
  logger.info('SIGTERM received, shutting down gracefully');
  await DatabaseService.close();
  process.exit(0);
});

export default app;
```

### Backend Best Practices

**Input Validation:**
- Validate all inputs using schema validation libraries
- Return clear error messages for validation failures
- Validate types, ranges, formats, and business rules

**Error Handling:**
- Implement global error handler
- Use try-catch blocks in async functions
- Return appropriate HTTP status codes
- Never expose stack traces to clients in production

**API Design:**
- Use RESTful conventions or GraphQL
- Version your APIs (`/api/v1/...`)
- Implement proper HTTP methods (GET, POST, PUT, DELETE)
- Return consistent response formats

---

## Testing Requirements

### Testing Strategy

**Coverage Goals:**
- **Minimum:** 70% overall coverage
- **Recommended:** 80%+ for business logic
- **Critical Paths:** 100% coverage for security and payment flows

### Backend Testing Example

```typescript
// Recommended testing approach with Jest
import { describe, it, expect, beforeEach, afterAll } from '@jest/globals';
import request from 'supertest';
import { app } from '../src/app';
import DatabaseService from '../src/services/database';

describe('User API', () => {
  beforeEach(async () => {
    // Clean database before each test
    await DatabaseService.query('TRUNCATE TABLE users CASCADE');
  });

  afterAll(async () => {
    await DatabaseService.close();
  });

  describe('POST /api/users', () => {
    it('should create user with valid data', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'SecurePass123!',
        name: 'Test User',
      };

      const response = await request(app)
        .post('/api/users')
        .send(userData)
        .expect(201);

      expect(response.body).toMatchObject({
        id: expect.any(String),
        email: userData.email,
        name: userData.name,
      });
      expect(response.body).not.toHaveProperty('password');
    });

    it('should reject invalid email', async () => {
      const response = await request(app)
        .post('/api/users')
        .send({
          email: 'invalid-email',
          password: 'SecurePass123!',
          name: 'Test User',
        })
        .expect(400);

      expect(response.body).toHaveProperty('error', 'Validation failed');
    });

    it('should reject weak password', async () => {
      const response = await request(app)
        .post('/api/users')
        .send({
          email: 'test@example.com',
          password: '123',
          name: 'Test User',
        })
        .expect(400);
    });
  });
});
```

### Frontend Testing Example

```typescript
// Recommended React Testing Library approach
import { render, screen, waitFor, fireEvent } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { UserProfile } from '../UserProfile';

describe('UserProfile Component', () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
    },
  });

  const wrapper = ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );

  it('should display user information', async () => {
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({
          id: '123',
          email: 'test@example.com',
          name: 'Test User',
          role: 'user',
          createdAt: '2024-01-01T00:00:00Z',
        }),
      })
    ) as jest.Mock;

    render(<UserProfile userId="123" />, { wrapper });

    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
    });
  });

  it('should handle loading state', () => {
    render(<UserProfile userId="123" />, { wrapper });
    expect(screen.getByRole('status')).toBeInTheDocument();
  });

  it('should handle error state', async () => {
    global.fetch = jest.fn(() =>
      Promise.reject(new Error('Network error'))
    ) as jest.Mock;

    render(<UserProfile userId="123" />, { wrapper });

    await waitFor(() => {
      expect(screen.getByRole('alert')).toBeInTheDocument();
      expect(screen.getByText(/Error/i)).toBeInTheDocument();
    });
  });
});
```

### Testing Best Practices

- **Unit Tests:** Test individual functions and methods in isolation
- **Integration Tests:** Test API endpoints and database interactions
- **E2E Tests:** Test critical user workflows (consider Playwright or Cypress)
- **Mock External Dependencies:** Database, APIs, file system
- **Test Edge Cases:** Empty inputs, large datasets, error conditions
- **Maintain Test Data:** Use factories or fixtures for consistent test data

---

## Observability & Monitoring

### Structured Logging

```typescript
// Recommended Winston logger setup
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: process.env.SERVICE_NAME || 'api',
    environment: process.env.NODE_ENV || 'development',
  },
  transports: [
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      ),
    }),
  ],
});

// Add file transports in production
if (process.env.NODE_ENV === 'production') {
  logger.add(new winston.transports.File({
    filename: 'error.log',
    level: 'error',
    maxsize: 10485760, // 10MB
    maxFiles: 5,
  }));

  logger.add(new winston.transports.File({
    filename: 'combined.log',
    maxsize: 10485760,
    maxFiles: 5,
  }));
}

export { logger };

// Usage examples
logger.info('User login successful', { userId: '123', ip: '192.168.1.1' });
logger.warn('High memory usage detected', { usage: '85%' });
logger.error('Database connection failed', { error: err.message, stack: err.stack });
```

### Metrics Collection

```typescript
// Recommended Prometheus metrics setup
import { register, Counter, Histogram, Gauge } from 'prom-client';
import express from 'express';

// HTTP request metrics
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status'],
  buckets: [0.1, 0.5, 1, 2, 5],
});

const httpRequestTotal = new Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'route', 'status'],
});

// Business metrics
const activeUsers = new Gauge({
  name: 'active_users_total',
  help: 'Number of currently active users',
});

const databaseConnectionsActive = new Gauge({
  name: 'database_connections_active',
  help: 'Number of active database connections',
});

// Middleware to track metrics
export const metricsMiddleware = (req: any, res: any, next: any) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    httpRequestDuration.labels(req.method, req.route?.path || req.path, res.statusCode).observe(duration);
    httpRequestTotal.labels(req.method, req.route?.path || req.path, res.statusCode).inc();
  });

  next();
};

// Metrics endpoint
export const setupMetricsEndpoint = (app: express.Application) => {
  app.get('/metrics', async (req, res) => {
    res.set('Content-Type', register.contentType);
    res.end(await register.metrics());
  });
};
```

### Monitoring Best Practices

- **Log Levels:** Use appropriate levels (error, warn, info, debug)
- **Structured Logging:** Use JSON format for easy parsing
- **Sensitive Data:** Never log passwords, tokens, credit cards, or PII
- **Correlation IDs:** Track requests across services
- **Metrics:** Track request rates, durations, error rates
- **Alerting:** Set up alerts for critical errors and performance degradation
- **APM Tools:** Consider DataDog, New Relic, or Elastic APM for production

---

## Performance Optimization

### Caching Strategy

```typescript
// Recommended Redis caching pattern
import Redis from 'ioredis';
import { logger } from './logger';

const redis = new Redis({
  host: process.env.REDIS_HOST || 'localhost',
  port: parseInt(process.env.REDIS_PORT || '6379'),
  retryStrategy: (times) => {
    const delay = Math.min(times * 50, 2000);
    return delay;
  },
  maxRetriesPerRequest: 3,
});

redis.on('error', (err) => {
  logger.error('Redis connection error', { error: err });
});

export async function getCachedData<T>(
  key: string,
  fetchFn: () => Promise<T>,
  ttlSeconds: number = 3600
): Promise<T> {
  try {
    // Check cache first
    const cached = await redis.get(key);
    if (cached) {
      logger.debug('Cache hit', { key });
      return JSON.parse(cached);
    }

    logger.debug('Cache miss', { key });

    // Fetch data
    const data = await fetchFn();

    // Store in cache
    await redis.setex(key, ttlSeconds, JSON.stringify(data));

    return data;
  } catch (error) {
    logger.error('Cache operation failed', { key, error });
    // Fallback to direct fetch on cache failure
    return fetchFn();
  }
}

// Usage example
app.get('/api/users/:id', async (req, res, next) => {
  try {
    const userId = req.params.id;
    const cacheKey = `user:${userId}`;

    const user = await getCachedData(
      cacheKey,
      async () => {
        const result = await DatabaseService.query(
          'SELECT id, email, name, role FROM users WHERE id = $1',
          [userId]
        );
        return result[0];
      },
      300 // 5 minutes
    );

    res.json(user);
  } catch (error) {
    next(error);
  }
});
```

### Database Optimization

```typescript
// Recommended pagination pattern
interface PaginationParams {
  page?: number;
  limit?: number;
}

interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

async function getPaginatedResults<T>(
  tableName: string,
  params: PaginationParams,
  filters?: Record<string, any>
): Promise<PaginatedResponse<T>> {
  // Validate and sanitize pagination params
  const page = Math.max(1, params.page || 1);
  const limit = Math.min(100, Math.max(1, params.limit || 20));
  const offset = (page - 1) * limit;

  // Build WHERE clause if filters provided
  let whereClause = '';
  const queryParams: any[] = [];

  if (filters) {
    const conditions = Object.entries(filters).map(([key, value], index) => {
      queryParams.push(value);
      return `${key} = $${index + 1}`;
    });
    whereClause = conditions.length > 0 ? `WHERE ${conditions.join(' AND ')}` : '';
  }

  // Get total count
  const countQuery = `SELECT COUNT(*) FROM ${tableName} ${whereClause}`;
  const countResult = await DatabaseService.query<{ count: string }>(countQuery, queryParams);
  const total = parseInt(countResult[0].count);

  // Get paginated data
  queryParams.push(limit, offset);
  const dataQuery = `
    SELECT * FROM ${tableName}
    ${whereClause}
    ORDER BY created_at DESC
    LIMIT $${queryParams.length - 1} OFFSET $${queryParams.length}
  `;
  const data = await DatabaseService.query<T>(dataQuery, queryParams);

  return {
    data,
    pagination: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit),
    },
  };
}
```

### Performance Best Practices

- **Database Indexes:** Add indexes on frequently queried columns
- **Connection Pooling:** Reuse database connections
- **Caching:** Cache expensive operations (API calls, database queries)
- **Lazy Loading:** Load resources only when needed
- **CDN:** Serve static assets from CDN
- **Code Splitting:** Split large bundles into smaller chunks
- **Compression:** Enable gzip/brotli compression
- **Image Optimization:** Use modern formats (WebP, AVIF) and responsive images

---

## Accessibility & Internationalization

### Accessibility (a11y) Requirements

```tsx
// Recommended accessible component patterns
export const AccessibleButton: React.FC<{
  onClick: () => void;
  children: React.ReactNode;
  ariaLabel?: string;
  disabled?: boolean;
}> = ({ onClick, children, ariaLabel, disabled = false }) => {
  return (
    <button
      onClick={onClick}
      aria-label={ariaLabel}
      aria-disabled={disabled}
      disabled={disabled}
      className="btn"
      // Ensure keyboard navigation
      onKeyDown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          e.preventDefault();
          onClick();
        }
      }}
    >
      {children}
    </button>
  );
};

// Form with proper labels and validation
export const AccessibleForm: React.FC = () => {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');

  return (
    <form onSubmit={(e) => e.preventDefault()}>
      <div className="form-group">
        <label htmlFor="email" className="form-label">
          Email Address
        </label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          aria-invalid={!!error}
          aria-describedby={error ? 'email-error' : undefined}
          className="form-input"
          required
        />
        {error && (
          <div id="email-error" role="alert" className="text-red-600">
            {error}
          </div>
        )}
      </div>
    </form>
  );
};
```

### Accessibility Best Practices

- **Semantic HTML:** Use appropriate HTML elements (`<button>`, `<nav>`, `<main>`)
- **ARIA Attributes:** Use when semantic HTML isn't sufficient
- **Keyboard Navigation:** Ensure all interactive elements are keyboard accessible
- **Focus Management:** Visible focus indicators and logical tab order
- **Color Contrast:** Meet WCAG 2.1 AA standards (4.5:1 for text)
- **Alt Text:** Provide descriptive alt text for images
- **Form Labels:** Associate labels with inputs using `htmlFor`/`id`
- **Screen Reader Support:** Test with NVDA, JAWS, or VoiceOver

### Internationalization (i18n)

```typescript
// Recommended i18next setup
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import Backend from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';

i18n
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    fallbackLng: 'en',
    supportedLngs: ['en', 'es', 'fr', 'de', 'ja'],
    debug: process.env.NODE_ENV === 'development',
    interpolation: {
      escapeValue: false, // React already escapes
    },
    backend: {
      loadPath: '/locales/{{lng}}/{{ns}}.json',
    },
  });

export default i18n;

// Usage in components
import { useTranslation } from 'react-i18next';

export const WelcomeMessage: React.FC = () => {
  const { t, i18n } = useTranslation();

  return (
    <div>
      <h1>{t('welcome.title')}</h1>
      <p>{t('welcome.description', { name: 'User' })}</p>
      <button onClick={() => i18n.changeLanguage('es')}>
        Español
      </button>
    </div>
  );
};
```

### i18n Best Practices

- **External Translation Files:** Keep translations separate from code
- **Placeholder Support:** Use interpolation for dynamic content
- **Date/Time Formatting:** Use `Intl.DateTimeFormat`
- **Number Formatting:** Use `Intl.NumberFormat`
- **RTL Support:** Support right-to-left languages (Arabic, Hebrew)
- **Pluralization:** Handle plural forms correctly per language

---

## Deployment & DevOps

### Docker Best Practices

```dockerfile
# Recommended multi-stage Dockerfile for Node.js
# Build stage
FROM node:18-alpine AS builder

WORKDIR /app

# Copy dependency files
COPY package*.json ./
COPY tsconfig.json ./

# Install dependencies
RUN npm ci --only=production && \
    npm cache clean --force

# Copy source code
COPY src ./src

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine AS production

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Copy built files and dependencies
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {r.statusCode === 200 ? process.exit(0) : process.exit(1)})"

# Start application
CMD ["node", "dist/index.js"]
```

### CI/CD Pipeline Example

```yaml
# Example GitHub Actions workflow
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run type check
        run: npm run type-check

      - name: Run tests
        run: npm run test:coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - uses: actions/checkout@v3

      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .

      - name: Push to registry
        run: |
          echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
          docker push myapp:${{ github.sha }}
```

### Deployment Best Practices

- **Environment Separation:** Dev, staging, production environments
- **Immutable Infrastructure:** Use containers and avoid manual changes
- **Health Checks:** Implement liveness and readiness probes
- **Graceful Shutdown:** Handle SIGTERM properly
- **Rolling Updates:** Zero-downtime deployments
- **Rollback Strategy:** Quick rollback on deployment failures
- **Secrets Management:** Use Kubernetes Secrets, AWS Secrets Manager, or Vault
- **Monitoring:** Set up alerts before deploying to production

---

## Code Quality Standards

### TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "moduleResolution": "node",
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

### ESLint Configuration

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "prettier"
  ],
  "rules": {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "no-console": ["warn", { "allow": ["warn", "error"] }],
    "prefer-const": "error",
    "no-var": "error",
    "eqeqeq": ["error", "always"]
  }
}
```

### Code Review Checklist

- [ ] Code follows project style guidelines
- [ ] No sensitive data (credentials, API keys) in code
- [ ] All inputs validated and sanitized
- [ ] Error handling implemented
- [ ] Tests written and passing
- [ ] No console.log statements
- [ ] TypeScript strict mode passes
- [ ] Documentation updated
- [ ] Performance implications considered
- [ ] Security implications reviewed
- [ ] Accessibility requirements met

### Pre-Commit Standards

Before committing code, ensure:

1. **Linting Passes:** `npm run lint`
2. **Type Checking Passes:** `npm run type-check` or `tsc --noEmit`
3. **Tests Pass:** `npm test`
4. **Build Succeeds:** `npm run build`
5. **No Secrets:** Check for accidental credential commits
6. **Meaningful Commit Messages:** Use conventional commits format

---

## Production Readiness Checklist

Before deploying to production, verify:

### Security
- [ ] All credentials in environment variables
- [ ] HTTPS enforced in production
- [ ] Rate limiting configured
- [ ] Security headers implemented (Helmet.js)
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (sanitization)
- [ ] CSRF protection (if using sessions)
- [ ] Dependencies scanned for vulnerabilities

### Performance
- [ ] Database indexes added
- [ ] Connection pooling configured
- [ ] Caching implemented (Redis)
- [ ] API pagination implemented
- [ ] Static assets optimized
- [ ] Bundle size analyzed and optimized
- [ ] Lazy loading for large components

### Observability
- [ ] Structured logging configured
- [ ] Error tracking set up (Sentry, etc.)
- [ ] Metrics endpoint exposed
- [ ] Health check endpoint implemented
- [ ] APM tool configured (optional)
- [ ] Alerts configured for critical errors

### Quality
- [ ] Test coverage ≥70% (preferably 80%)
- [ ] TypeScript strict mode enabled
- [ ] ESLint passing with no errors
- [ ] No TypeScript `any` types
- [ ] Documentation complete

### DevOps
- [ ] Docker multi-stage build configured
- [ ] CI/CD pipeline set up
- [ ] Environment variables documented
- [ ] Database migrations automated
- [ ] Graceful shutdown implemented
- [ ] Health checks in orchestrator
- [ ] Backup strategy defined

### User Experience
- [ ] Loading states implemented
- [ ] Error states handled gracefully
- [ ] Accessibility standards met (WCAG 2.1 AA)
- [ ] Mobile responsive design
- [ ] Browser compatibility tested
- [ ] Internationalization ready (if needed)

---

## Common Anti-Patterns to Avoid

### Backend
- ❌ Using `any` type in TypeScript
- ❌ String concatenation for SQL queries
- ❌ Exposing sensitive data in API responses
- ❌ Missing error handling in async functions
- ❌ Unbounded database queries without pagination
- ❌ Synchronous file operations blocking the event loop
- ❌ Not using environment variables for configuration
- ❌ Logging sensitive information (passwords, tokens)

### Frontend
- ❌ Direct DOM manipulation in React
- ❌ Missing cleanup in useEffect hooks
- ❌ Prop drilling instead of composition/context
- ❌ Not handling loading and error states
- ❌ Trusting client-side validation alone
- ❌ Storing sensitive data in localStorage
- ❌ Missing error boundaries
- ❌ Unnecessary re-renders (missing memoization)

### General
- ❌ Committing `.env` files or secrets
- ❌ Commented-out code in production
- ❌ `console.log` statements in production
- ❌ TODO/FIXME comments in production code
- ❌ Running as root in Docker containers
- ❌ Missing health check endpoints
- ❌ No monitoring or logging

---

## Additional Resources

### Recommended Libraries

**Backend:**
- Express.js or Fastify (web framework)
- Zod or Joi (validation)
- Winston or Pino (logging)
- pg (PostgreSQL client)
- ioredis (Redis client)
- jsonwebtoken (JWT)
- bcrypt (password hashing)
- Helmet (security headers)

**Frontend:**
- React 18+
- TypeScript 5+
- React Query (server state)
- Zustand or Redux Toolkit (client state)
- React Router (routing)
- Zod (validation)
- Axios or native fetch (HTTP client)
- Testing Library (testing)

**Development:**
- ESLint + Prettier (code quality)
- Jest or Vitest (testing)
- Playwright or Cypress (E2E testing)
- Husky (git hooks)
- TypeScript strict mode

### Further Reading

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Web Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/WCAG21/quickref/)
- [Twelve-Factor App](https://12factor.net/)

---

## Conclusion

These standards are designed to be pragmatic and adaptable. Apply them based on your project's context:

- **Startups/MVPs:** Focus on security and core patterns, optimize later
- **Enterprise:** Full compliance with all standards
- **Maintenance Mode:** Gradually adopt when making changes

The goal is **production-ready code from day one** while maintaining velocity and developer experience. When in doubt, prioritize:

1. **Security** - Protect user data and system integrity
2. **Reliability** - Handle errors gracefully
3. **Maintainability** - Write code others can understand
4. **Performance** - Optimize for scale

Remember: These are **standards, not strict rules**. Use judgment and adapt to your specific needs.
