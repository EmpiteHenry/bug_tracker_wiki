---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [local-dev, setup, postgresql, docker]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js (LTS)
- npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone repo and install dependencies
npm install

# 2. Create local env file
cp .env.example .env
# Edit .env if needed — defaults work for local PostgreSQL

# 3. Start PostgreSQL container
docker compose up -d postgres

# 4. Apply database migrations
npm run db:migrate:postgres

# 5. Start dev server
npm run dev
```

Open `http://localhost:3000`.

## Daily Workflow

```bash
# Start dependencies
docker compose up -d postgres

# Start dev server (automatically builds DB worker first)
npm run dev
```

## Running Tests

```bash
# Fast unit tests (in-memory DB, no Docker needed)
npm test

# PostgreSQL-backed unit tests (requires running container)
npm run test:unit:postgres

# E2E tests
npm run test:e2e:seed      # seed test database
npm run test:e2e           # run Playwright tests
npm run test:e2e:headed    # run with visible browser
npm run test:e2e:ui        # Playwright UI mode
```

## Code Quality

```bash
npm run lint        # ESLint
npm run typecheck   # TypeScript + Next.js type generation
npm run ci          # Full local CI (lint + typecheck + test)
npm run ci:quick    # Skip slower checks
```

## Production Build

```bash
npm run build   # Next.js build + DB worker bundle
npm run start   # Serve production build
```

## Environment Variables

Key variables (from `.env.example`):

| Variable | Purpose |
|---|---|
| `DATABASE_URL` or `POSTGRES_*` | PostgreSQL connection |
| `SENDGRID_API_KEY` | Email delivery (optional for local) |
| `BUG_TRACKER_TEST_DB_BACKEND` | `memory` for unit tests |

## Troubleshooting

- **DB connection refused**: Ensure `docker compose up -d postgres` is running
- **Worker build errors**: Run `npm run build:db-worker` manually
- **Type errors**: Run `npm run typecheck` — it regenerates Next.js types first