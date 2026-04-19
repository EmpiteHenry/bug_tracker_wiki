---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 10
tags: [api, organizations, invitations, billing]
status: active
---

# Organizations API

Organizations are the top-level tenancy boundary. Each user belongs to one or more organizations; bugs and projects are scoped to an organization.

## Endpoints

### GET /api/organizations
List all organizations the authenticated user belongs to.

### POST /api/organizations
Create a new organization (used during onboarding flow).

### GET /api/organizations/active
Returns the currently active organization from the session.

### GET /api/organizations/[organizationId]
Get details of a specific organization.

### PATCH /api/organizations/[organizationId]
Update organization settings (name, slug, etc.)

### POST /api/invitations
Create an invitation to join an organization.

### GET /api/invitations/[invitationToken]
Look up an invitation by token (public — used from invitation email link).

### POST /api/invitations/activate
Activate an invitation (creates membership).

### GET /api/projects
List projects visible to the current user in the active organization.

### POST /api/projects
Create a project.

### GET /api/projects/[projectId]
Get project details.

### PATCH /api/projects/[projectId]
Update a project.

## Active Organization Session

The active organization is stored separately in the session (`active-organization-session.ts`). The middleware/layout resolves it on each request. Access the active org via `requireActiveOrganizationSession()`.

## Service Files

| File | Responsibility |
|---|---|
| `src/lib/organizations/organization-service.ts` | Org CRUD |
| `src/lib/organizations/organization-membership-service.ts` | Member add/remove/list |
| `src/lib/organizations/organization-invitation-service.ts` | Invitation tokens |
| `src/lib/organizations/organization-invitation-activation-service.ts` | Activate invitation |
| `src/lib/organizations/organization-invitation-email-service.ts` | Send invite emails |
| `src/lib/organizations/organization-access-service.ts` | Role/permission checks |
| `src/lib/organizations/organization-billing-service.ts` | Billing state |
| `src/lib/organizations/organization-seat-service.ts` | Seat counting/limits |
| `src/lib/organizations/active-organization-service.ts` | Resolve active org |
| `src/lib/projects/project-service.ts` | Project + section CRUD |
| `src/lib/projects/project-membership-service.ts` | Project-level access |