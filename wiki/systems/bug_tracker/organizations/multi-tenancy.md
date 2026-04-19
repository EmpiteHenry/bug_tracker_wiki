---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [organizations, multi-tenancy, membership, invitations, billing]
status: active
---

# Organizations & Multi-Tenancy

## Model

Each user belongs to one or more **Organizations**. All data (bugs, projects, settings) is scoped to an organization.

A user has one **active organization** per session, managed via `active-organization-session.ts`.

## Organization Lifecycle

1. **Creation**: Org is created during signup (`registerUserWithOrganization()`)
2. **Onboarding**: Additional setup via `/onboarding` flow
3. **Switching**: Users switch between orgs; active org stored in session

## Membership

| Service | Purpose |
|---|---|
| `organization-membership-service.ts` | Add/remove members, role management |
| `organization-access-service.ts` | Access checks: `requireOrganizationAdminAccessForUser()` |
| `organization-seat-service.ts` | Seat count enforcement |

## Invitations

Flow:
1. Admin sends invitation → `organization-invitation-service.ts` creates token
2. Email sent via `organization-invitation-email-service.ts`
3. Invitee lands on `/invitations/[invitationId]` 
4. Accepts via `organization-invitation-activation-service.ts`

API routes: `/api/invitations/*`, `/api/organizations/invitations`

## Projects Within Organizations

Each project belongs to an organization. Project access is additionally controlled at the project level.

`project-access-context.ts` provides UI content when a user lacks project access.

## Billing

`organization-billing-service.ts` manages billing state. UI in `organization-billing-panel.tsx` at `/billing`.

`billing-ui.ts` provides presentation helpers.

## Active Organization Session

`requireActiveOrganizationSession()` is used by all bug-facing API routes to ensure a valid org context. This guard appears in all `/api/bugs/*` handlers.

## Audit Log

All significant org actions are logged via `organization-audit-log-service.ts` / `organization-audit-log-store.ts`.

Accessible at `/organization/audit` in the UI.