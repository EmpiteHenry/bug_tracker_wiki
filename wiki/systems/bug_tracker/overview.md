---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 45
tags: [next.js, typescript, postgresql, bug-tracking, saas]
status: active
---

# Bug Tracker — System Overview

A Next.js 16 SaaS application for teams to capture, triage, and manage software issues inside authenticated multi-tenant workspaces.

## Architecture at a Glance

```
Browser (Next.js App Router)
  └── Authenticated Routes  (/app/(authenticated)/*)
  └── Public Routes         (/login, /signup, /invitations/*)
  └── API Routes            (/app/api/*)
       ├── /auth/*          — session, login, signup, password flows
       ├── /bugs/*          — CRUD, comments, attachments, history
       ├── /agent/*         — machine-to-machine bug operations
       ├── /extension/*     — Chrome extension API surface
       ├── /admin/*         — platform admin (projects, users, monitoring)
       └── /organizations/* — org membership and invitations

PostgreSQL (primary store)
  └── Managed via postgres-worker.ts (Worker thread)
  └── In-memory SQLite for fast unit tests
```

## Major Subsystems

| Subsystem | Location | Purpose |
|---|---|---|
| Auth | `src/lib/auth/` | Session, registration, password, email verification |
| Bugs | `src/lib/bugs/` | Bug CRUD, comments, attachments, history, import/export |
| Organizations | `src/lib/organizations/` | Multi-tenancy, membership, invitations, billing |
| Projects | `src/lib/projects/` | Project + section management |
| Monitoring | `src/lib/monitoring/` | Observability, alerts, error grouping, performance |
| Notifications | `src/lib/notifications/` | Email dispatch via SendGrid |
| Extension | `src/lib/extension/` | Chrome extension auth and version management |
| Agent | `src/lib/bugs/agent-*.ts` | Automated/AI bug intake and operations |
| Settings | `src/lib/settings/` | API keys, JWT auth, API access config |

## Key Technology Choices

- **Framework**: Next.js 16 with App Router (webpack bundler)
- **Language**: TypeScript 5
- **Database**: PostgreSQL (primary), in-memory SQLite (tests)
- **UI**: React 19, shadcn/ui, Tailwind CSS 4
- **Email**: SendGrid
- **Validation**: Zod 4
- **AI/Agent Integration**: MCP (Model Context Protocol) server

## Entry Points

- **Web app**: `http://localhost:3000`
- **Dev server**: `npm run dev`
- **MCP server**: `npm run mcp:server`
- **Bug agent CLI**: `npm run bugs:cli`
- **Alerts CLI**: `npm run alerts:cli`

## Related Pages

- [Authentication](./auth/authentication.md)
- [Bug Management](./bugs/bug-management.md)
- [API Endpoints](./api/endpoints.md)
- [Database Architecture](./database/schema.md)
- [Monitoring & Observability](./monitoring/observability.md)
- [Agent Integration](./agent/agent-integration.md)
- [Chrome Extension](./extension/chrome-extension.md)
- [Organizations & Multi-tenancy](./organizations/multi-tenancy.md)
- [Notifications](./notifications/email-notifications.md)