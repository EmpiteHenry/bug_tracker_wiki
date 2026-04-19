---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 42
tags: [bug-tracker, nextjs, typescript, postgresql]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a Next.js 16 application for teams to capture, triage, and manage software issues inside authenticated, multi-tenant workspaces.

## Key Capabilities

| Capability | Description |
|---|---|
| Bug management | Create, triage, update, bulk-update, and archive bugs with history tracking |
| Multi-tenancy | Organizations, invitations, project-scoped access |
| Agent API | REST endpoints for automated bug operations by AI agents |
| Chrome Extension | Browser extension for capturing bugs from any page |
| MCP Server | Model Context Protocol server exposing bug operations |
| Notifications | Email delivery via SendGrid for bug events and auth flows |
| Observability | Structured NDJSON logging, request tracing, operational alert system |
| Data Import/Export | Bulk import/export of bugs with job tracking |
| Admin Panel | Dashboard, user management, project configuration, monitoring |

## Technology Stack

- **Framework**: Next.js 16 (App Router, webpack mode)
- **Language**: TypeScript 5
- **Database**: PostgreSQL (primary) + in-memory SQLite (tests)
- **Email**: SendGrid
- **UI**: React 19, Tailwind CSS v4, shadcn/ui, Radix Base UI
- **Testing**: Node.js built-in test runner + Playwright (e2e)
- **Protocol**: MCP SDK (`@modelcontextprotocol/sdk`)

## Top-Level Directory Map

```
src/app/           Next.js App Router pages + API routes
src/lib/           Domain services, stores, and utilities
src/components/    Shared UI components
src/hooks/         React hooks
src/types/         TypeScript ambient declarations
extension/         Chrome extension source
scripts/           CLI scripts (bug-agent-cli, alerts-cli, mcp-server)
docs/              Deployment and observability guides
tasks/             PRD and planning documents
```

## Related Pages

- [Architecture: Service/Store Layers](./architecture/layers.md)
- [API Endpoints Reference](./api/endpoints.md)
- [Database](./database/schema.md)
- [Authentication](./auth/authentication.md)
- [Bug Lifecycle](./bugs/bug-lifecycle.md)
- [Monitoring & Observability](./monitoring/observability.md)
- [Notifications](./notifications/email-notifications.md)
- [Agent API](./agent/agent-api.md)
- [Chrome Extension](./extension/chrome-extension.md)
- [Multi-tenancy & Organizations](./organizations/multi-tenancy.md)
- [Data Import/Export](./bugs/data-import-export.md)