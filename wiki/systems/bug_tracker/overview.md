---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 45
tags: [bug-tracker, next.js, postgresql, multi-tenant]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a Next.js 16 application for teams to capture, triage, and manage software issues inside an authenticated, multi-tenant workspace.

## Core Capabilities

| Capability | Description |
|---|---|
| Bug management | Create, update, bulk-update, archive bugs with comments and file attachments |
| Projects & sections | Bugs are scoped to projects; projects contain ordered sections |
| Organizations | Multi-tenant — users belong to organizations, each with its own data |
| Authentication | Session-based auth with email verification, password reset, and JWT API keys |
| Notifications | Email delivery via SendGrid for bug lifecycle events |
| Monitoring | Operational logs, error fingerprinting, grouped errors, endpoint performance |
| Admin panel | Admin-only surfaces for users, projects, notifications, monitoring, bug data I/O |
| Agent API | REST endpoints for automated bug operations (claim, update status) |
| MCP server | Model Context Protocol server for LLM-based agent integration |
| Chrome extension | Browser extension for in-page bug reporting |
| Bug data I/O | CSV import and export with job queue and status tracking |

## Technology Stack

- **Runtime**: Node.js + Next.js 16 (App Router, webpack mode)
- **Language**: TypeScript
- **Database**: PostgreSQL (primary); in-memory SQLite for fast unit tests
- **UI**: React 19, Tailwind CSS v4, shadcn/ui components
- **Email**: SendGrid
- **Validation**: Zod v4
- **Testing**: Node built-in test runner + Playwright for E2E

## High-Level Architecture

```
Browser / Extension
       │
  Next.js App Router
  ┌────┴────────────────────────────────────┐
  │  src/app/                               │
  │  ├── (authenticated)/   UI routes       │
  │  ├── api/               API handlers    │
  │  └── layout / auth pages               │
  └─────────────────┬───────────────────────┘
                    │
            src/lib/ (domain services)
            ├── auth/         Authentication & sessions
            ├── bugs/         Bug domain + agent + MCP
            ├── organizations/ Multi-tenancy
            ├── projects/     Project & section management
            ├── notifications/ Email dispatch
            ├── monitoring/   Observability & alerts
            ├── settings/     API keys & JWT config
            └── db/           Database abstraction
                    │
          PostgreSQL (primary)
          In-memory DB (tests)
```

## Key Entry Points

| Path | Purpose |
|---|---|
| `src/app/(authenticated)/` | All authenticated UI pages |
| `src/app/api/` | REST API route handlers |
| `src/lib/bugs/` | Core bug domain logic |
| `src/lib/monitoring/` | Observability stack |
| `src/lib/notifications/` | Email notification system |
| `scripts/` | CLI tools: bug agent, alerts, MCP server |

## Related Pages

- [API Endpoints](./api/endpoints.md)
- [Database Schema](./database/schema.md)
- [Deployment Setup](./deployment/setup.md)
- [Bug Domain Model](../../concepts/bug_tracker-domain-model.md)
- [Notification System](./notifications.md)
- [Monitoring & Observability](./monitoring.md)
- [Agent & MCP Integration](./agent-integration.md)