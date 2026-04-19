---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 45
tags: [nextjs, typescript, postgresql, bug-tracking, multi-tenant]
status: active
---

# Bug Tracker — System Overview

A Next.js 16 full-stack application for authenticated teams to capture, triage, and manage software issues. Supports multi-tenant organizations, project-level access control, AI agent integration, a Chrome extension, and an MCP server interface.

## High-Level Architecture

```
Browser (React/Next.js)
    │
    ├── App Router pages  (/app/(authenticated)/...)
    │       └── Client components, server components, filter logic
    │
    └── API Routes  (/app/api/...)
            ├── /auth/*          — session auth, signup, password flows
            ├── /bugs/*          — CRUD, comments, attachments, history
            ├── /organizations/* — multi-tenant org management
            ├── /projects/*      — project + section management
            ├── /invitations/*   — org invite flows
            ├── /settings/*      — API keys, JWT, access config
            ├── /extension/*     — Chrome extension auth + bug submit
            ├── /agent/*         — agent API (claim, update, list bugs)
            └── /admin/*         — admin-only: users, monitoring, alerts, notifications
```

## Core Layers

| Layer | Path | Responsibility |
|---|---|---|
| Pages / UI | `src/app/(authenticated)/` | Route segments, React panels, client-side filter state |
| API Routes | `src/app/api/` | HTTP handlers, request validation, response shaping |
| Services | `src/lib/*/\*-service.ts` | Business logic, orchestration |
| Stores | `src/lib/*/\*-store.ts` | Database queries, data access |
| DB Client | `src/lib/db/` | PostgreSQL + in-memory adapters, migrations, schema |

## Key Subsystems

- [Auth](./auth.md) — session cookies, email verification, password reset, JWT
- [Bugs](./bugs.md) — bug lifecycle, comments, attachments, history, bulk ops, import/export
- [Organizations & Projects](./organizations.md) — multi-tenancy, invitations, billing seats
- [Notifications](./notifications.md) — SendGrid email dispatch with templates
- [Monitoring](./monitoring.md) — operational logs, error grouping, alerts, performance
- [Agent Integration](./agent-integration.md) — agent API, CLI, MCP server, coordination notes
- [Chrome Extension](./chrome-extension.md) — header-auth extension API, evidence upload
- [Admin Panel](./admin.md) — admin surfaces for all above subsystems

## Tech Stack

- **Runtime**: Node.js, Next.js 16 (App Router, webpack mode)
- **Language**: TypeScript 5
- **Database**: PostgreSQL (production), in-memory (unit tests)
- **UI**: React 19, Tailwind CSS 4, shadcn/ui, Radix primitives
- **Email**: SendGrid
- **AI/Agent**: MCP SDK (`@modelcontextprotocol/sdk`), custom agent CLI
- **Validation**: Runtime type guards (no schema library in API routes)
- **Testing**: Node built-in test runner; Playwright for E2E

## Entry Points

| Purpose | Command |
|---|---|
| Dev server | `npm run dev` |
| Production build | `npm run build` |
| DB migrations | `npm run db:migrate:postgres` |
| Unit tests (in-memory) | `npm run test` |
| Unit tests (postgres) | `npm run test:unit:postgres` |
| E2E tests | `npm run test:e2e` |
| Agent CLI | `npm run bugs:cli` |
| Alerts CLI | `npm run alerts:cli` |
| MCP server | `npm run mcp:server` |
