---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 40
tags: [nextjs, react, typescript, postgresql, bug-tracking]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a Next.js 16 application for teams to capture, triage, and manage software issues inside an authenticated multi-tenant workspace.

## Core Capabilities

| Area | Summary |
|---|---|
| Bug management | Create, update, comment, attach files, bulk status changes |
| Project / section hierarchy | Bugs are scoped to projects and optional sections |
| Multi-tenant organizations | Each workspace is an organization; users belong to one or more |
| Authentication | Email/password sessions plus extension Bearer tokens and API keys |
| Admin surfaces | Project, user, notification, monitoring, and bug-data management |
| Agent API | Headless REST endpoints for automated bug claiming and status updates |
| Chrome extension | Companion extension that submits bugs from the browser |
| Observability | Structured request logging, error fingerprinting, alerting, performance |
| Notifications | Email delivery via SendGrid with per-event settings and retry support |
| Data import/export | Bulk CSV import and full ZIP export with async job tracking |

## Technology

- **Runtime**: Node.js via Next.js 16 (App Router, webpack mode)
- **Frontend**: React 19, Tailwind CSS 4, shadcn/ui, Sonner toasts
- **Database**: PostgreSQL (production); in-memory SQLite-compatible adapter for tests
- **Email**: SendGrid
- **Validation**: Zod 4
- **MCP integration**: `@modelcontextprotocol/sdk` for the MCP server entry point

## Repository Layout

```
src/app/          Next.js routes (authenticated UI + API handlers)
src/lib/          Business logic, services, stores (no framework deps)
src/components/   Shared React components
scripts/          CLI entry points (bug agent, alerts, MCP server, migrations)
extension/        Chrome extension source (manifest v3)
docs/             Deployment and observability guides
```

## Related Pages

- [Domain Model](../../concepts/bug_tracker-domain-model.md)
- [API Endpoints](api/endpoints.md)
- [Authentication](auth/authentication.md)
- [Database](database/schema.md)
- [Observability & Monitoring](monitoring/observability.md)
- [Notifications](notifications/email-notifications.md)
- [Agent Integration](integrations/agent-api.md)
- [Chrome Extension](integrations/chrome-extension.md)
- [Admin Surfaces](admin/admin-surfaces.md)
- [Bug Data Import/Export](bugs/data-import-export.md)
- [Local Setup Runbook](../../runbooks/bug_tracker-local-setup.md)