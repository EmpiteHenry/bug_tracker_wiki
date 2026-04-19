---
type: concept
owner: engineering
last_updated: 2026-04-19
source_count: 8
tags: [domain, model, bugs, organizations, projects]
status: active
---

# Domain Model

## Core Entities

### Organization
Top-level tenancy boundary. All bugs, projects, and users are scoped to an organization. A user can belong to multiple organizations and switches between them via the organization switcher UI.

### Project
A container for bugs within an organization. Projects have a short key (e.g. `PROJ`) and can be divided into **Sections** for further grouping. Projects can be archived.

### Bug
The central entity. Key fields:
- **title** / **description** — human-readable content
- **severity** — e.g. low, medium, high, critical
- **status** — `New | Triaged | In Progress | Ready for QA | Closed`
- **reporter** / **assignee** — user references
- **project** + **section** — location within project hierarchy
- **is_archived** — soft deletion flag
- **agent state** — claim tracking for AI agent workflows

### Bug Comment
Free-text comments on a bug with optional **visibility** control (e.g. internal vs. public).

### Bug History
Immutable audit trail of changes to a bug. Each entry records the `eventType`, which user/source made the change, and a list of `changedFields` with `previousValue` / `nextValue`. Event types starting with `bug_import_` indicate bulk import origins; those starting with `bug_` indicate user actions; others are system-generated.

### Attachment
A file linked to a bug, stored on disk. Metadata (filename, path, size) is stored in the database.

### Testing Session
A structured QA session within which bugs can be captured. The extension API validates an active testing session before accepting evidence uploads.

## User Roles

| Role | Scope | Capabilities |
|---|---|---|
| Regular user | Organization | View/create/edit bugs in accessible projects |
| Organization admin | Organization | All user capabilities + manage members, settings |
| Admin (`requireAdminUser`) | Global | Full access to admin panel, all orgs/users |
| Agent | API | Claim bugs, update agent status via API key |

## Multi-Tenancy Pattern

1. User authenticates → session cookie set
2. Active organization resolved from session → `requireActiveOrganizationSession()`
3. Bug/project queries scoped to active organization's ID
4. Project-level membership further restricts which projects a user can see

## Bug Lifecycle

```
New → Triaged → In Progress → Ready for QA → Closed
                                             ↓
                                          Reopened
```

Agents can claim a bug (recording `claimedBy`) and update `state` + `statusNote` independently of the main status field.

## Import/Export

Bugs can be bulk-imported from CSV/file via an async job system (`BugDataManagementJob`). Jobs are queued, run asynchronously, and support `skip_existing` or `update_existing` duplicate strategies. Exports produce a ZIP-style archive with bugs, history, and attachments.