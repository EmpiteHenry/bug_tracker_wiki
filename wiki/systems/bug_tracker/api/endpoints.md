---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 35
tags: [api, rest, next.js, route-handlers]
status: active
---

# API Endpoints

All API routes live under `src/app/api/` and are Next.js App Router route handlers. Every handler is wrapped with `withRequestObservability` which records request timing and errors to the operational log.

## Auth (`/api/auth/`)

| Method | Path | Description |
|---|---|---|
| POST | `/api/auth/signup` | Register user + create organization; sets session cookie |
| POST | `/api/auth/login` | Authenticate user; sets session cookie |
| POST | `/api/auth/logout` | Delete session; clears cookie |
| GET  | `/api/auth/session` | Return current session user |
| POST | `/api/auth/verify-email` | Confirm email token |
| POST | `/api/auth/forgot-password` | Send reset link |
| POST | `/api/auth/reset-password` | Complete reset via token |
| POST | `/api/auth/change-password` | Change password for authenticated user |

## Bugs (`/api/bugs/`)

| Method | Path | Description |
|---|---|---|
| GET  | `/api/bugs` | List bugs (filterable: projectId, status, reporter, dates, sort) |
| POST | `/api/bugs` | Create bug |
| GET  | `/api/bugs/[bugId]` | Get single bug |
| PATCH | `/api/bugs/[bugId]` | Update bug fields |
| PATCH | `/api/bugs/bulk` | Bulk-update bug statuses |
| GET  | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment |
| GET  | `/api/bugs/[bugId]/attachments/[attachmentId]` | Download attachment |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[attachmentId]` | Delete attachment |
| GET  | `/api/bugs/[bugId]/history` | Bug change timeline (admin only) |

## Agent API (`/api/agent/bugs/`)

Authenticated via API key. Used by automated agents.

| Method | Path | Description |
|---|---|---|
| GET  | `/api/agent/bugs` | List claimable bugs |
| GET  | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug (`claimedBy`) |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state/note |

## Admin (`/api/admin/`)

Requires admin session.

| Method | Path | Description |
|---|---|---|
| GET/POST | `/api/admin/projects` | List / create projects |
| PATCH | `/api/admin/projects/[projectId]` | Update project |
| GET/POST | `/api/admin/projects/[projectId]/sections` | List / create sections |
| PATCH | `/api/admin/projects/[projectId]/sections/[sectionId]` | Update section |
| GET/POST | `/api/admin/users` | List / create QA users |
| POST | `/api/admin/users/[userId]/deactivate` | Deactivate user |
| GET  | `/api/admin/dashboard/summary` | Dashboard metrics |
| GET  | `/api/admin/logs` | Operational log entries (paginated) |
| GET  | `/api/admin/errors` | Grouped error fingerprints |
| PATCH | `/api/admin/errors/[fingerprint]/bug` | Link error group to a bug |
| GET  | `/api/admin/alerts` | List operational alerts |
| GET/PATCH | `/api/admin/alerts/[alertId]` | Get / update alert status |
| POST | `/api/admin/alerts/[alertId]/bugs` | Create bug from alert |
| GET  | `/api/admin/performance/endpoints` | Endpoint latency stats |
| GET  | `/api/admin/performance/org-usage` | Per-org usage stats |
| GET  | `/api/admin/notifications/deliveries` | Notification delivery history |
| POST | `/api/admin/notifications/deliveries/[id]/retry` | Retry delivery |
| POST | `/api/admin/notifications/events/[id]/retry` | Retry notification event |
| GET/PATCH | `/api/admin/notifications/settings` | Get / update notification settings |
| GET  | `/api/admin/organizations-with-projects` | Org + project list for admin |

## Extension (`/api/extension/`)

Used by the Chrome extension; auth via Bearer token in header.

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Authenticate from extension |
| POST | `/api/extension/auth/logout` | Deauthenticate |
| GET  | `/api/extension/auth/session` | Current session |
| GET  | `/api/extension/projects` | List projects for extension |
| GET/POST | `/api/extension/bugs` | List / submit bugs |
| POST | `/api/extension/bugs/[bugId]/evidence` | Upload screenshot evidence |
| GET  | `/api/extension/version` | Extension version check |

## Organizations & Invitations

| Method | Path | Description |
|---|---|---|
| GET/POST | `/api/organizations` | List / create organizations |
| GET  | `/api/organizations/active` | Active organization for session |
| GET/POST | `/api/organizations/invitations` | List / create invitations |
| GET  | `/api/organizations/[id]` | Get organization |
| GET/POST | `/api/projects` | List / create projects (user-scoped) |
| GET/POST | `/api/invitations/activate` | Activate invitation |
| GET  | `/api/invitations/[token]` | Get invitation by token |

## Settings

| Method | Path | Description |
|---|---|---|
| GET/POST/DELETE | `/api/settings/api-access` | Manage API keys and JWT config |

## Observability

| Method | Path | Description |
|---|---|---|
| POST | `/api/observability/page-load` | Client-side page load timing |

## Request Validation Pattern

All handlers validate request bodies before touching the database. Shape checks use plain TypeScript `typeof` guards — no Zod at the boundary. Invalid requests return `400`. Auth failures return `401`. Service-layer errors (typed error classes) map to `400`/`404`/`409`.