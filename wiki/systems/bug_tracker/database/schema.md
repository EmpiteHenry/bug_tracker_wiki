---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 5
tags: [database, postgresql, schema, migrations]
status: active
---

# Database Schema & Configuration

## Database Backends

The app supports two backends selected by `BUG_TRACKER_TEST_DB_BACKEND`:

| Mode | Used when | File |
|---|---|---|
| `memory` | Unit tests (`npm test`) | `local-test-database.ts` |
| `postgres` | Development and production | `postgres-client.ts` |

The `database-client.ts` module exports `getDatabase()` which returns the appropriate client based on environment.

## PostgreSQL Setup

Config loaded from environment variables in `postgres-config.ts`:
- `POSTGRES_HOST` (default `127.0.0.1`)
- `POSTGRES_PORT` (default `5432`)
- `POSTGRES_DB`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`

Local dev uses Docker Compose (`compose.yaml`, service `postgres`).

## Migrations

```bash
npm run db:migrate:postgres
```

Runs `scripts/migrate-postgres.ts` which applies SQL migrations in order.

Schema source files:
- `src/lib/db/schema.ts` — shared TypeScript schema types
- `src/lib/db/postgres-schema.ts` — PostgreSQL-specific DDL helpers

## Postgres Worker

DB operations run in a separate worker thread (`postgres-worker.ts`) to keep the Next.js main thread non-blocking. The worker is bundled separately:

```bash
npm run build:db-worker   # outputs to .next/db/postgres-worker.mjs
```

`postgres-worker-bootstrap.mjs` is the entry point. `postgres-bootstrap.ts` handles worker startup.

## Key Schema Entities (inferred from services)

| Entity | Description |
|---|---|
| `users` | Authenticated user accounts |
| `sessions` | Auth session tokens |
| `auth_action_tokens` | One-time tokens (verify email, reset password) |
| `organizations` | Top-level tenancy unit |
| `organization_members` | User ↔ organization membership |
| `organization_invitations` | Pending invitations |
| `projects` | Bug containers within an org |
| `project_sections` | Sub-divisions of a project |
| `bugs` | Core bug records |
| `bug_comments` | Comments on bugs |
| `bug_history` | Audit trail of bug changes |
| `attachments` | File attachment metadata |
| `notifications` | Notification event records |
| `notification_deliveries` | Per-recipient delivery attempts |
| `operational_logs` | Structured app log entries |
| `operational_alerts` | Alert records from monitoring |
| `bug_data_jobs` | Import/export job records |
| `api_keys` | API key credentials |