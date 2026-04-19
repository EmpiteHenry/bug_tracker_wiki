---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [database, postgresql, schema]
status: active
---

# Database Schema

The app supports two database backends selected via the `BUG_TRACKER_TEST_DB_BACKEND` env var:

- **`postgres`** — production backend using `pg` driver; schema defined in `src/lib/db/postgres-schema.ts`
- **`memory`** — in-memory SQLite-compatible store used for unit tests (no Docker needed)

The abstraction layer in `src/lib/db/database-client.ts` exposes a unified `DatabaseClient` interface to all services.

## Migration

Migrations run against PostgreSQL only:

```bash
npm run db:migrate:postgres
```

This executes `scripts/migrate-postgres.ts` using `.env` for connection details.

## Core Tables (inferred from services and schema)

### users
Stores workspace users. Created during signup or by admins as QA users.

| Column | Notes |
|---|---|
| id | PK |
| name | Display name |
| email | Unique, used for login |
| password_hash | Bcrypt hash |
| is_admin | Admin flag |
| is_verified | Email verified flag |
| created_at / updated_at | Timestamps |

### organizations
Top-level tenant boundary. Created at signup.

| Column | Notes |
|---|---|
| id | PK |
| name | Display name |
| slug | URL-safe unique identifier |

### organization_members
Many-to-many: users ↔ organizations with role.

### projects
Scoped to an organization.

| Column | Notes |
|---|---|
| id | PK |
| organization_id | FK → organizations |
| name, key | Identifiers |
| description | Optional |
| is_archived | Soft delete |

### project_sections
Ordered sub-groupings within a project.

| Column | Notes |
|---|---|
| id | PK |
| project_id | FK → projects |
| name | Section label |
| display_order | Integer for ordering |
| is_archived | Soft delete |

### bugs
Core entity.

| Column | Notes |
|---|---|
| id | PK |
| organization_id | FK → organizations |
| project_id | FK → projects |
| section_id | FK → project_sections (nullable) |
| title | Short description |
| description | Full detail |
| severity | Enum (see domain model) |
| status | Enum: New / Triaged / In Progress / Ready for QA / Closed |
| reporter_user_id | FK → users |
| assignee_user_id | FK → users (nullable) |
| is_archived | Boolean |
| agent_state | Agent claim state (nullable) |
| claimed_by | Agent identifier string (nullable) |
| created_at / updated_at | Timestamps |

### bug_comments
| Column | Notes |
|---|---|
| id | PK |
| bug_id | FK → bugs |
| author_user_id | FK → users |
| body | Comment text |
| visibility | `internal` / `external` |

### bug_attachments
| Column | Notes |
|---|---|
| id | PK |
| bug_id | FK → bugs |
| file_path | Path in attachment storage |
| original_filename | Original upload name |

### bug_history
Audit trail of all field changes on a bug.

| Column | Notes |
|---|---|
| id | PK |
| bug_id | FK → bugs |
| event_type | e.g. `bug_created`, `bug_status_changed`, `bug_import_created` |
| changed_by_user_id | FK → users (nullable — system events) |
| changed_fields | JSONB array of `{field, previousValue, nextValue}` |
| created_at | Timestamp |

### operational_logs
Application log sink for observability events.

| Column | Notes |
|---|---|
| id | PK |
| severity | `debug` / `info` / `warn` / `error` / `critical` |
| message | Log line |
| context | JSONB metadata |
| created_at | Timestamp |

### operational_alerts
Aggregated alerts derived from error patterns.

| Column | Notes |
|---|---|
| id | PK |
| grouping_key | Error fingerprint |
| severity | Alert severity |
| status | `open` / `acknowledged` / `resolved` |
| source | Origin system |
| latest_seen_at | Last occurrence timestamp |
| linked_bug_id | FK → bugs (nullable) |

### notifications
Tracks email notification events and deliveries.

### auth_action_tokens
Short-lived tokens for email verification and password reset.

### api_keys / auth_jwt_config
Settings for API key and JWT authentication.

### bug_data_jobs
Import/export job queue records.

| Column | Notes |
|---|---|
| id | PK |
| organization_id | FK → organizations |
| job_type | `import` / `export` |
| status | `queued` / `running` / `completed` / `completed_with_warnings` / `failed` |
| source_file_name | Original filename for imports |
| result_file_path | Output path for exports |
| summary | JSONB job summary metrics |
| warnings / errors | JSONB arrays |

## PostgreSQL Worker

Some database operations run in a separate worker thread (`src/lib/db/postgres-worker.ts`) to avoid blocking the main thread. The worker bundle is built separately:

```bash
npm run build:db-worker   # outputs to .next/db/postgres-worker.mjs
```