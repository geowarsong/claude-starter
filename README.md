# Overview

This repo ships Claude Code–ready starter files to enforce **enterprise-grade** architecture, security, and delivery standards from day one. See the full policy in **[CLAUDE.md](./CLAUDE.md)**.

## What’s inside (high level)

- **THINK First:** checklist before any code—production impact, security, performance, errors, scalability.
- **Security:** env-based secrets, input validation, parameterized queries, JWT (with refresh), HTTPS, bcrypt, rate limiting; never expose creds or log PII.
- **Architecture:** canonical React/Node structure for `/backend` and `/frontend`, plus infra folders; clear naming rules.
- **Database:** PostgreSQL + Redis (cache only) + TimescaleDB; pooling, transactions; no string-built SQL.
- **Backend (Node/Express):** security headers, CORS, rate limits, health checks, structured logging, global error handler, strict validation.
- **Frontend (React):** TypeScript, Zod-validated responses, Redux Toolkit + RTK Query, error boundaries, clean hooks patterns.
- **Testing:** Jest + Supertest + React Testing Library; **≥80%** coverage.
- **Observability:** structured logs, request logging, Prometheus metrics.
- **Performance:** Redis caching helpers, pagination, pooled DB access.
- **Docker:** multi-stage builds, non-root user, healthchecks.
- **Release Gate:** production checklist + forbidden practices (no `console.log`, no `any`, no hardcoded secrets, etc.).

## How to use

1. Read **`CLAUDE.md`** and keep it open while coding—it’s a must-follow policy.
2. Scaffold services using the prescribed folder layout and naming rules.
3. Enforce gates in CI: type-check, lint, tests **≥80%**, and the production checklist before merge.

> For full standards and copy-paste snippets, see **[CLAUDE.md](./CLAUDE.md)**.
