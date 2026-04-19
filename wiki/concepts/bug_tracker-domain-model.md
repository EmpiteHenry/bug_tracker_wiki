---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [domain, data-model, entities]
status: active
---

# Domain Model

## Entity Hierarchy

```
Organization
├── has many Members (Users with roles)
├── has many Projects
│   └── has many Sections
│       └── has many Bugs
│           ├── has many Comments
│           ├── has many Attachments
│           └── has many History Entries
├── has many Invitations
├── has one Billing plan
└── has many Audit Log entries
```

## Key Entities

### Bug

The central entity. Fields: title, description, severity, status, projectId, sectionId, reporterUserId, assigneeUserId, isArchived.

Severity and status are string-typed enums enforced by service-layer validation, not DB constraints.

### Organization

Top-level tenant boundary. All bug data is scoped to an org. Users switch orgs via the org switcher; the active org is stored in the session.

### Project / Section

Projects group bugs by product area. Sections provide ordering within a project (analogous to columns in a kanban board).

### Bug History Entry

Immutable audit trail. Each entry records `eventType` and a list of `changedFields` with `previousValue` / `nextValue`. Source is classified as user, import, or system.

### Testing Session

Represents an active QA session. While a testing session is open, the Chrome extension can attach evidence (screenshots, recordings) to bugs through the extension evidence API.

### Operational Alert

A monitored-infrastructure concern (distinct from bugs). Alerts have a `groupingKey` for deduplication and can be promoted to bugs via the admin panel.

## Status Lifecycle

```
New → Triaged → In Progress → Ready for QA → Closed
                                                ↑
                                          (can reopen)
```

`isArchived` is orthogonal to status — a bug can be archived at any status.