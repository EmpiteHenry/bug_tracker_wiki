---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [database, postgresql, schema, migrations]
status: active
---

# Database

## Backend Selection

The application supports two database backends:

| Backend | When used | Config |
|---|---|---|
| PostgreSQL | Production and `test:unit:postgres` | `DATABASE_URL` env var |
| In-memory | Default `npm test` / unit tests | `BUG_TRACKER_TEST_DB_BACKEND=memory` |

The active backend is selected at startup via `src/lib/db/database-client.ts` → `getDatabase()`. The in-memory adapter mirrors the PostgreSQL schema so tests run without Docker.

## PostgreSQL Setup

- Client: `pg` (node-postgres)
- Config: `src/lib/db/postgres-config.ts` reads env vars
- Client pool: `src/lib/db/postgres-client.ts`
- Schema DDL: `src/lib/db/postgres-schema.ts`
- Migrations: `scripts/migrate-postgres.ts` — run via `npm run db:migrate:postgres`
- Bootstrap (admin seed): `src/lib/db/bootstrap-admin.ts`

## Worker Thread

PostgreSQL queries run inside a worker thread to avoid blocking the Next.js server event loop:

- Worker entry: `src/lib/db/postgres-worker.ts`
- Bundled to `.next/db/postgres-worker.mjs` via esbuild during `build:db-worker`
- Bootstrap for worker: `src/lib/db/postgres-worker-bootstrap.mjs`

## Schema Highlights (from `src/lib/db/schema.ts`)

Key tables inferred from service and store files:

| Table | Purpose |
|---|---|
| `users` | User accounts |
| `sessions` | Active login sessions |
| `auth_action_tokens` | One-time tokens (verify email, reset password) |
| `organizations` | Tenant workspaces |
| `organization_memberships` | User ↔ org membership with roles |
| `organization_invitations` | Pending invites |
| `projects` | Bug projects within an org |
| `project_sections` | Optional sections within a project |
| `project_memberships` | User ↔ project access |
| `bugs` | Core bug records |
| `bug_comments` | Comments on bugs |
| `bug_attachments` | File attachments |
| `bug_history` | Audit trail of field changes |
| `bug_data_jobs` | Async import/export job tracking |
| `operational_logs` | Structured observability logs |
| `operational_alerts` | Derived alert records |
| `notifications` | Notification events |
| `notification_deliveries` | Per-recipient email delivery records |
| `api_keys` | User API keys for agent access |
| `testing_sessions` | Manual QA testing session records |

## Test Utilities

- `src/lib/db/local-test-database.ts` — in-memory DB factory
- `src/lib/db/postgres-test-reset.ts` — truncate tables between tests
- `src/lib/db/test-database.ts` — unified test DB interface
- `src/lib/db/postgres-test-setup.test.ts` — validates schema parity