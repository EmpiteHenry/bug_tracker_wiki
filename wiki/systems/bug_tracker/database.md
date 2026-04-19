---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [database, postgresql, schema, migrations, testing]
status: active
---

# Database Layer

## Adapters

The app uses a **dual-database strategy**:

| Backend | When used | Config |
|---|---|---|
| PostgreSQL | Production, postgres unit tests | `BUG_TRACKER_TEST_DB_BACKEND=postgres` |
| In-memory | Default unit tests | `BUG_TRACKER_TEST_DB_BACKEND=memory` |

`src/lib/db/database-client.ts` provides `getDatabase()` which returns the appropriate adapter. `src/lib/db/index.ts` re-exports it for use across the app.

## PostgreSQL

- Client: `pg` library (`postgres-client.ts`)
- Config: `postgres-config.ts` — reads `POSTGRES_*` env vars, defaults to `127.0.0.1:5432`
- Schema: `postgres-schema.ts` — DDL definitions
- Migrations: `scripts/migrate-postgres.ts` (run via `npm run db:migrate:postgres`)
- Worker: `postgres-worker.ts` (compiled to `.next/db/postgres-worker.mjs`) — runs DB operations off the main thread
- Bootstrap: `postgres-bootstrap.ts` — seed initial admin user

## Schema

Defined in `src/lib/db/schema.ts` (shared types) and `src/lib/db/postgres-schema.ts` (PostgreSQL DDL).

Key tables inferred from service/store files:

| Table | Purpose |
|---|---|
| `users` | User accounts |
| `sessions` | Auth sessions |
| `auth_action_tokens` | Email verification / password reset tokens |
| `organizations` | Tenant organizations |
| `organization_memberships` | User ↔ org membership + role |
| `organization_invitations` | Pending invitations |
| `projects` | Bug projects |
| `project_sections` | Sections within projects |
| `project_memberships` | User ↔ project access |
| `bugs` | Bug records |
| `bug_comments` | Bug comments |
| `bug_attachments` | Attachment metadata |
| `bug_history` | Audit trail of bug changes |
| `bug_data_jobs` | Import/export async jobs |
| `testing_sessions` | QA testing sessions |
| `testing_session_logs` | Log entries for testing sessions |
| `notifications` | Notification event records |
| `notification_deliveries` | Per-recipient delivery records |
| `notification_settings` | Event/recipient-group enable flags |
| `operational_logs` | App observability log entries |
| `operational_alerts` | Raised operational alerts |
| `api_keys` | API key credentials |

## Testing

- **In-memory**: `local-test-database.ts` + `local-test-database-runtime.ts`
- **PostgreSQL tests**: `postgres-test-setup.test.ts`, `postgres-test-reset.ts`
- Test isolation: `postgres-test-reset.ts` truncates tables between runs
- `wait-for-promise.ts` — retry helper for async DB readiness checks
