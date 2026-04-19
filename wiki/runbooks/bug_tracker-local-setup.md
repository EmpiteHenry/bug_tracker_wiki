---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [setup, local, development, postgresql, docker]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js (LTS)
- npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone and enter the repo
cd /Users/henrysmith/Empite/bug_tracker

# 2. Copy environment file
cp .env.example .env
# Keep default PostgreSQL settings unless you have a reason to change them.
# Leave SendGrid vars empty for local development.

# 3. Install dependencies
npm install

# 4. Start PostgreSQL
docker compose up -d postgres

# 5. Apply database migrations
npm run db:migrate:postgres

# 6. Start the dev server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Common Commands

| Command | Purpose |
|---|---|
| `npm run dev` | Start dev server (runs `build:db-worker` first via `predev`) |
| `npm run db:migrate:postgres` | Apply migrations (needs `.env` + running postgres) |
| `npm test` | Fast unit tests (in-memory DB, no Docker needed) |
| `npm run test:unit:postgres` | PostgreSQL-backed unit tests |
| `npm run build` | Production build + DB worker bundle |
| `npm run start` | Serve production build locally |
| `npm run lint` | ESLint |
| `npm run typecheck` | TypeScript type check |
| `npm run bugs:cli` | Run the bug agent CLI |
| `npm run alerts:cli` | Run the alerts CLI |
| `npm run mcp:server` | Start the MCP server |

## E2E Tests

```bash
# Seed test DB and start dev server on port 3001
npm run dev:playwright

# Run Playwright tests
npm run test:e2e

# Interactive Playwright UI
npm run test:e2e:ui
```

## CI

```bash
npm run ci          # Full local CI
npm run ci:quick    # Skip slower checks
npm run ci:e2e      # E2E only
```

## Advanced

- Nginx reverse proxy: see [`docs/deployment-nginx.md`](../../docs/deployment-nginx.md)
- Observability file output: see [`docs/observability.md`](../../docs/observability.md)