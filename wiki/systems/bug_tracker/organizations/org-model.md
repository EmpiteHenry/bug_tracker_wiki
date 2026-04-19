---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [organizations, multi-tenant, membership, billing, invitations]
status: active
---

# Organizations & Multi-Tenancy

## Model

```
Organization
  └── Members (users with roles)
  └── Projects
        └── Sections
        └── Bugs
```

Every authenticated action operates within an active organization context, resolved via `requireActiveOrganizationSession()` (stored in `active-organization-session.ts`).

## Key Services

| Service | Purpose |
|---|---|
| `organization-service.ts` | Create/read organizations |
| `organization-membership-service.ts` | Add/remove/list members |
| `organization-access-service.ts` | Role-based access checks; `requireOrganizationAdminAccessForUser()` |
| `organization-invitation-service.ts` | Create and validate invitations |
| `organization-invitation-activation-service.ts` | Accept invitation and join org |
| `organization-invitation-email-service.ts` | Send invitation emails |
| `active-organization-service.ts` | Get/set user's active org |
| `organization-seat-service.ts` | Seat limit enforcement |
| `organization-billing-service.ts` | Billing state per org |

## Invitation Flow

1. Admin creates invitation → `POST /api/organizations/invitations`
2. Invitee receives email with token
3. Invitee visits `/invitations/[invitationId]`
4. `POST /api/invitations/activate` — activates invitation and creates membership

## Billing

`billing-ui.ts` — billing state helpers for the UI.  
`organization-billing-panel.tsx` — billing management page at `/billing`.

## Org Switcher

`organization-switcher.tsx` — UI component for switching active organization. Active org persisted in session.

## Audit Log

`src/lib/audit/organization-audit-log-service.ts` + `organization-audit-log-store.ts` — records admin-level actions within an org.  
Accessible at `/organization/audit`.

## Access Context

`project-access-context.ts` — generates messaging for users who lack project access.