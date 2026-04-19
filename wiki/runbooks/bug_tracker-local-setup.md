---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [setup, local, development, postgres, docker]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js + npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone and enter the repo
cd /path/to/bug_tracker

# 2. Create local env file
cp .env.example .env

# 3. Install dependencies
npm install

# 4. Start PostgreSQL
docker compose up -d postgres

# 5. Apply database migrations
npm run db:migrate:postgres

# 6. Start the dev server
npm run dev
```

Open http://localhost:3000.

## Daily Workflow

| Task | Command |
|---|---|
| Start dev server | `npm run dev` |
| Run fast unit tests | `npm run test` |
| Run PostgreSQL unit tests | `npm run test:unit:postgres` |
| Type-check | `npm run typecheck` |
| Lint | `npm run lint` |
| Production build | `npm run build && npm run start` |

## E2E Tests

```bash
# Seed test DB and start on port 3001
npm run dev:playwright

# In another terminal
npm run test:e2e
npm run test:e2e:ui    # with Playwright UI
```

## Agent / CLI Tools

```bash
npm run bugs:cli      # bug agent CLI
npm run alerts:cli    # operational alerts CLI
npm run mcp:server    # MCP tool server for LLM agents
```

## Environment Variables

Key variables in `.env`:

| Variable | Purpose |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string |
| `SENDGRID_API_KEY` | Email delivery (optional for local dev) |
| `BUG_TRACKER_TEST_DB_BACKEND` | `memory` (default) or `postgres` for tests |

## Troubleshooting

- **`db:migrate:postgres` fails** — check Docker is running: `docker compose ps`
- **Port 5432 in use** — stop local PostgreSQL: `brew services stop postgresql` or equivalent
- **Type errors after pull** — run `npm run typecheck` to regenerate Next.js types