---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [domain, model, organizations, projects, bugs]
status: active
---

# Domain Model

## Entity Hierarchy

```
Organization
  └── Project
        └── Section (optional)
              └── Bug
                    ├── Comment
                    ├── Attachment
                    └── History Entry
```

## Organizations

The top-level multi-tenancy unit. Every user belongs to at least one organization. Users switch between organizations via the organization switcher.

- Managed by: `organization-service.ts`, `organization-membership-service.ts`
- Active organization is stored in the session: `active-organization-session.ts`
- Organizations have billing plans tracked by `organization-billing-service.ts` and seat limits enforced by `organization-seat-service.ts`

## Projects

A workspace for grouping related bugs. Each project has:
- A **name** and short **key** (identifier prefix)
- Optional **description**
- **Archive** flag for retired projects
- **Sections** — ordered sub-groups within a project (e.g., "Frontend", "Backend")

User access to projects is controlled separately from org membership via `project-membership-service.ts`.

## Bugs

The core entity. Key relationships:
- Must belong to a **Project** and optionally a **Section**
- Has a **reporter** (the user who filed it) and optional **assignee**
- Tracks **severity** and **status** (see [Bug Management](../systems/bug_tracker/bugs/bug-management.md))
- Accumulates **comments**, **attachments**, and a **history** audit trail

## Users

Users authenticate with email/password. Roles:
- **Admin** — full platform access including all admin routes
- **Member** — normal workspace user
- **QA User** — created by admins with temporary passwords for testing purposes

## Invitations

Organizations invite new members via email. The invitation flow:
1. Admin creates invitation → `organization-invitation-service.ts` → sends email
2. Recipient clicks link → `/invitations/[invitationId]`
3. Activation via `organization-invitation-activation-service.ts`

## Audit Log

Organization-level actions are recorded by `organization-audit-log-service.ts` for compliance and debugging.

## Billing

`organization-billing-service.ts` and `billing-ui.ts` manage plan state. The billing UI is at `/billing`.