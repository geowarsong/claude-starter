# CLAUDE.md - Enterprise Production Standards for React/Node.js

## 🚨 IMPORTANT: MANDATORY COMPLIANCE DOCUMENT

**THINK**: This document contains **CRITICAL REQUIREMENTS** that Claude Code MUST **ALWAYS** follow when generating code. **NO** exceptions allowed.

---

## 🧠 IMPORTANT: THINK FIRST PRINCIPLE

### ALWAYS THINK Before ANY Code Generation:
1. **THINK** about production implications
2. **THINK** about security vulnerabilities  
3. **THINK** about performance bottlenecks
4. **THINK** about error scenarios
5. **THINK** about scalability requirements

**NEVER** write code without **THINKING** through these aspects first.

---

## 🔐 IMPORTANT: SECURITY REQUIREMENTS

### ALWAYS Follow These Security Rules:
- ✅ **ALWAYS** use environment variables for credentials
- ✅ **ALWAYS** validate and sanitize ALL inputs
- ✅ **ALWAYS** use parameterized queries
- ✅ **ALWAYS** implement JWT authentication properly
- ✅ **ALWAYS** use HTTPS in production
- ✅ **ALWAYS** hash passwords with bcrypt (min 10 rounds)
- ✅ **ALWAYS** implement rate limiting
- ❌ **NEVER** expose credentials in code
- ❌ **NEVER** trust client-side validation alone
- ❌ **NEVER** log sensitive data (passwords, tokens, PII)
- ❌ **NO** SQL string concatenation
- ❌ **NO** eval() or Function() constructor usage

### IMPORTANT: Credential Management
```typescript
// ✅ YES - ALWAYS do this
import dotenv from 'dotenv';
dotenv.config();

const config = {
  database: {
    host: process.env.DB_HOST,
    port: parseInt(process.env.DB_PORT || '5432'),
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME
  },
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '24h'
  }
};

// ❌ NO - NEVER do this
const config = {
  database: {
    host: 'localhost',
    password: 'Admin2024Pass' // NEVER!
  },
  jwt: {
    secret: 'my-secret-key' // NEVER!
  }
};
```

---

## 🏗️ IMPORTANT: ARCHITECTURE STANDARDS

### ALWAYS Use This Project Structure:
```
/platform/
├── /infrastructure/
│   ├── /docker/          # IMPORTANT: ALL Docker files here ONLY
│   ├── /kubernetes/      # YES: K8s manifests here
│   └── /scripts/         # YES: Deployment scripts ONLY here
├── /backend/
│   ├── /src/
│   │   ├── /controllers/   # ALWAYS: Route handlers
│   │   ├── /services/      # ALWAYS: Business logic
│   │   ├── /models/        # ALWAYS: Data models
│   │   ├── /middleware/    # ALWAYS: Express middleware
│   │   ├── /utils/         # ALWAYS: Utility functions
│   │   └── /config/        # ALWAYS: Configuration
│   └── index.ts            # YES: Single entry point
└── /frontend/
    ├── /src/
    │   ├── /components/    # ALWAYS: React components
    │   ├── /hooks/         # ALWAYS: Custom hooks
    │   ├── /services/      # ALWAYS: API calls
    │   ├── /store/         # ALWAYS: State management
    │   └── /utils/         # ALWAYS: Utilities
    └── index.tsx           # YES: Single entry point
```

### IMPORTANT: File Naming Rules
- ✅ **ALWAYS** use `index.ts` or `index.tsx` as entry points
- ✅ **ALWAYS** use PascalCase for React components
- ✅ **ALWAYS** use camelCase for functions and variables
- ❌ **NEVER** create `index_simple.ts`, `index_test.ts` variants
- ❌ **NO** duplicate entry points allowed

---

## 💾 IMPORTANT: DATABASE STANDARDS

### ALWAYS Use These Technologies:
- ✅ **YES**: PostgreSQL 17 as primary database
- ✅ **YES**: Redis for caching ONLY
- ✅ **YES**: TimescaleDB for time-series data
- ❌ **NO**: MongoDB (NEVER use document stores)
- ❌ **NO**: MySQL or other databases
- ❌ **NEVER**: Store persistent data in Redis

### IMPORTANT: Node.js Database Access
```typescript
// ✅ YES - ALWAYS use connection pooling with pg
import { Pool } from 'pg';
import { logger } from './logger';

class DatabaseService {
  private pool: Pool;

  constructor() {
    // ALWAYS use connection pooling
    this.pool = new Pool({
      host: process.env.DB_HOST,
      port: parseInt(process.env.DB_PORT || '5432'),
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME,
      max: 20, // IMPORTANT: Max pool size
      idleTimeoutMillis: 30000,
      connectionTimeoutMillis: 2000,
    });

    // ALWAYS handle pool errors
    this.pool.on('error', (err) => {
      logger.error('Unexpected database error', err);
    });
  }

  // ALWAYS use parameterized queries
  async executeQuery<T>(query: string, params: any[] = []): Promise<T[]> {
    const client = await this.pool.connect();
    try {
      const result = await client.query(query, params);
      return result.rows;
    } catch (error) {
      logger.error('Query execution failed', { query, error });
      throw error;
    } finally {
      client.release(); // ALWAYS release connections
    }
  }

  // ALWAYS use transactions for multiple operations
  async executeTransaction<T>(
    queries: Array<{ query: string; params: any[] }>
  ): Promise<T[]> {
    const client = await this.pool.connect();
    try {
      await client.query('BEGIN');
      const results = [];
      for (const { query, params } of queries) {
        const result = await client.query(query, params);
        results.push(result.rows);
      }
      await client.query('COMMIT');
      return results;
    } catch (error) {
      await client.query('ROLLBACK');
      logger.error('Transaction failed', { error });
      throw error;
    } finally {
      client.release();
    }
  }
}

// ❌ NO - NEVER do string concatenation
async badQuery(userId: string) {
  // NEVER do this - SQL injection vulnerability!
  const query = `SELECT * FROM users WHERE id = '${userId}'`;
}
```

---

## 🎨 IMPORTANT: REACT FRONTEND STANDARDS

### ALWAYS Follow React Best Practices:

```tsx
// ✅ YES - ALWAYS write components like this
import React, { useState, useCallback, useMemo } from 'react';
import { useQuery, useMutation } from '@tanstack/react-query';
import { z } from 'zod';

// ALWAYS validate props with TypeScript
interface UserProfileProps {
  userId: string;
  onUpdate?: (user: User) => void;
}

// ALWAYS validate API responses
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  name: z.string().min(1),
  role: z.enum(['admin', 'user']),
});

type User = z.infer<typeof UserSchema>;

// IMPORTANT: ALWAYS use proper error boundaries
export const UserProfile: React.FC<UserProfileProps> = ({ userId, onUpdate }) => {
  // ALWAYS use proper state management
  const [isEditing, setIsEditing] = useState(false);

  // ALWAYS use React Query for API calls
  const { data: user, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: async () => {
      const response = await fetch(`/api/users/${userId}`, {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
        },
      });

      if (!response.ok) {
        throw new Error('Failed to fetch user');
      }

      const data = await response.json();
      return UserSchema.parse(data); // ALWAYS validate API responses
    },
    retry: 3, // ALWAYS implement retry logic
    staleTime: 5 * 60 * 1000, // THINK about cache strategy
  });

  // ALWAYS memoize expensive computations
  const displayName = useMemo(() => {
    return user?.name || 'Anonymous User';
  }, [user?.name]);

  // ALWAYS use useCallback for event handlers
  const handleEdit = useCallback(() => {
    setIsEditing(true);
  }, []);

  // IMPORTANT: ALWAYS handle loading and error states
  if (isLoading) {
    return <div className="loading-spinner">Loading...</div>;
  }

  if (error) {
    return (
      <div className="error-container">
        <p>Error loading user: {error.message}</p>
        <button onClick={() => window.location.reload()}>Retry</button>
      </div>
    );
  }

  return (
    <div className="user-profile">
      <h2>{displayName}</h2>
      {/* Component content */}
    </div>
  );
};

// ❌ NO - NEVER write components like this
export const BadComponent = (props: any) => { // NO: any type
  const [data, setData] = useState(); // NO: untyped state

  // NEVER fetch in useEffect without cleanup
  useEffect(() => {
    fetch('/api/data').then(res => res.json()).then(setData);
  }, []); // NO: Missing error handling, cleanup, dependencies

  return <div>{data}</div>; // NO: No loading/error states
};
```

### IMPORTANT: Custom Hooks Pattern
```typescript
// ALWAYS create custom hooks for reusable logic
export const useAuth = () => {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  // ALWAYS implement proper cleanup
  useEffect(() => {
    let cancelled = false;

    const checkAuth = async () => {
      try {
        const token = localStorage.getItem('token');
        if (!token) {
          throw new Error('No token');
        }

        const response = await fetch('/api/auth/me', {
          headers: { Authorization: `Bearer ${token}` },
        });

        if (!cancelled) {
          const userData = await response.json();
          setUser(UserSchema.parse(userData));
        }
      } catch (error) {
        if (!cancelled) {
          setUser(null);
        }
      } finally {
        if (!cancelled) {
          setIsLoading(false);
        }
      }
    };

    checkAuth();

    // ALWAYS return cleanup function
    return () => {
      cancelled = true;
    };
  }, []);

  return { user, isLoading, isAuthenticated: !!user };
};
```

---

## 🚀 IMPORTANT: NODE.JS BACKEND STANDARDS

### ALWAYS Structure Express Apps Properly:

```typescript
// ✅ YES - ALWAYS structure your app like this
import express, { Request, Response, NextFunction } from 'express';
import helmet from 'helmet';
import cors from 'cors';
import rateLimit from 'express-rate-limit';
import { z } from 'zod';
import winston from 'winston';
import { Pool } from 'pg';
import jwt from 'jsonwebtoken';

// IMPORTANT: ALWAYS set up structured logging
const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      ),
    }),
  ],
});

// IMPORTANT: ALWAYS use TypeScript interfaces
interface AuthRequest extends Request {
  user?: {
    id: string;
    email: string;
    role: string;
  };
}

// ALWAYS validate request schemas
const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(1).max(100),
});

class UserController {
  constructor(private db: Pool) {}

  // ALWAYS use proper error handling
  createUser = async (req: Request, res: Response, next: NextFunction) => {
    try {
      // ALWAYS validate input
      const validatedData = CreateUserSchema.parse(req.body);

      // ALWAYS hash passwords
      const hashedPassword = await bcrypt.hash(validatedData.password, 12);

      // ALWAYS use transactions for critical operations
      const client = await this.db.connect();
      try {
        await client.query('BEGIN');

        // ALWAYS use parameterized queries
        const userResult = await client.query(
          `INSERT INTO users (email, password, name)
           VALUES ($1, $2, $3)
           RETURNING id, email, name, created_at`,
          [validatedData.email, hashedPassword, validatedData.name]
        );

        // ALWAYS log important operations
        logger.info('User created', {
          userId: userResult.rows[0].id,
          email: validatedData.email
        });

        await client.query('COMMIT');

        // NEVER return sensitive data
        const { password, ...userWithoutPassword } = userResult.rows[0];
        res.status(201).json(userWithoutPassword);

      } catch (error) {
        await client.query('ROLLBACK');
        throw error;
      } finally {
        client.release();
      }
    } catch (error) {
      // ALWAYS use proper error handling
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: error.errors
        });
      }

      logger.error('Failed to create user', { error });
      next(error);
    }
  };
}

// IMPORTANT: ALWAYS implement authentication middleware
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

    // ALWAYS verify JWT properly
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as any;

    // ALWAYS validate token payload
    req.user = {
      id: decoded.id,
      email: decoded.email,
      role: decoded.role,
    };

    next();
  } catch (error) {
    logger.error('Authentication failed', { error });
    res.status(401).json({ error: 'Invalid token' });
  }
};

// IMPORTANT: ALWAYS set up the app with security middleware
const app = express();

// ALWAYS use security headers
app.use(helmet());

// ALWAYS implement CORS properly
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
  credentials: true,
}));

// ALWAYS implement rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP',
});
app.use('/api', limiter);

// ALWAYS parse JSON with size limit
app.use(express.json({ limit: '10mb' }));

// ALWAYS implement health checks
app.get('/health', (req, res) => {
  res.json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
  });
});

// ALWAYS implement global error handler
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

// ❌ NO - NEVER write Express apps like this
app.get('/user/:id', (req, res) => {
  // NEVER: No validation, no error handling, no auth
  const query = `SELECT * FROM users WHERE id = ${req.params.id}`; // NEVER!
  db.query(query, (err, result) => {
    res.json(result); // NEVER: Exposes all data including passwords
  });
});
```

---

## 🧪 IMPORTANT: TESTING REQUIREMENTS

### ALWAYS Write Comprehensive Tests:

```typescript
// ✅ YES - ALWAYS write tests like this
import { describe, it, expect, beforeEach, afterEach, jest } from '@jest/globals';
import request from 'supertest';
import { app } from '../app';
import { pool } from '../db';

describe('UserController', () => {
  // ALWAYS clean up after tests
  afterEach(async () => {
    await pool.query('TRUNCATE TABLE users CASCADE');
  });

  // ALWAYS close connections after all tests
  afterAll(async () => {
    await pool.end();
  });

  describe('POST /api/users', () => {
    it('should create a user with valid data', async () => {
      // ALWAYS test the happy path
      const userData = {
        email: 'test@example.com',
        password: 'SecurePass123!',
        name: 'Test User',
      };

      const response = await request(app)
        .post('/api/users')
        .send(userData)
        .expect(201);

      // ALWAYS assert on response structure
      expect(response.body).toHaveProperty('id');
      expect(response.body.email).toBe(userData.email);
      expect(response.body).not.toHaveProperty('password'); // NEVER expose passwords
    });

    it('should reject invalid email', async () => {
      // ALWAYS test validation
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

    it('should handle database errors gracefully', async () => {
      // ALWAYS test error scenarios
      jest.spyOn(pool, 'connect').mockRejectedValueOnce(new Error('DB Error'));

      const response = await request(app)
        .post('/api/users')
        .send({
          email: 'test@example.com',
          password: 'SecurePass123!',
          name: 'Test User',
        })
        .expect(500);

      expect(response.body).toHaveProperty('error', 'Internal server error');
    });
  });
});

// React Component Testing
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
    // ALWAYS mock API calls
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({
          id: '123',
          email: 'test@example.com',
          name: 'Test User',
          role: 'user',
        }),
      })
    ) as jest.Mock;

    render(<UserProfile userId="123" />, { wrapper });

    // ALWAYS wait for async operations
    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
    });
  });

  it('should handle loading state', () => {
    render(<UserProfile userId="123" />, { wrapper });

    // ALWAYS test loading states
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });
});
```

### IMPORTANT: Test Coverage Requirements
- ✅ **ALWAYS** aim for 80% minimum coverage
- ✅ **ALWAYS** test happy paths
- ✅ **ALWAYS** test error scenarios
- ✅ **ALWAYS** test edge cases
- ❌ **NEVER** skip tests for "simple" functions
- ❌ **NO** untested production code

---

## 📊 IMPORTANT: MONITORING & LOGGING

### ALWAYS Implement Observability:

```typescript
// IMPORTANT: ALWAYS use structured logging
import winston from 'winston';
import { Request, Response, NextFunction } from 'express';

const logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    // ALWAYS log to files in production
    new winston.transports.File({
      filename: 'error.log',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'combined.log'
    })
  ],
});

// IMPORTANT: ALWAYS add request logging middleware
const requestLogger = (req: Request, res: Response, next: NextFunction) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;

    logger.info('Request processed', {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration,
      ip: req.ip,
      userAgent: req.headers['user-agent'],
      requestId: req.headers['x-request-id'],
    });
  });

  next();
};

// IMPORTANT: ALWAYS implement metrics
import { register, Counter, Histogram } from 'prom-client';

const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status'],
});

const httpRequestTotal = new Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'route', 'status'],
});

// ALWAYS expose metrics endpoint
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', register.contentType);
  res.end(await register.metrics());
});
```

---

## 🚀 IMPORTANT: PERFORMANCE REQUIREMENTS

### ALWAYS Optimize Performance:

```typescript
// IMPORTANT: ALWAYS implement caching
import Redis from 'ioredis';

const redis = new Redis({
  host: process.env.REDIS_HOST,
  port: parseInt(process.env.REDIS_PORT || '6379'),
  retryStrategy: (times) => {
    const delay = Math.min(times * 50, 2000);
    return delay;
  },
});

// ALWAYS use caching for expensive operations
const getCachedData = async <T>(
  key: string,
  fetchFn: () => Promise<T>,
  ttl: number = 3600
): Promise<T> => {
  try {
    // ALWAYS check cache first
    const cached = await redis.get(key);
    if (cached) {
      return JSON.parse(cached);
    }

    // THINK about race conditions
    const data = await fetchFn();

    // ALWAYS set expiration
    await redis.setex(key, ttl, JSON.stringify(data));

    return data;
  } catch (error) {
    logger.error('Cache operation failed', { key, error });
    // ALWAYS fallback to direct fetch on cache failure
    return fetchFn();
  }
};

// IMPORTANT: ALWAYS use database connection pooling
const pool = new Pool({
  max: 20, // THINK about connection limits
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

// IMPORTANT: ALWAYS implement pagination
const getPaginatedResults = async (
  table: string,
  page: number = 1,
  limit: number = 20
) => {
  // ALWAYS validate pagination params
  const validPage = Math.max(1, page);
  const validLimit = Math.min(100, Math.max(1, limit)); // NEVER allow unlimited results

  const offset = (validPage - 1) * validLimit;

  const query = `
    SELECT * FROM ${table}
    ORDER BY created_at DESC
    LIMIT $1 OFFSET $2
  `;

  return pool.query(query, [validLimit, offset]);
};
```

---

## 🐳 IMPORTANT: DOCKER REQUIREMENTS

### ALWAYS Use Multi-Stage Builds:

```dockerfile
# ✅ YES - ALWAYS use multi-stage builds
FROM node:18-alpine AS builder

# ALWAYS set working directory
WORKDIR /app

# ALWAYS copy package files first for better caching
COPY package*.json ./
COPY tsconfig.json ./

# ALWAYS use npm ci for production
RUN npm ci --only=production

# ALWAYS copy source after dependencies
COPY src ./src

# ALWAYS build TypeScript
RUN npm run build

# Production stage
FROM node:18-alpine

# ALWAYS run as non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

WORKDIR /app

# ALWAYS copy only necessary files
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./

USER nodejs

# ALWAYS expose port
EXPOSE 3000

# ALWAYS include health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {r.statusCode === 200 ? process.exit(0) : process.exit(1)})"

# ALWAYS use node directly, not npm start
CMD ["node", "dist/index.js"]
```

---

## ✅ IMPORTANT: PRODUCTION CHECKLIST

### THINK: Before ANY Code Goes to Production:

**ALWAYS verify these requirements:**
- [ ] **NO** exposed credentials anywhere
- [ ] **NO** console.log statements
- [ ] **NO** commented-out code
- [ ] **NO** TODO or FIXME comments
- [ ] **YES** All inputs validated with Zod
- [ ] **YES** All errors handled properly
- [ ] **YES** Structured logging implemented
- [ ] **YES** Authentication on all protected routes
- [ ] **YES** Rate limiting configured
- [ ] **YES** Database connection pooling
- [ ] **YES** Redis caching where appropriate
- [ ] **YES** 80%+ test coverage
- [ ] **YES** TypeScript strict mode enabled
- [ ] **YES** Docker multi-stage build
- [ ] **YES** Health check endpoint
- [ ] **YES** Metrics endpoint
- [ ] **YES** Environment variables for all config

---

## 🚫 IMPORTANT: FORBIDDEN PRACTICES

### NEVER Include These in Production:

- ❌ **NEVER** `console.log()` - use structured logging
- ❌ **NEVER** `any` type - ALWAYS use proper TypeScript types
- ❌ **NEVER** hardcoded URLs or credentials
- ❌ **NEVER** synchronous file operations
- ❌ **NEVER** unbounded queries without pagination
- ❌ **NEVER** missing error boundaries in React
- ❌ **NEVER** direct DOM manipulation in React
- ❌ **NO** `eval()` or `new Function()`
- ❌ **NO** SQL string concatenation
- ❌ **NO** passwords in logs
- ❌ **NO** unhandled promise rejections
- ❌ **NO** missing cleanup in useEffect
- ❌ **NO** infinite loops in useEffect

---

## 💡 IMPORTANT: FINAL REMINDER

**THINK** before you code:
- 🔒 **THINK** Security First
- 🚀 **THINK** Performance Always  
- 🧪 **THINK** Test Everything
- 📝 **THINK** Document Clearly
- 🔍 **THINK** Monitor Everything
- 💪 **THINK** Handle All Errors
- 🎯 **THINK** Production-Ready

**ALWAYS** remember: **NO** shortcuts, **NO** exceptions, **ALWAYS** enterprise-grade.

**NEVER** compromise on quality. **ALWAYS** write code that's production-ready from day one.
