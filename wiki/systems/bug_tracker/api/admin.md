---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 18
tags: [api, admin, monitoring, users, projects]
status: active
---

# Admin API

All admin endpoints are under `/api/admin/` and require the user to pass `requireAdminUser()`. Non-admins receive 403.

## User Management

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/users` | List QA/admin users |
| POST | `/api/admin/users` | Create a QA user (`{ name, email, temporaryPassword }`) |
| POST | `/api/admin/users/[userId]/deactivate` | Deactivate a user |

## Project Management

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/projects` | List all projects |
| POST | `/api/admin/projects` | Create project (`{ name, key, description? }`) |
| PATCH | `/api/admin/projects/[projectId]` | Update project fields |
| GET | `/api/admin/projects/[projectId]/sections` | List sections |
| POST | `/api/admin/projects/[projectId]/sections` | Create section |
| PATCH | `/api/admin/projects/[projectId]/sections/[sectionId]` | Update section |

## Operational Alerts

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/alerts` | List alerts with filters (severity, status, source, date range, limit) |
| GET | `/api/admin/alerts/[alertId]` | Alert detail |
| PATCH | `/api/admin/alerts/[alertId]` | Update alert status |
| POST | `/api/admin/alerts/[alertId]/bugs` | Convert alert to a bug |

## Error Monitoring

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/errors` | Grouped error list (severity: error/critical) |
| PATCH | `/api/admin/errors/[fingerprint]/bug` | Link grouped error to a bug/project |

## Logs

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/logs` | Paginated operational log entries (debug/info/warn/error/critical) |

## Performance

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/performance/endpoints` | Endpoint latency stats by time window |
| GET | `/api/admin/performance/org-usage` | Per-org usage statistics |

## Notifications

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/notifications/settings` | List notification settings |
| PATCH | `/api/admin/notifications/settings` | Update settings (enable/disable by eventType+recipientGroup) |
| GET | `/api/admin/notifications/deliveries` | Delivery history |
| POST | `/api/admin/notifications/deliveries/[deliveryId]/retry` | Retry a failed delivery |
| POST | `/api/admin/notifications/events/[eventId]/retry` | Retry a notification event |

## Dashboard

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/dashboard/summary` | Aggregate stats for admin dashboard |
| GET | `/api/admin/organizations-with-projects` | Flat join of orgs + projects for dropdowns |

## Bug Data Import/Export

Admin surfaces for bulk import/export are in `src/app/(authenticated)/admin/bug-data/`. Jobs have statuses: `queued → running → completed | completed_with_warnings | failed`.

See `src/lib/bugs/bug-data-import-service.ts` and `bug-data-export-service.ts`.