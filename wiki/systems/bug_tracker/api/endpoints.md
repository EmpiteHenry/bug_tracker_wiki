---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 35
tags: [api, rest, nextjs, routes]
status: active
---

# API Endpoints

All routes live under `src/app/api/`. Every handler is wrapped with `withRequestObservability` which records request metrics and structured logs.

## Authentication (`/api/auth/`)

| Method | Path | Purpose |
|---|---|---|
| POST | `/api/auth/signup` | Register user + create organization; sets session cookie |
| POST | `/api/auth/login` | Authenticate; sets session cookie |
| POST | `/api/auth/logout` | Clear session cookie and delete session row |
| GET | `/api/auth/session` | Return current session info (used by the client) |
| POST | `/api/auth/verify-email` | Consume email verification token |
| POST | `/api/auth/forgot-password` | Trigger forgot-password email |
| POST | `/api/auth/reset-password` | Consume reset token, set new password |
| POST | `/api/auth/change-password` | Change password for authenticated user |

## Bugs (`/api/bugs/`)

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/bugs` | List bugs with filters (project, status, reporter, dates, sort) |
| POST | `/api/bugs` | Create a new bug |
| GET | `/api/bugs/[bugId]` | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Update bug fields (title, status, severity, assignee, project…) |
| PATCH | `/api/bugs/bulk` | Bulk status update for an array of bug IDs |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[attachmentId]` | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[attachmentId]` | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | Bug change timeline (admin only) |

## Agent API (`/api/agent/`) — Headless/Automation

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug for agent consumption |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug (`claimedBy` string) |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state and note |

Agent routes use Bearer token auth (`requireAgentApiUser`), not session cookies.

## Admin (`/api/admin/`) — Admin-Only

| Method | Path | Purpose |
|---|---|---|
| GET/POST | `/api/admin/projects` | List / create projects |
| PATCH | `/api/admin/projects/[projectId]` | Update project |
| GET/POST | `/api/admin/projects/[projectId]/sections` | List / create sections |
| PATCH | `/api/admin/projects/[projectId]/sections/[sectionId]` | Update section |
| GET/POST | `/api/admin/users` | List / create QA users |
| POST | `/api/admin/users/[userId]/deactivate` | Deactivate a QA user |
| GET | `/api/admin/dashboard/summary` | Admin dashboard metrics |
| GET | `/api/admin/logs` | Paginated operational logs |
| GET | `/api/admin/errors` | Grouped error fingerprints |
| PATCH | `/api/admin/errors/[fingerprint]/bug` | Link error group to a bug / project |
| GET | `/api/admin/alerts` | List operational alerts |
| GET/PATCH | `/api/admin/alerts/[alertId]` | Get or update alert status |
| POST | `/api/admin/alerts/[alertId]/bugs` | Create bug from alert |
| GET | `/api/admin/performance/endpoints` | Endpoint performance stats |
| GET | `/api/admin/performance/org-usage` | Per-org usage stats |
| GET/PATCH | `/api/admin/notifications/settings` | Notification settings |
| GET | `/api/admin/notifications/deliveries` | Delivery history |
| POST | `/api/admin/notifications/deliveries/[id]/retry` | Retry delivery |
| POST | `/api/admin/notifications/events/[id]/retry` | Retry notification event |
| GET | `/api/admin/organizations-with-projects` | Org + project list |

## Extension (`/api/extension/`)

| Method | Path | Purpose |
|---|---|---|
| POST | `/api/extension/auth/login` | Extension login (returns token) |
| POST | `/api/extension/auth/logout` | Extension logout |
| GET | `/api/extension/auth/session` | Check extension session |
| GET | `/api/extension/projects` | Projects list for extension picker |
| POST | `/api/extension/bugs` | Submit bug from extension |
| GET | `/api/extension/version` | Version compatibility check |

## Other

| Method | Path | Purpose |
|---|---|---|
| POST | `/api/observability/page-load` | Ingest client-side page load metrics |
| POST | `/api/onboarding/organizations` | Create organization during onboarding |
| GET/POST | `/api/organizations` | List / create organizations |
| POST | `/api/invitations/activate` | Accept an org invitation |
| GET | `/api/settings/api-access` | API key settings |

## OpenAPI

A static spec is served from `public/openapi.json` and rendered at `/settings/api-docs`.