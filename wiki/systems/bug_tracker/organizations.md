---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [organizations, multi-tenancy, billing, invitations, membership]
status: active
---

# Organizations & Multi-Tenancy

## Model

Every authenticated user belongs to one or more **organizations**. Within an organization, users are members with roles. Projects and bugs are scoped to an organization.

The active organization for a request is resolved by `active-organization-service.ts` and stored in the session via `active-organization-session.ts`. All authenticated API routes call `requireActiveOrganizationSession` to enforce this scope.

## Invitation Flow

1. Admin sends invitation → `organization-invitation-service.ts` creates a pending invitation and fires an email via `organization-invitation-email-service.ts`.
2. Invitee lands on `/invitations/:invitationId` — `invitation-landing-content.ts` validates the token and describes the invitation.
3. Invitee accepts via `invitation-activation-form.tsx` → `POST /api/invitations/activate` → `organization-invitation-activation-service.ts` converts the invitation to membership.

## Membership & Access

- `organization-membership-service.ts` — add/remove/query members
- `organization-access-service.ts` — role checks; `requireOrganizationAdminAccessForUser` used for admin-gated history/audit routes
- `project-membership-service.ts` — per-project access within an org

## Seats & Billing

- `organization-seat-service.ts` — tracks occupied seats vs. plan limit
- `organization-billing-service.ts` — billing state and plan information
- `billing-ui.ts` — client-side billing display helpers
- UI: `app/(authenticated)/billing/organization-billing-panel.tsx`

## Audit Log

`organization-audit-log-service.ts` / `organization-audit-log-store.ts` record significant org-level events. Accessible at `/organization/audit`.

## Onboarding

New users create their first organization via `POST /api/onboarding/organizations` after signup.

## Organization Switcher

`components/app/organization-switcher.tsx` allows switching the active org without logging out. The active org is persisted to session server-side.