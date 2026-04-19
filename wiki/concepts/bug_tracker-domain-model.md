---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [domain, data-model, bugs, organizations, projects]
status: active
---

# Domain Model

## Core Entities

```
Organization
    │
    ├── Members (Users with roles)
    ├── Projects
    │       └── Sections
    │               └── Bugs
    │                     ├── Comments
    │                     ├── Attachments
    │                     └── History entries
    │
    ├── Invitations
    └── Notification Settings
```

## Bug

Central entity. Key fields:

| Field | Type | Notes |
|---|---|---|
| `title` | string | Required |
| `description` | string | Required |
| `severity` | enum | e.g. `low`, `medium`, `high`, `critical` |
| `status` | enum | `New`, `Triaged`, `In Progress`, `Ready for QA`, `Closed` |
| `projectId` | FK | Required |
| `sectionId` | FK | Required |
| `reporterUserId` | FK | Optional |
| `assigneeUserId` | FK | Optional |
| `is_archived` | boolean | Soft archive |

Agent-specific fields: `claimedBy` (string agent name), agent `state`, `statusNote`.

## Organization

Top-level tenant. Has a `slug` (URL-friendly identifier), `name`, billing state, and seat count.

## Project

Belongs to an organization. Has a `key` (short code), `name`, `description`, and `isArchived` flag. Contains ordered **sections**.

## User

Global (not org-scoped). Users join organizations via membership records. Roles are per-membership. QA users are a distinct user type managed by admins.

## Testing Session

A structured QA session tied to a user. Evidence uploads via the Chrome extension require an active testing session. Logs are recorded per session in `testing_session_logs`.

## Operational Alert

A system-generated alert (not a user-filed bug). Admins can acknowledge/resolve alerts or promote them to bugs via `POST /api/admin/alerts/[id]/bugs`.
