---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [organizations, projects, multi-tenant, invitations, billing]
status: active
---

# Organizations & Projects

## Multi-Tenancy Model

The app is multi-tenant at the organization level. Each user belongs to one or more organizations. All data (bugs, projects, notifications) is scoped to an organization.

The active organization for a request is resolved via `requireActiveOrganizationSession` (`src/lib/organizations/active-organization-session.ts`), which reads a stored active-org preference from the session.

Users can switch organizations via the `OrganizationSwitcher` component.

## Organization Membership

- Users are members of organizations with a role.
- `organization-membership-service.ts` handles adding/removing members.
- `organization-access-service.ts` exposes `requireOrganizationAdminAccessForUser` for admin-gated operations.
- `organization-seat-service.ts` enforces seat limits tied to billing plan.

## Invitations

1. Admin sends invite → `organization-invitation-service.ts` creates a record + `organization-invitation-email-service.ts` sends email.
2. Invitee lands on `/invitations/[invitationId]` → sees invitation landing page.
3. Invitee activates → `organization-invitation-activation-service.ts` creates/links their account.

Token is embedded in the invitation URL. Logic in `src/app/invitations/[invitationId]/invitation-landing-content.ts`.

## Projects

Projects are created by admins and scoped to an organization. Each project can have **sections** (ordered groupings of bugs).

| API | Description |
|---|---|
| `GET /api/admin/projects` | List all projects |
| `POST /api/admin/projects` | Create project |
| `PATCH /api/admin/projects/[id]` | Update (name, key, description, archive) |
| `GET /api/admin/projects/[id]/sections` | List sections |
| `POST /api/admin/projects/[id]/sections` | Create section |
| `PATCH /api/admin/projects/[id]/sections/[sid]` | Update section (name, order, archive) |

Project access for non-admin users is controlled by `project-membership-service.ts`. The context helper `src/app/(authenticated)/project-access-context.ts` generates UI copy for unauthorized states.

## Billing

`organization-billing-service.ts` manages subscription state. `billing-ui.ts` drives UI visibility rules based on plan. Seat enforcement happens in `organization-seat-service.ts` before invitations are accepted.
