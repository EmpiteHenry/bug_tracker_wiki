---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [domain, model, bugs, organizations, projects]
status: active
---

# Domain Model

## Entity Hierarchy

```
User
└── belongs to one or more Organizations (via Membership)
    └── Organization
        ├── has many Projects
        │   ├── has many Sections
        │   └── has many Bugs (scoped to project + section)
        │       ├── has many Comments
        │       ├── has many Attachments
        │       ├── has many HistoryEntries
        │       └── has TestingSessions (QA evidence)
        ├── has AuditLog
        ├── has NotificationSettings
        └── has Billing state
```

## Bug Fields

| Field | Type | Notes |
|---|---|---|
| `title` | string | Required |
| `description` | string | Required |
| `severity` | string | e.g. critical, high, medium, low |
| `status` | enum | New, Triaged, In Progress, Ready for QA, Closed |
| `reporterUserId` | number? | Who filed it |
| `assigneeUserId` | number? | Who owns it |
| `projectId` | number | Required |
| `sectionId` | number | Required |
| `is_archived` | boolean | Soft-delete |

## Organization Membership Roles

Roles control access to admin surfaces and project membership. `requireOrganizationAdminAccessForUser()` gates admin-only operations.

## Active Organization Session

A user can belong to multiple organizations. The "active" org is stored in session state. All bug/project queries are scoped to the active org.

## Project Sections

Projects have ordered sections (like columns in a board or phases in a pipeline). Sections have `displayOrder` and `isArchived`.

## Operational Alerts vs Bugs

**Operational Alerts** come from the monitoring system (log-derived, auto-detected).  
**Bugs** are human or agent-submitted issue reports.  
Alerts can be promoted to bugs via `createBugFromOperationalAlert()`.