---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 1
tags: [setup, local-dev, docker, postgresql]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js + npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone and enter repo
cd /path/to/bug_tracker

# 2. Create env file
cp .env.example .env

# 3. Install dependencies
npm install

# 4. Start PostgreSQL
docker compose up -d postgres

# 5. Run migrations
npm run db:migrate:postgres

# 6. Start dev server
npm run dev
```

Open `http://localhost:3000`.

## Daily Workflow

```bash
docker compose up -d postgres   # ensure DB is running
npm run dev                     # start Next.js dev server
```

## Environment Variables

`.env.example` contains all required vars with local defaults. Key vars:

| Var | Purpose |
|---|---|
| `DATABASE_URL` (or similar) | PostgreSQL connection string |
| `SENDGRID_API_KEY` | Leave empty to disable email in dev |

## Test Commands

```bash
npm test                    # fast unit tests (in-memory DB, no Docker needed)
npm run test:unit:postgres  # unit tests against real PostgreSQL
npm run typecheck           # TypeScript type checking
npm run lint                # ESLint
```

## E2E Tests

```bash
npm run dev:playwright      # seed test DB + start on port 3001
npm run test:e2e            # run Playwright tests
npm run test:e2e:ui         # Playwright UI mode
```

## Build & Production Preview

```bash
npm run build
npm run start
```

## Useful Scripts

```bash
npm run bugs:cli    # bug agent CLI (tsx scripts/bug-agent-cli.ts)
npm run alerts:cli  # alerts CLI (tsx scripts/alerts-cli.ts)
npm run mcp:server  # MCP server for LLM agent integration
```