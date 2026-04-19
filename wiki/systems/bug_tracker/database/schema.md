---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [database, postgresql, schema, migrations, testing]
status: active
---

# Database Architecture

## Overview

The app uses **PostgreSQL** as its primary database. For unit tests, an **in-memory SQLite** backend is available to avoid Docker dependency.

## Database Client Architecture

PostgreSQL access is intentionally isolated in a **Worker thread** to avoid connection pool issues in Next.js serverless/edge environments:

```
Next.js Request Handler
  └── getDatabase()           [src/lib/db/index.ts]
       └── postgres-client.ts — decides backend from env
            ├── PostgresWorker (prod/integration)
            │    └── postgres-worker.ts  [Worker thread]
            │         └── pg (node-postgres)
            └── LocalTestDatabase (unit tests, in-memory)
```

The worker is pre-built to `.next/db/postgres-worker.mjs` during `npm run build` and `predev`.

## Backend Selection

Set via environment variable:

```
BUG_TRACKER_TEST_DB_BACKEND=memory    # in-memory SQLite (unit tests)
BUG_TRACKER_TEST_DB_BACKEND=postgres  # real PostgreSQL
(unset)                               # defaults to PostgreSQL in production
```

## Key Files

| File | Role |
|---|---|
| `src/lib/db/schema.ts` | TypeScript schema definitions |
| `src/lib/db/postgres-schema.ts` | PostgreSQL DDL schema |
| `src/lib/db/postgres-client.ts` | PostgreSQL client setup |
| `src/lib/db/postgres-worker.ts` | Worker thread entry point |
| `src/lib/db/postgres-config.ts` | Config from env vars |
| `src/lib/db/database-client.ts` | Shared client interface |
| `src/lib/db/local-test-database.ts` | In-memory SQLite for tests |
| `src/lib/db/bootstrap-admin.ts` | Admin seed on first run |
| `src/lib/db/postgres-bootstrap.ts` | Schema bootstrap |

## Migrations

Run against local PostgreSQL:

```bash
npm run db:migrate:postgres
```

This executes `scripts/migrate-postgres.ts` using env from `.env`.

## Local Setup

```bash
# Start PostgreSQL via Docker Compose
docker compose up -d postgres

# Apply migrations
npm run db:migrate:postgres
```

Default connection: `127.0.0.1:5432` (from `.env.example`).

## Test Strategy

- **Fast unit tests** (`npm test`): use in-memory backend, no Docker required
- **PostgreSQL unit tests** (`npm run test:unit:postgres`): require running container, run via `scripts/run-postgres-unit-tests.sh`
- **E2E tests**: seed via `npm run test:e2e:seed`, run against real DB at port 3001