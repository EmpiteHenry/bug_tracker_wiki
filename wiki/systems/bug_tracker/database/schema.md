---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [database, postgresql, schema, migrations, testing]
status: active
---

# Database

## Backends

| Backend | Used for |
|---|---|
| PostgreSQL | Production, staging, and postgres-backed unit tests |
| In-memory (SQLite-compatible) | Default fast unit test suite |

Backend is selected via `BUG_TRACKER_TEST_DB_BACKEND` env var:
- `memory` — uses `local-test-database.ts`
- `postgres` — uses Docker PostgreSQL container

## Key DB Files (`src/lib/db/`)

| File | Purpose |
|---|---|
| `database-client.ts` | Unified database client interface |
| `postgres-client.ts` | PostgreSQL client using `pg` |
| `postgres-config.ts` | Reads connection config from env |
| `postgres-schema.ts` | Schema definitions for PostgreSQL |
| `schema.ts` | Shared schema types (`OperationalLogSeverity`, etc.) |
| `postgres-worker.ts` | Web worker for PostgreSQL operations (bundled separately) |
| `postgres-worker-bootstrap.mjs` | Worker entry point |
| `postgres-bootstrap.ts` | Bootstrap migrations on startup |
| `bootstrap-admin.ts` | Seeds initial admin user |
| `local-test-database.ts` | In-memory DB for tests |
| `local-test-database-runtime.ts` | Runtime wiring for in-memory DB |
| `test-database.ts` | Test DB factory |
| `postgres-test-reset.ts` | Resets PostgreSQL between tests |
| `postgres-test-setup.test.ts` | PostgreSQL test environment setup |
| `wait-for-promise.ts` | Utility for async DB readiness |

## Migrations

Apply with:
```bash
npm run db:migrate:postgres
```

Script: `scripts/migrate-postgres.ts`

Migrations target the local PostgreSQL container (configured via `.env`).

## PostgreSQL Worker

The `postgres-worker.ts` is bundled separately via esbuild:
```bash
npm run build:db-worker
# Output: .next/db/postgres-worker.mjs
```

Run before `dev` (`predev` hook) and included in `build`.

## Schema Domains

Domains modeled in the schema (inferred from service/store files):

| Domain | Key tables |
|---|---|
| Users & Auth | users, sessions, auth_action_tokens |
| Organizations | organizations, organization_memberships |
| Projects | projects, project_sections, project_memberships |
| Bugs | bugs, bug_attachments, bug_comments, bug_history |
| Notifications | notification_events, notification_deliveries, notification_settings |
| Monitoring | operational_logs, operational_alerts |
| Data Jobs | bug_data_jobs |
| Settings | api_keys, auth_jwt_configs |
| Audit | organization_audit_logs |
| Testing Sessions | testing_sessions, testing_session_logs |
| Invitations | organization_invitations |

## Environment Variables (`.env.example`)

```
POSTGRES_HOST=127.0.0.1
POSTGRES_PORT=5432
POSTGRES_DB=bug_tracker
POSTGRES_USER=...
POSTGRES_PASSWORD=...
```

`SENDGRID_*` variables are optional for basic local development.