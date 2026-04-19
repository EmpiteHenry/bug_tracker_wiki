---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [domain-model, bugs, organizations, projects, glossary]
status: active
---

# Domain Model

## Entity Hierarchy

```
Organization
  └── has many Users (via Membership)
  └── has many Projects
       └── has many ProjectSections
            └── has many Bugs
                 ├── has many Comments
                 ├── has many Attachments
                 └── has many HistoryEntries
```

## Core Entities

### Organization
The top-level tenant. All data is scoped to an org. Users switch between orgs via the org switcher. Created during signup.

### Project
Belongs to an org. Groups related bugs. Has **sections** (columns/phases) for organizing bugs within a project.

### Bug
The primary work item. Key attributes:
- **Status lifecycle**: `New` → `Triaged` → `In Progress` → `Ready for QA` → `Closed`
- **Severity**: `low` | `medium` | `high` | `critical`
- **Archived**: soft-deleted bugs hidden by default

### BugHistory
Immutable audit trail for a bug. Each entry records `eventType` and `changedFields` with `previousValue` / `nextValue`. Sources: user action, import, system.

### TestingSession
A bounded QA session where evidence (screenshots, logs) is captured and linked to bugs. Created by the extension or agent during testing.

### OperationalAlert
A monitoring signal that may require human action. Can be linked to a bug. Has a status lifecycle (open → acknowledged → resolved).

## User Roles

| Role | Access |
|---|---|
| Platform Admin | All `/api/admin/*` routes, full system access |
| Org Admin | Organization management, member access control |
| Member | Bug CRUD within assigned projects |
| Agent | Machine-to-machine via `/api/agent/*` (API key) |

## Key Status Enumerations

**Bug statuses** (ordered): `New`, `Triaged`, `In Progress`, `Ready for QA`, `Closed`

**Bug data job statuses**: `queued` → `running` → `completed` | `completed_with_warnings` | `failed`

**Alert statuses**: open / acknowledged / resolved (managed via `OperationalAlertStatus` type)

**Notification delivery statuses**: tracked in `notification-store.ts`