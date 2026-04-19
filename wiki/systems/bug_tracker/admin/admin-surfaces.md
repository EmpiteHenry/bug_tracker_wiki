---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [admin, projects, users, monitoring, notifications]
status: active
---

# Admin Surfaces

Admin pages live under `src/app/(authenticated)/admin/` and are protected by `requireAdminUser`. All admin API routes require admin role.

## Navigation

`src/app/(authenticated)/admin/admin-nav.tsx` renders the admin sidebar navigation.

## Admin Areas

### Projects (`/admin/projects`)

- List all projects across all organizations
- Create new projects (`POST /api/admin/projects`)
- Update project fields: name, key, description, archive status
- Manage project sections (create, update, reorder, archive)

### Users (`/admin/users`)

- List QA users (`GET /api/admin/users`)
- Create QA user accounts with temporary passwords
- Deactivate users (`POST /api/admin/users/[userId]/deactivate`)

### Dashboard (`/admin/dashboard`)

- Summary metrics from `dashboard-summary-service.ts`
- Bug counts, user counts, recent alert counts

### Monitoring (`/admin/monitoring`)

Three sub-sections:

**Logs** — Paginated operational log viewer with severity filter (`debug`, `info`, `warn`, `error`, `critical`).

**Errors** — Grouped error fingerprints from structured logs. Each group shows recurrence count and can be linked to a bug and project via the UI.

**Alerts** — Operational alerts with filters:
- Severity, status, source
- Date range (`latestSeenFrom` / `latestSeenTo` as datetime-local inputs)
- Limit

Alert filter state and URL serialization: `alert-list-filters.ts` → `buildMonitoringAlertSearchParams`.

### Notifications (`/admin/notifications`)

- View and toggle per-event, per-recipient-group notification settings
- Browse delivery history with filters: event type, delivery status, recipient email
- Retry failed deliveries or entire notification events

Filter state: `notification-center-filters.ts` → `buildNotificationDeliverySearchParams`.

### Bug Data (`/admin/bug-data`)

See [Bug Data Import/Export](../bugs/data-import-export.md).

### Organizations with Projects

`GET /api/admin/organizations-with-projects` returns a flat join used to populate admin dropdowns.