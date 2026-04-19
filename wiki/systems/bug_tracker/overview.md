---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 45
tags: [next.js, react, typescript, postgresql, bug-tracking]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a multi-tenant Next.js 16 application for capturing, triaging, and managing software bugs inside authenticated workspaces.

## Architecture at a Glance

```
Browser (React/Next.js)
    │
    ├─ App Router (src/app/)
    │   ├─ (authenticated)/    ← workspace UI, requires session
    │   └─ api/                ← REST API route handlers
    │
    ├─ Service Layer (src/lib/)
    │   ├─ auth/               ← sessions, passwords, email verification
    │   ├─ bugs/               ← bug CRUD, comments, attachments, history
    │   ├─ organizations/      ← tenancy, billing, invitations, membership
    │   ├─ projects/           ← project and section management
    │   ├─ monitoring/         ← observability, alerts, performance
    │   ├─ notifications/      ← SendGrid email pipeline
    │   └─ settings/           ← API keys, JWT config
    │
    └─ Database (PostgreSQL)
        └─ postgres-worker.ts  ← isolated worker thread for DB operations
```

## Key Subsystems

| Subsystem | Purpose | Key files |
|---|---|---|
| Authentication | Session cookies, password flows, email verify | [auth](../../concepts/bug_tracker-auth-flow.md) |
| Bug management | CRUD, bulk ops, attachments, comments, history | [bugs API](./api/bugs.md) |
| Organizations | Multi-tenancy, invitations, seats, billing | [organizations API](./api/organizations.md) |
| Admin panel | User mgmt, projects, monitoring, notifications | [admin API](./api/admin.md) |
| Agent API | Automated bug claim/update for AI agents | [agent API](./api/agent.md) |
| Chrome extension | Browser-based bug capture | [extension API](./api/extension.md) |
| Monitoring | Structured logs, error grouping, perf metrics | [monitoring](./monitoring.md) |
| Notifications | SendGrid email delivery with templates | [notifications](./notifications.md) |
| Storage | File attachments on disk | [storage](./storage.md) |

## Technology Stack

- **Runtime**: Node.js, Next.js 16 (App Router, Webpack mode)
- **UI**: React 19, Tailwind CSS 4, shadcn/ui, Lucide icons
- **Database**: PostgreSQL (via `pg`), with in-memory fallback for unit tests
- **Email**: SendGrid
- **Validation**: Zod 4
- **Testing**: Node built-in test runner, Playwright (E2E)
- **MCP**: `@modelcontextprotocol/sdk` for agent tooling

## Entry Points

| Path | Purpose |
|---|---|
| `http://localhost:3000` | Main web app |
| `src/app/api/` | REST API |
| `scripts/bug-agent-cli.ts` | CLI for agent bug operations |
| `scripts/alerts-cli.ts` | CLI for operational alerts |
| `scripts/mcp-server.ts` | MCP server for AI tooling |
| `extension/` | Chrome extension source |