---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 45
tags: [next.js, typescript, postgresql, react, bug-tracking]
status: active
---

# Bug Tracker — System Overview

Bug Tracker is a Next.js 16 application for capturing, triaging, and managing software issues inside an authenticated workspace. It is a full-stack monorepo: the browser app, all API routes, and background tooling live in a single Next.js project.

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (webpack mode) |
| Language | TypeScript 5 |
| UI | React 19, Tailwind CSS 4, shadcn/ui |
| Database | PostgreSQL (primary), in-memory SQLite (tests) |
| Email | SendGrid |
| Runtime tooling | tsx, esbuild, node:test |
| E2E testing | Playwright |

## Major Subsystems

| Subsystem | Path | Purpose |
|---|---|---|
| Auth | `src/lib/auth/` | Login, signup, sessions, password flows, email verification |
| Bugs | `src/lib/bugs/` | CRUD, comments, attachments, history, bulk ops, import/export |
| Organizations | `src/lib/organizations/` | Org creation, membership, invitations, billing, seat management |
| Projects | `src/lib/projects/` | Project + section management, membership |
| Monitoring | `src/lib/monitoring/` | Observability events, operational logs, alerts, performance |
| Notifications | `src/lib/notifications/` | Email dispatch via SendGrid, template engine, delivery history |
| Settings | `src/lib/settings/` | API keys, JWT auth config, API access policy |
| Extension | `src/lib/extension/` | Chrome extension auth and bug submission |
| Agent API | `src/lib/bugs/agent-api.ts` | Programmatic bug operations for automated agents |
| MCP Server | `src/lib/bugs/mcp-server.ts` | Model Context Protocol server for LLM tool access |

## Entry Points

- **Browser app**: `src/app/(authenticated)/` — all authenticated workspace pages
- **REST API**: `src/app/api/` — Next.js route handlers
- **Admin surfaces**: `src/app/(authenticated)/admin/`
- **CLI scripts**: `scripts/` — bug agent CLI, alerts CLI, MCP server
- **Chrome extension**: `extension/`

## Database Backends

The app supports two backends selected via `BUG_TRACKER_TEST_DB_BACKEND`:

- `memory` — in-memory SQLite for fast unit tests (default for `npm test`)
- `postgres` — PostgreSQL for production and integration tests

See [database schema](./database/schema.md) and [local setup runbook](../../runbooks/bug_tracker-local-setup.md).

## Related Pages

- [API Endpoints](./api/endpoints.md)
- [Authentication](./auth/authentication.md)
- [Bug Management](./bugs/bug-management.md)
- [Monitoring & Observability](./monitoring/observability.md)
- [Notification System](./notifications/email-notifications.md)
- [Agent API & MCP](./agent/agent-api.md)
- [Chrome Extension](./extension/chrome-extension.md)
- [Database Schema](./database/schema.md)