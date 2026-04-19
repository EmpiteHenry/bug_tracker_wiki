---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 35
tags: [api, rest, endpoints, reference]
status: active
---

# API Endpoints Reference

All routes wrap handlers with `withRequestObservability(method, handler)` for structured logging.

## Auth (`/api/auth/*`)

| Method | Path | Auth Required | Description |
|---|---|---|---|
| POST | `/api/auth/signup` | None | Register user + create org |
| POST | `/api/auth/login` | None | Authenticate, set session cookie |
| POST | `/api/auth/logout` | Session | Clear session |
| GET | `/api/auth/session` | Session | Get current session |
| POST | `/api/auth/forgot-password` | None | Send reset email |
| POST | `/api/auth/reset-password` | None | Complete reset with token |
| POST | `/api/auth/change-password` | Session | Change own password |
| POST | `/api/auth/verify-email` | None | Verify email token |

## Bugs (`/api/bugs/*`)

| Method | Path | Auth Required | Description |
|---|---|---|---|
| GET | `/api/bugs` | Org session | List bugs with filters |
| POST | `/api/bugs` | Org session | Create bug |
| GET | `/api/bugs/[bugId]` | Org session | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Org session | Update bug |
| PATCH | `/api/bugs/bulk` | Org session | Bulk status update |
| GET | `/api/bugs/[bugId]/comments` | Org session | List comments |
| POST | `/api/bugs/[bugId]/comments` | Org session | Add comment |
| POST | `/api/bugs/[bugId]/attachments` | Org session | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[id]` | Org session | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[id]` | Org session | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | Org admin | Audit history timeline |

## Agent API (`/api/agent/*`)

Machine-to-machine interface using API key auth (`requireAgentApiUser`).

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim bug `{ claimedBy }` |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work `{ claimedBy, state, statusNote }` |

## Extension (`/api/extension/*`)

Separate auth surface for Chrome extension (Bearer token, not cookie).

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Login, returns token |
| POST | `/api/extension/auth/logout` | Logout |
| GET | `/api/extension/auth/session` | Get session |
| GET | `/api/extension/projects` | List projects |
| POST | `/api/extension/bugs` | Submit bug from extension |
| GET/POST | `/api/extension/bugs/[bugId]/evidence` | Attach evidence during testing session |
| GET | `/api/extension/version` | Extension version check |

## Admin (`/api/admin/*`)

Requires `requireAdminUser()` (platform admin role).

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/dashboard/summary` | Dashboard metrics |
| GET | `/api/admin/projects` | List all projects |
| POST | `/api/admin/projects` | Create project |
| PATCH | `/api/admin/projects/[id]` | Update project |
| GET/POST | `/api/admin/projects/[id]/sections` | Project sections |
| PATCH | `/api/admin/projects/[id]/sections/[id]` | Update section |
| GET | `/api/admin/users` | List QA users |
| POST | `/api/admin/users` | Create QA user |
| POST | `/api/admin/users/[id]/deactivate` | Deactivate user |
| GET | `/api/admin/alerts` | List operational alerts |
| GET/PATCH | `/api/admin/alerts/[id]` | Alert detail / status update |
| POST | `/api/admin/alerts/[id]/bugs` | Create bug from alert |
| GET | `/api/admin/errors` | Grouped errors |
| PATCH | `/api/admin/errors/[fingerprint]/bug` | Link error to bug |
| GET | `/api/admin/logs` | Operational logs |
| GET | `/api/admin/performance/endpoints` | Endpoint performance |
| GET | `/api/admin/performance/org-usage` | Org usage stats |
| GET | `/api/admin/notifications/deliveries` | Notification delivery history |
| POST | `/api/admin/notifications/deliveries/[id]/retry` | Retry delivery |
| GET/PATCH | `/api/admin/notifications/settings` | Notification settings |
| POST | `/api/admin/notifications/events/[id]/retry` | Retry notification event |
| GET | `/api/admin/organizations-with-projects` | Org/project join view |

## Observability

| Method | Path | Description |
|---|---|---|
| POST | `/api/observability/page-load` | Report client page load timing |

## OpenAPI Spec

Available at `/public/openapi.json` and rendered in the UI at `/settings/api-docs`.