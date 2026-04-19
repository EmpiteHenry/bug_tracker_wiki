---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 30
tags: [api, rest, endpoints]
status: active
---

# API Endpoints Reference

All handlers are wrapped with `withRequestObservability` which logs request timing and errors to the operational log.

## Authentication — `/api/auth/*`

| Method | Path | Description |
|---|---|---|
| POST | `/api/auth/signup` | Register user + create organization; sets session cookie |
| POST | `/api/auth/login` | Authenticate with email/password; sets session cookie |
| POST | `/api/auth/logout` | Invalidate session cookie |
| GET | `/api/auth/session` | Return current session info |
| POST | `/api/auth/verify-email` | Consume email verification token |
| POST | `/api/auth/forgot-password` | Send password reset email |
| POST | `/api/auth/reset-password` | Consume reset token and set new password |
| POST | `/api/auth/change-password` | Change password for authenticated user |

Session is stored in a cookie (`getSessionCookieName()`). The Extension uses `Authorization: Bearer` header instead.

## Bugs — `/api/bugs/*`

| Method | Path | Description |
|---|---|---|
| GET | `/api/bugs` | List bugs (filterable by project, status, reporter, dates, sort) |
| POST | `/api/bugs` | Create a bug |
| GET | `/api/bugs/[bugId]` | Get single bug |
| PATCH | `/api/bugs/[bugId]` | Update bug fields (title, description, severity, status, assignee, project, section, archived) |
| PATCH | `/api/bugs/bulk` | Bulk update bug statuses |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment (with optional visibility) |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[attachmentId]` | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[attachmentId]` | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | List change history (org admin only) |

## Agent API — `/api/agent/*`

Authenticated via agent API key. Used by automated agents.

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug (`claimedBy` string required) |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state (`claimedBy`, `state`, `statusNote`) |

## Admin — `/api/admin/*`

All endpoints require admin role (`requireAdminUser`).

### Projects
| Method | Path | Description |
|---|---|---|
| GET/POST | `/api/admin/projects` | List / create projects |
| PATCH | `/api/admin/projects/[projectId]` | Update project (name, key, description, isArchived) |
| GET/POST | `/api/admin/projects/[projectId]/sections` | List / create sections |
| PATCH | `/api/admin/projects/[projectId]/sections/[sectionId]` | Update section |

### Users
| Method | Path | Description |
|---|---|---|
| GET/POST | `/api/admin/users` | List / create QA users |
| POST | `/api/admin/users/[userId]/deactivate` | Deactivate QA user |

### Monitoring
| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/logs` | Paginated operational logs (filterable by severity) |
| GET | `/api/admin/errors` | Grouped error fingerprints |
| PATCH | `/api/admin/errors/[fingerprint]/bug` | Link error group to a bug |
| GET | `/api/admin/alerts` | List operational alerts |
| GET/PATCH | `/api/admin/alerts/[alertId]` | Get / update alert status |
| POST | `/api/admin/alerts/[alertId]/bugs` | Create bug from alert |
| GET | `/api/admin/performance/endpoints` | Endpoint performance stats |
| GET | `/api/admin/performance/org-usage` | Per-org usage stats |
| GET | `/api/admin/dashboard/summary` | Admin dashboard summary |

### Notifications
| Method | Path | Description |
|---|---|---|
| GET/PATCH | `/api/admin/notifications/settings` | List / update notification settings |
| GET | `/api/admin/notifications/deliveries` | Delivery history |
| POST | `/api/admin/notifications/deliveries/[deliveryId]/retry` | Retry failed delivery |
| POST | `/api/admin/notifications/events/[eventId]/retry` | Retry failed event |

## Extension API — `/api/extension/*`

Separate auth flow using `Authorization: Bearer` token (not cookies).

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Login, returns token |
| POST | `/api/extension/auth/logout` | Invalidate token |
| GET | `/api/extension/auth/session` | Get current session |
| GET | `/api/extension/projects` | List projects |
| GET | `/api/extension/version` | Extension compatibility check |
| POST | `/api/extension/bugs/[bugId]/evidence` | Upload evidence during testing session |

## Observability — `/api/observability/*`

| Method | Path | Description |
|---|---|---|
| POST | `/api/observability/page-load` | Record client-side page load metrics |

## Error Response Pattern

API errors follow this pattern based on the service error class thrown:

| Error Class | HTTP Status |
|---|---|
| `AuthenticationError` | 401 |
| `ActiveOrganizationSessionError` | 403 |
| `BugServiceError` | 400 / 404 |
| `ProjectServiceError` | 400 / 404 |
| `OperationalAlertServiceError` | 400 / 404 |
| Unhandled | 500 |