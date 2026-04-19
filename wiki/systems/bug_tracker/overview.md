---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 42
tags: [nextjs, typescript, react, postgresql, bug-tracking]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a multi-tenant Next.js 16 application for capturing, triaging, and managing software issues inside authenticated workspaces.

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 16 (App Router, webpack mode) |
| Language | TypeScript 5 |
| UI | React 19, Tailwind CSS 4, shadcn/ui |
| Database | PostgreSQL (primary) + in-memory (test) |
| Email | SendGrid |
| Runtime | Node.js |

## Top-Level Architecture

```
browser client
    │
    ├── /app/(authenticated)/*   — protected workspace UI
    ├── /app/api/*               — Next.js Route Handlers (REST API)
    │       ├── /auth/*          — login, signup, session, password flows
    │       ├── /bugs/*          — CRUD, comments, attachments, bulk
    │       ├── /agent/*         — agent automation API
    │       ├── /extension/*     — Chrome extension API
    │       ├── /admin/*         — admin surfaces (monitoring, alerts, users)
    │       ├── /organizations/* — org management, invitations
    │       └── /projects/*      — project and section management
    │
    └── /src/lib/*               — domain services, stores, utilities
```

## Major Subsystems

| Subsystem | Key files | Description |
|---|---|---|
| Auth | `src/lib/auth/` | Session cookies, email verification, password reset |
| Bugs | `src/lib/bugs/` | Core bug lifecycle, history, comments, attachments |
| Organizations | `src/lib/organizations/` | Multi-tenancy, invitations, billing, seats |
| Projects | `src/lib/projects/` | Projects and sections within an org |
| Monitoring | `src/lib/monitoring/` | Observability, operational logs, grouped errors, alerts |
| Notifications | `src/lib/notifications/` | Event-driven emails via SendGrid templates |
| Agent API | `src/lib/bugs/agent-api.ts` | Programmatic bug claiming and status updates for AI agents |
| MCP Server | `src/lib/bugs/mcp-server.ts` | Model Context Protocol server for LLM tool use |
| Extension | `src/lib/extension/` | Chrome extension version gating and auth |
| Data I/O | `src/lib/bugs/bug-data-*` | Bulk import/export with job tracking |

## Request Flow

Every API route is wrapped in `withRequestObservability(method, handler)` which records performance metrics, errors, and structured logs before delegating to the handler.

## Related Pages

- [Auth System](./auth.md)
- [Bug Lifecycle](./bug-lifecycle.md)
- [Agent API](./agent-api.md)
- [Monitoring & Observability](./monitoring.md)
- [Notifications](./notifications.md)
- [Data Import / Export](./data-import-export.md)
- [Chrome Extension](./chrome-extension.md)
- [Local Setup Runbook](../../runbooks/bug_tracker-local-setup.md)