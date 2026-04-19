---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 40
tags: [api, rest, endpoints, routes]
status: active
---

# API Endpoints Reference

All routes live under `src/app/api/`. Routes are grouped by authentication domain.

## Authentication Routes (`/api/auth/`)

| Method | Path | Description |
|---|---|---|
| POST | `/api/auth/signup` | Register new user + organization |
| POST | `/api/auth/login` | Authenticate and set session cookie |
| POST | `/api/auth/logout` | Clear session cookie |
| GET | `/api/auth/session` | Get current session info |
| POST | `/api/auth/forgot-password` | Send password reset email |
| POST | `/api/auth/reset-password` | Complete password reset with token |
| POST | `/api/auth/change-password` | Change password (authenticated) |
| POST | `/api/auth/verify-email` | Verify email address with token |

## Bug Routes (`/api/bugs/`)

| Method | Path | Description |
|---|---|---|
| GET | `/api/bugs` | List bugs (filterable by project, status, reporter, dates, sort) |
| POST | `/api/bugs` | Create a new bug |
| GET | `/api/bugs/[bugId]` | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Update bug fields (title, description, severity, status, assignee, project, section, archived) |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment to bug |
| GET | `/api/bugs/[bugId]/attachments/[attachmentId]` | Download attachment file |
| DELETE | `/api/bugs/[bugId]/attachments/[attachmentId]` | Delete attachment |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Create comment |
| GET | `/api/bugs/[bugId]/history` | List bug history timeline (admin only) |
| PATCH | `/api/bugs/bulk` | Bulk update bug status |

## Agent Routes (`/api/agent/`) — API Key Authenticated

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug for agent processing |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state and status note |

## Extension Routes (`/api/extension/`) — Bearer Token Authenticated

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Extension login (returns JWT) |
| POST | `/api/extension/auth/logout` | Extension logout |
| GET | `/api/extension/auth/session` | Get extension session |
| GET | `/api/extension/projects` | List projects for bug creation |
| POST | `/api/extension/bugs` | Create bug from extension |
| GET | `/api/extension/bugs/[bugId]/evidence` | Get evidence for bug (testing session) |
| POST | `/api/extension/bugs/[bugId]/evidence` | Upload evidence from testing session |
| GET | `/api/extension/version` | Get current extension version info |

## Admin Routes (`/api/admin/`) — Admin Session Required

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/dashboard/summary` | Dashboard summary stats |
| GET | `/api/admin/alerts` | List operational alerts |
| GET | `/api/admin/alerts/[alertId]` | Alert detail |
| PATCH | `/api/admin/alerts/[alertId]` | Update alert status |
| POST | `/api/admin/alerts/[alertId]/bugs` | Create bug from alert |
| GET | `/api/admin/errors` | Grouped error list |
| GET | `/api/admin/logs` | Paginated operational log entries |
| GET | `/api/admin/performance/endpoints` | Endpoint performance stats |
| GET | `/api/admin/performance/org-usage` | Per-org usage stats |
| GET | `/api/admin/notifications/settings` | Notification settings |
| PATCH | `/api/admin/notifications/settings` | Update notification settings |
| GET | `/api/admin/notifications/deliveries` | Notification delivery history |
| POST | `/api/admin/notifications/deliveries/[deliveryId]/retry` | Retry delivery |
| GET/POST | `/api/admin/notifications/events/[eventId]/retry` | Retry notification event |
| GET | `/api/admin/projects` | List all projects |
| POST | `/api/admin/projects` | Create project |
| PATCH | `/api/admin/projects/[projectId]` | Update project |
| GET/POST | `/api/admin/projects/[projectId]/sections` | List/create sections |
| PATCH | `/api/admin/projects/[projectId]/sections/[sectionId]` | Update section |
| GET | `/api/admin/users` | List QA users |
| POST | `/api/admin/users` | Create QA user |
| POST | `/api/admin/users/[userId]/deactivate` | Deactivate QA user |
| GET | `/api/admin/organizations-with-projects` | Org + project list |

## Other Routes

| Method | Path | Description |
|---|---|---|
| POST | `/api/observability/page-load` | Record client-side page load timing |
| GET/POST | `/api/organizations` | List/create organizations |
| GET/PATCH/DELETE | `/api/organizations/[organizationId]` | Org detail/update/delete |
| GET | `/api/organizations/active` | Get active organization |
| POST | `/api/invitations` | Create invitation |
| POST | `/api/invitations/[token]/activate` | Accept invitation |
| GET/PATCH | `/api/projects` | List/create user projects |
| GET/PATCH/DELETE | `/api/projects/[projectId]` | Project operations |
| GET/PATCH | `/api/settings/api-access` | API key and JWT settings |

## Authentication Methods

| Context | Method |
|---|---|
| Browser app | Session cookie (`bug-tracker-session`) |
| Chrome extension | Bearer token (JWT) via `Authorization` header |
| Agent API | API key authentication |