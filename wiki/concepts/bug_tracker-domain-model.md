---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [domain, bugs, organizations, projects]
status: active
---

# Bug Tracker — Domain Model

## Core Entities

### Organization
The top-level tenant boundary. Every user, project, and bug belongs to an organization. Created during signup. Users can belong to multiple organizations and switch between them via the organization switcher.

### User
Has a name, email, and password. Can be:
- **Regular member** — participates in one or more organizations
- **Admin** — full access to admin panel, all organizations
- **QA user** — created by admins for testing purposes; can be deactivated

### Project
Scoped to an organization. Organizes bugs into logical groupings (e.g. by product area). Projects have a short `key` used for display. Projects can be archived.

### Section
An ordered sub-grouping within a project (e.g. sprint, milestone, or feature area). Bugs are assigned to a project and optionally to a section.

### Bug
The central entity. A bug belongs to an organization, project, and optionally a section.

**Status lifecycle:**
```
New → Triaged → In Progress → Ready for QA → Closed
                                            ↓
                                         (Reopened)
```

**Severity levels** (from badge component):
- Low
- Medium  
- High
- Critical

**Key fields:**
- `title` — short description
- `description` — full detail
- `severity` — impact level
- `status` — workflow state
- `reporter_user_id` — who reported it
- `assignee_user_id` — who is working it (nullable)
- `is_archived` — soft-delete flag
- `agent_state` / `claimed_by` — agent automation fields

### Bug Comment
Text attached to a bug. Has a `visibility` field: `internal` (team-only) or `external` (customer-facing).

### Bug Attachment
A file uploaded against a bug (screenshot, log file, recording).

### Bug History
Immutable audit trail. Every field change on a bug records a history entry with:
- `event_type` — e.g. `bug_created`, `bug_status_changed`, `bug_import_created`
- `changed_fields` — array of `{field, previousValue, nextValue}`
- Source: either a user action or a system/import action

## Filter Model

The bug list supports filtering by:
- Project
- Status
- Reporter user
- Date range (start/end)
- Show archived (boolean)
- Sort field: `createdAt` | `updatedAt` | `status` | `project`
- Sort direction: `asc` | `desc`

## Organization Access Model

- Users must be members of an organization to access its data.
- The active organization is tracked in the session (`active-organization-session.ts`).
- Organization admins have elevated permissions (e.g. view bug history).
- Project access can be further restricted (`project-access-context.ts`).

## Bug Data Import/Export

Bugs can be bulk-imported from CSV and exported to ZIP archives.

**Import:** validates rows, reports invalid rows with field-level issues, then executes with a configurable duplicate strategy (`skip_existing` | `update_existing`).

**Export:** produces a manifest with counts of bugs, history entries, attachments, and missing files.

Jobs are tracked in `bug_data_jobs` with statuses: `queued` → `running` → `completed` / `completed_with_warnings` / `failed`.