---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [admin, users, projects, monitoring, notifications, bug-data]
status: active
---

# Admin Panel

## Access Control

All admin API routes call `requireAdminUser` from `auth-service.ts`. Non-admin requests receive 401/403.

The admin nav is rendered by `src/app/(authenticated)/admin/admin-nav.tsx`.

## Admin Surfaces

### User Management
- `GET /api/admin/users` — list QA users
- `POST /api/admin/users` — create QA user (name, email, temporaryPassword)
- `POST /api/admin/users/[id]/deactivate` — deactivate a user

Implemented via `user-service.ts` (`listQaUsers`, `createQaUser`, `deactivateQaUser`).

### Project Management
- `GET|POST /api/admin/projects` — list / create projects
- `PATCH /api/admin/projects/[id]` — update project (name, key, description, isArchived)
- `GET|POST /api/admin/projects/[id]/sections` — list / create sections
- `PATCH /api/admin/projects/[id]/sections/[sid]` — update section

### Organizations View
- `GET /api/admin/organizations-with-projects` — flat join of all orgs and their projects (for admin overview)

### Monitoring
See [Monitoring](./monitoring.md) for full details. Admin dashboard summary at `GET /api/admin/dashboard/summary`.

### Notifications Admin
See [Notifications](./notifications.md) for full details.

### Bug Data Management
Admins can import and export bug data as batch jobs. See [Bugs — Import/Export](./bugs.md#data-import--export).

Admin UI in `src/app/(authenticated)/admin/bug-data/`.

## Filter Components

Admin sections with filterable lists use local filter state objects:
- `MonitoringAlertFilterState` — `alert-list-filters.ts`
- `NotificationDeliveryFilterState` — `notification-center-filters.ts`

Both build `URLSearchParams` and validate before making API calls.
