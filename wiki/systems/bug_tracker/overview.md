---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 45
tags: [nextjs, typescript, postgresql, bug-tracker]
status: active
---

# Bug Tracker — System Overview

A Next.js 16 application for teams to capture, triage, and manage software issues inside an authenticated workspace.

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16.1.6 (App Router, webpack mode) |
| Language | TypeScript 5 |
| UI | React 19, Tailwind CSS 4, shadcn/ui |
| Database | PostgreSQL (primary), in-memory SQLite (tests) |
| Email | SendGrid |
| Runtime | Node.js |

## Major Subsystems

| Subsystem | Location | Purpose |
|---|---|---|
| Auth | `src/lib/auth/` | Login, signup, sessions, password flows |
| Bugs | `src/lib/bugs/` | Core bug CRUD, comments, attachments, history |
| Organizations | `src/lib/organizations/` | Multi-tenant orgs, membership, invitations, billing |
| Projects | `src/lib/projects/` | Project + section management |
| Monitoring | `src/lib/monitoring/` | Operational logs, alerts, error fingerprinting, performance |
| Notifications | `src/lib/notifications/` | Email dispatch via SendGrid, templates, delivery tracking |
| Agent API | `src/app/api/agent/` | Programmatic bug operations for automated agents |
| Extension API | `src/app/api/extension/` | Chrome extension auth and bug reporting |
| Admin API | `src/app/api/admin/` | Admin-only management surfaces |
| Settings | `src/lib/settings/` | API keys, JWT config, API access |

## URL Structure

```
/                        → redirect
/login                   → login form
/signup                  → registration + org creation
/dashboard               → authenticated home
/bugs                    → bug list with filters
/bugs/[bugId]            → bug detail
/bugs/new                → create bug
/admin/*                 → admin panel
/settings/*              → user/org settings
/organization/*          → org management
/billing                 → billing panel
```

## API Route Groups

- `/api/auth/*` — authentication flows
- `/api/bugs/*` — bug CRUD and sub-resources
- `/api/agent/bugs/*` — agent-facing bug API
- `/api/extension/*` — Chrome extension API
- `/api/admin/*` — admin operations
- `/api/organizations/*` — org management
- `/api/projects/*` — project management
- `/api/invitations/*` — invitation flows
- `/api/observability/*` — client-side telemetry ingestion
- `/api/settings/*` — API key and access settings

## Related Pages

- [Authentication](../auth/authentication.md)
- [Bug Lifecycle](../bugs/bug-lifecycle.md)
- [Agent API](../agent/agent-api.md)
- [Monitoring & Observability](../monitoring/observability.md)
- [Database](../database/schema.md)
- [Admin Panel](../admin/admin-panel.md)
- [Notifications](../notifications/email-notifications.md)
- [Chrome Extension API](../extension/chrome-extension.md)