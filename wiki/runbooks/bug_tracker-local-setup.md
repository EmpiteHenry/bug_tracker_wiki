---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [local-dev, setup, postgresql, docker]
status: active
---

# Runbook: Local Development Setup

## Prerequisites

- Node.js
- npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone the repository and install dependencies
npm install

# 2. Create local environment file
cp .env.example .env

# 3. Start local PostgreSQL container
docker compose up -d postgres

# 4. Apply database migrations
npm run db:migrate:postgres

# 5. Start the development server
npm run dev
```

Open `http://localhost:3000`.

## Environment Variables (`.env.example`)

```
POSTGRES_HOST=127.0.0.1
POSTGRES_PORT=5432
POSTGRES_DB=bug_tracker
POSTGRES_USER=<set in example>
POSTGRES_PASSWORD=<set in example>
# SendGrid vars — leave empty for basic local dev
```

Do not change PostgreSQL defaults unless you have a specific reason.

## Day-to-Day Commands

| Command | When to use |
|---|---|
| `npm run dev` | Start dev server (runs `build:db-worker` first via predev hook) |
| `npm run db:migrate:postgres` | After pulling new migrations |
| `npm run test` | Run fast unit tests (in-memory DB, no Docker needed) |
| `npm run test:unit:postgres` | Run DB-specific tests (requires Docker postgres running) |
| `npm run lint` | ESLint check |
| `npm run typecheck` | Full TypeScript type check |
| `npm run build` | Production build |
| `npm run start` | Run production server (after build) |

## Running E2E Tests

```bash
# Seed test database and start dev server on port 3001
npm run dev:playwright

# In another terminal, run Playwright tests
npm run test:e2e

# With UI
npm run test:e2e:ui
```

## Running CLI Tools

```bash
# Bug agent CLI
npm run bugs:cli

# Alerts CLI
npm run alerts:cli

# MCP server
npm run mcp:server
```

## Local CI

```bash
npm run ci          # Full CI suite
npm run ci:quick    # Skip slow checks
npm run ci:e2e      # CI with e2e tests
```

## Related Docs

- [Deployment with Nginx](../systems/bug_tracker/deployment/nginx.md) — reverse proxy setup
- [Observability](../systems/bug_tracker/monitoring/observability.md) — log configuration