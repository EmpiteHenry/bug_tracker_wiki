---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [domain-model, organizations, projects, bugs, multi-tenant]
status: active
---

# Domain Model

## Core Entities

```
Organization
  └── Projects
        └── Sections
              └── Bugs
                    ├── Comments
                    ├── Attachments
                    └── History Events
```

### Organization

The top-level tenant. Every user belongs to one or more organizations. All data (bugs, projects, notifications) is scoped to an organization. Users can switch between organizations they belong to.

### Project

A named collection of bugs within an organization. Projects have a `key` (short identifier), optional description, and an `isArchived` flag. Projects contain **Sections** for grouping bugs (e.g., "Frontend", "Backend", "Infrastructure").

### Bug

The central entity. Fields:

| Field | Type | Notes |
|---|---|---|
| `title` | string | Short description |
| `description` | string | Full details |
| `severity` | enum | e.g. Low, Medium, High, Critical |
| `status` | enum | New → Triaged → In Progress → Ready for QA → Closed |
| `reporterUserId` | int? | Who reported it |
| `assigneeUserId` | int? | Who is working on it |
| `projectId` | int | Which project |
| `sectionId` | int | Which section |
| `is_archived` | bool | Hidden from default views |

### User

Users are members of organizations and optionally of specific projects. `isAdmin` is a boolean on the user record granting access to admin surfaces.

## Relationships

- User ↔ Organization: many-to-many via `organization_members`
- User ↔ Project: many-to-many via `project_members`
- Bug → Project → Organization: strict ownership chain
- Bug → Assignee (User): nullable FK
- Bug → Reporter (User): nullable FK

## Bug Visibility Rules

A user can see a bug if:
1. They have an active organization session for the bug's organization, AND
2. They are a member of the bug's project (or are an org admin)

The `requireActiveOrganizationSession()` guard enforces the organization boundary. Project membership is checked at the service layer.

## Active Organization Session

The "active organization" is the org currently selected by the user. It is stored in the session and resolved on each request. If a user belongs to multiple orgs, they must explicitly switch to see another org's data.

## Agent Identity

Agents (automated processes) interact via API key, not user sessions. They appear in bug history as agent actions rather than user actions. The `claimedBy` field on a bug records the agent identifier string.