---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [database, postgresql, schema, migration]
status: active
---

# Database

## Backends

| Backend | When used |
|---|---|
| PostgreSQL | Production and `npm run test:unit:postgres` |
| In-memory (SQLite-compatible) | Default unit tests (`npm test`) |

Controlled by `BUG_TRACKER_TEST_DB_BACKEND` env var.

## Key Files

| File | Purpose |
|---|---|
| `src/lib/db/schema.ts` | Shared schema types (used by both backends) |
| `src/lib/db/postgres-schema.ts` | PostgreSQL DDL definitions |
| `src/lib/db/postgres-client.ts` | PostgreSQL connection pool (uses `pg`) |
| `src/lib/db/postgres-config.ts` | Config from env vars |
| `src/lib/db/database-client.ts` | Abstraction layer returned by `getDatabase()` |
| `src/lib/db/index.ts` | `getDatabase()` export |
| `src/lib/db/postgres-worker.ts` | Worker thread for CPU-bound DB operations |
| `src/lib/db/bootstrap-admin.ts` | Seeds initial admin user |
| `src/lib/db/postgres-bootstrap.ts` | Creates schema if not exists |

## Worker Pattern

`postgres-worker.ts` runs in a Node.js worker thread (built to `.next/db/postgres-worker.mjs` by `build:db-worker`). The `predev` script builds it before `next dev` starts.

## Migrations

```bash
npm run db:migrate:postgres
```

Runs `scripts/migrate-postgres.ts` with env loaded from `.env`.

## Key Schema Types (from `schema.ts`)

- `OperationalLogSeverity` — `"debug" | "info" | "warn" | "error" | "critical"`
- `OperationalAlertStatus` — alert lifecycle states
- Bug job statuses — `"queued" | "running" | "completed" | "completed_with_warnings" | "failed"`

## Local Setup

Requires Docker. Run:
```bash
docker compose up -d postgres
npm run db:migrate:postgres
```

Default connection: `127.0.0.1:5432`. See `.env.example` for full config.

## Test Database

`local-test-database.ts` + `local-test-database-runtime.ts` — in-memory database for fast unit tests, no Docker needed.

`postgres-test-reset.ts` — resets schema between postgres test runs.