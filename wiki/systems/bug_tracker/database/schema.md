---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [database, postgresql, schema, migrations]
status: active
---

# Database Schema & Configuration

## Database Abstraction

`src/lib/db/database-client.ts` — abstract database client interface  
`src/lib/db/index.ts` — `getDatabase()` factory, returns the configured backend  
`src/lib/db/schema.ts` — TypeScript type definitions for all database tables  
`src/lib/db/postgres-schema.ts` — PostgreSQL-specific DDL and schema definitions

## Backends

### PostgreSQL (Production)

Configuration: `src/lib/db/postgres-config.ts`  
Client: `src/lib/db/postgres-client.ts`  
Bootstrap: `src/lib/db/postgres-bootstrap.ts`

Connection settings come from environment variables (see `.env.example`). Default local values: `127.0.0.1:5432`.

**Worker architecture**: Some database operations run in a dedicated worker thread via `src/lib/db/postgres-worker.ts`. The worker is bundled separately by esbuild into `.next/db/postgres-worker.mjs` at build time (`npm run build:db-worker`). `postgres-worker-bootstrap.mjs` initialises the worker context.

### In-Memory SQLite (Tests)

`src/lib/db/local-test-database.ts` — SQLite-backed test database  
`src/lib/db/local-test-database-runtime.ts` — runtime initialisation  
`src/lib/db/test-database.ts` — test database factory  
`src/lib/db/postgres-test-reset.ts` — resets PostgreSQL between test runs  
`src/lib/db/postgres-test-setup.test.ts` — test setup verification

Selected via `BUG_TRACKER_TEST_DB_BACKEND=memory` (default for `npm test`) or `postgres`.

## Key Tables (inferred from service layer)

| Table | Description |
|---|---|
| `users` | User accounts, `isAdmin` flag |
| `sessions` | Session tokens with expiry |
| `auth_action_tokens` | Password reset / email verification tokens |
| `organizations` | Workspace tenants |
| `organization_members` | User ↔ org membership |
| `organization_invitations` | Pending invitations |
| `projects` | Projects within an org |
| `project_sections` | Sections within a project |
| `project_members` | User ↔ project membership |
| `bugs` | Bug records |
| `bug_comments` | Comments on bugs |
| `bug_attachments` | Attachment metadata |
| `bug_history` | Change events per bug |
| `bug_data_jobs` | Import/export job records |
| `testing_sessions` | Tester sessions |
| `testing_session_logs` | Per-session activity log |
| `operational_logs` | Observability log entries |
| `operational_alerts` | Actionable alert records |
| `notifications` | Notification event records |
| `notification_deliveries` | Per-delivery status records |
| `api_keys` | Organization API keys |

## Migrations

Run with: `npm run db:migrate:postgres`

Script: `scripts/migrate-postgres.ts` — applies pending migrations against the configured PostgreSQL instance.

## Type Definitions

`src/lib/db/schema.ts` exports TypeScript types for all tables. `OperationalLogSeverity` is one example: `"debug" | "info" | "warn" | "error" | "critical"`. `OperationalAlertStatus` is `"open" | "acknowledged" | "resolved"`.