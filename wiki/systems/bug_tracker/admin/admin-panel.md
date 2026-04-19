---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [admin, management, users, projects]
status: active
---

# Admin Panel

Admin routes are under `/admin/` (UI) and `/api/admin/` (API). All require `requireAdminUser()`.

## Sections

### Dashboard
`GET /api/admin/dashboard/summary` — aggregated counts for the admin home page.

### Projects
| Route | Purpose |
|---|---|
| `GET/POST /api/admin/projects` | List / create projects |
| `PATCH /api/admin/projects/[id]` | Update project (`name`, `key`, `description`, `isArchived`) |
| `GET/POST /api/admin/projects/[id]/sections` | List / create sections |
| `PATCH /api/admin/projects/[id]/sections/[sectionId]` | Update section (`name`, `displayOrder`, `isArchived`) |

### Users (QA Users)
| Route | Purpose |
|---|---|
| `GET/POST /api/admin/users` | List / create QA users |
| `POST /api/admin/users/[userId]/deactivate` | Deactivate a QA user |

### Monitoring
See [Monitoring & Observability](../monitoring/observability.md).

### Notifications
See [Email Notifications](../notifications/email-notifications.md).

### Bug Data (Import/Export)
Admin panel at `/admin/bug-data`. Jobs are async; statuses: `queued`, `running`, `completed`, `completed_with_warnings`, `failed`.

Import supports CSV with validation preview before execution. Duplicate strategies: `skip_existing`, `update_existing`.

Export produces a downloadable archive with bugs, history, and attachments.

### Organizations With Projects
`GET /api/admin/organizations-with-projects` — flat join of orgs and their projects for admin overviews.

## Admin Navigation
`src/app/(authenticated)/admin/admin-nav.tsx` — admin sidebar navigation component.