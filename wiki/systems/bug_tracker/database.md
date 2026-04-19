---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 7
tags: [database, postgresql, schema, migrations]
status: active
---

# Database

## Backends

The app supports two database backends, selected via the `BUG_TRACKER_TEST_DB_BACKEND` environment variable:

| Backend | When used |
|---|---|
| PostgreSQL | Production and `test:unit:postgres` suite |
| In-memory | Default test suite (`npm run test`) |

`src/lib/db/database-client.ts` abstracts the backend. `src/lib/db/index.ts` exports `getDatabase()`.

## PostgreSQL Setup

- Config: `postgres-config.ts` reads `DATABASE_URL` or individual `POSTGRES_*` env vars from `.env`.
- Client: `postgres-client.ts` wraps the `pg` npm package.
- Schema DDL: `postgres-schema.ts`; test helpers in `postgres-schema.test.ts`.
- Migrations: applied via `npm run db:migrate:postgres` (runs `scripts/migrate-postgres.ts`).
- Bootstrap: `postgres-bootstrap.ts` handles first-run setup; `bootstrap-admin.ts` seeds the initial admin account.

## PostgreSQL Worker

Certain heavy operations run in a separate worker thread to avoid blocking the Next.js server:

- Worker source: `src/lib/db/postgres-worker.ts`
- Built to: `.next/db/postgres-worker.mjs` via esbuild
- Bootstrap script: `postgres-worker-bootstrap.mjs`

The worker is built automatically before `dev` and `build` scripts via the `predev` npm hook.

## Schema

Core tables (inferred from services and stores):

| Table | Key fields |
|---|---|
| `users` | id, name, email, password_hash, role, is_verified |
| `sessions` | token, user_id, expires_at |
| `auth_action_tokens` | token, type, user_id, expires_at |
| `organizations` | id, name, slug, billing_plan |
| `organization_members` | org_id, user_id, role |
| `organization_invitations` | token, org_id, email, status |
| `projects` | id, org_id, name, key, is_archived |
| `project_sections` | id, project_id, name, display_order |
| `bugs` | id, org_id, project_id, section_id, title, description, severity, status, reporter_user_id, assignee_user_id, is_archived |
| `bug_comments` | id, bug_id, user_id, body, visibility |
| `bug_attachments` | id, bug_id, file_path, file_name |
| `bug_history` | id, bug_id, event_type, changed_fields (json), created_by |
| `bug_data_jobs` | id, org_id, job_type, status, summary (json) |
| `operational_logs` | id, severity, message, context (json), fingerprint |
| `operational_alerts` | id, severity, status, source, grouping_key |
| `notifications` | id, event_type, recipient_id, delivery_status |

Full canonical schema: `src/lib/db/schema.ts` and `postgres-schema.ts`.

## Local Docker Setup

```bash
docker compose up -d postgres   # starts postgres on 127.0.0.1:5432
npm run db:migrate:postgres     # applies migrations
```

Credentials default to values in `.env.example`.