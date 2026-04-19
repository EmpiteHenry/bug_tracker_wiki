---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [organizations, multi-tenancy, memberships, invitations, billing]
status: active
---

# Multi-Tenancy & Organizations

## Data Model

```
User
 └─ OrganizationMembership ──► Organization
                                    └─ Project
                                         └─ ProjectSection
                                              └─ Bug
```

Every user belongs to one or more organizations. Within an organization, access to individual projects is controlled by `ProjectMembership`.

## Organization Lifecycle

1. Created at signup via `registerUserWithOrganization()` in `auth-service`
2. Or created standalone via `POST /api/onboarding/organizations`
3. Users can switch active organization via `active-organization-service`
4. Active org stored in session via `active-organization-session`

## Active Organization Session (`src/lib/organizations/active-organization-session.ts`)

The active organization is tracked in the session. `requireActiveOrganizationSession(request)` throws `ActiveOrganizationSessionError` if no org is active — used by all authenticated bug API routes.

## Invitations

1. Admin creates invitation via `POST /api/organizations/invitations`
2. Recipient receives email (via `organization-invitation-email-service`)
3. Recipient visits `/invitations/[invitationId]` → accepts via `organization-invitation-activation-service`
4. `POST /api/invitations/[token]/activate` completes membership creation

## Access Control

`organization-access-service.ts` exposes:
- `requireOrganizationAdminAccessForUser()` — throws `OrganizationAccessError` if user is not org admin

Project-level access managed by `project-membership-service`.

`project-access-context.ts` — computes no-access messaging when a user lacks project access:
```ts
getNoProjectAccessContent(organizationName) → { title, description, detail }
```

## Seats & Billing

- `organization-seat-service.ts` — tracks how many seats an org has used
- `organization-billing-service.ts` — billing tier and limits logic
- `billing-ui.ts` — UI-facing billing state helpers
- Admin billing panel at `/billing`

## Audit Log

All organization-level administrative actions are recorded by `organization-audit-log-service` → `organization-audit-log-store`. Viewable at `/organization/audit`.

## Organization Switcher

`src/components/app/organization-switcher.tsx` — sidebar component that lets users switch their active org. Fetches from `GET /api/organizations`.