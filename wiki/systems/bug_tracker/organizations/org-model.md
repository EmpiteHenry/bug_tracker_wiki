---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [organizations, membership, billing, invitations, multi-tenant]
status: active
---

# Organizations & Multi-Tenancy

The app is multi-tenant. Every resource (bugs, projects, notifications) is scoped to an organization. The organization subsystem lives in `src/lib/organizations/`.

## Tenant Isolation

The active organization for a session is tracked in `src/lib/organizations/active-organization-session.ts`. Every authenticated request resolves the active org via `requireActiveOrganizationSession()`, which throws `ActiveOrganizationSessionError` if no org is active. This is the tenancy boundary — all data queries include `organizationId`.

`organization-switcher.tsx` in the sidebar allows users who belong to multiple orgs to switch context.

## Membership & Access (`organization-membership-service.ts`, `organization-access-service.ts`)

- Users join orgs via invitation or signup
- `requireOrganizationAdminAccessForUser` — gates org-admin operations
- `OrganizationAccessError` — thrown when access is denied

## Invitations (`organization-invitation-service.ts`)

Flow:
1. Admin creates invitation → token stored in DB, email sent via `organization-invitation-email-service.ts`
2. Invitee visits `/invitations/[invitationId]` → sees landing page
3. Invitee accepts → `organization-invitation-activation-service.ts` creates membership

## Seat Management (`organization-seat-service.ts`)

Tracks the number of active seats per organization for billing purposes. Seat counts are checked before new members are added.

## Billing (`organization-billing-service.ts`)

`src/lib/organizations/billing-ui.ts` provides UI helper logic for the billing page. The billing service interfaces with the subscription/plan layer. The billing page lives at `/billing`.

## Project Access Context

`src/app/(authenticated)/project-access-context.ts` provides the "no project access" UI copy for users who are authenticated but not a member of any project in the active org.

## Audit Log (`src/lib/audit/`)

`organization-audit-log-service.ts` records admin actions (user changes, project changes, settings changes) with actor, action, and target. The audit log is viewable at `/organization/audit`.