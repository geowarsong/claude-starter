# Overview

This repo ships Claude Code–ready starter files to enforce **enterprise-grade** architecture, security, and delivery standards from day one. See the full policy in **[CLAUDE.md](./CLAUDE.md)**.

> **Stack focus:** `CLAUDE.md` is optimized for **Node.js (backend)** and **React (frontend)**. You can use **MinIO** for object storage, **Redis** for background jobs/queues, and **PostgreSQL** as the primary database. If you adopt other languages or frameworks, keep the folder structure and strict rules—only replace Node/React-specific fragments.

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
- **Docker:** multi-stage builds, non-root user, health checks.
- **Release Gate:** production checklist + forbidden practices (no `console.log`, no `any`, no hardcoded secrets, etc.).

## How to use

1. Read **`CLAUDE.md`** and keep it open while coding—it’s a must-follow policy.
2. Scaffold services using the prescribed folder layout and naming rules.
3. Enforce gates in CI: type-check, lint, tests **≥80%**, and the production checklist before merge.

> For full standards and copy-paste snippets, see **[CLAUDE.md](./CLAUDE.md)**.

---

## AI usage guidelines (Claude 4)

- **Leverage thinking & interleaved thinking capabilities:**  
  Claude 4 offers thinking capabilities that are especially helpful for tasks involving reflection after tool use or complex multi-step reasoning. After receiving tool results, carefully reflect on their quality and determine optimal next steps before proceeding. Use your thinking to plan and iterate based on this new information, and then take the best next action.

- **Optimize parallel tool calling:**  
  Claude 4 models excel at parallel tool execution. They have a high success rate in using parallel tool calling without any prompting to do so, but some minor prompting can boost this behavior to ~100% success. For maximum efficiency, whenever you need to perform multiple independent operations, invoke all relevant tools **simultaneously** rather than sequentially.

- **Reduce file creation in agentic coding:**  
  If you create any temporary new files, scripts, or helper files for iteration, **clean them up** by removing these files at the end of the task.
