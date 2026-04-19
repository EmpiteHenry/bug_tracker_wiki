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
# 1. Clone and enter the repo
cd /Users/henrysmith/Empite/bug_tracker

# 2. Copy environment file
cp .env.example .env
# Keep default PostgreSQL settings unless you have a reason to change them

# 3. Install dependencies
npm install

# 4. Start PostgreSQL
docker compose up -d postgres

# 5. Run migrations
npm run db:migrate:postgres

# 6. Start dev server
npm run dev

# 7. Open browser
open http://localhost:3000
```

## Daily Development

```bash
# Start PostgreSQL (if not running)
docker compose up -d postgres

# Start dev server (predev builds the DB worker automatically)
npm run dev
```

## Environment Variables

See `.env.example`. Required for full functionality:

| Variable | Required | Notes |
|---|---|---|
| `DATABASE_URL` or equivalent PG vars | Yes | Points to local PostgreSQL |
| `SENDGRID_API_KEY` | No | Only needed for email flow testing |
| `SENDGRID_FROM_EMAIL` | No | Only needed for email flow testing |

## Running Tests

```bash
# Fast unit tests (in-memory SQLite, no Docker required)
npm test

# Unit tests against real PostgreSQL
npm run test:unit:postgres

# E2E tests (requires running app on port 3001)
npm run test:e2e:seed    # seed test DB first
npm run test:e2e
```

## Useful Commands

```bash
npm run lint          # ESLint
npm run typecheck     # TypeScript check (slow — rebuilds from scratch)
npm run build         # Production build
npm run start         # Run production build locally
```

## Admin Bootstrap

To create the first admin user on a fresh database, run the bootstrap script:

```bash
# See src/lib/db/bootstrap-admin.ts
npx tsx scripts/bootstrap-admin.ts   # (if script exists; otherwise adapt from bootstrap-admin.ts)
```

## Nginx / Domain / Observability

See:
- `docs/deployment-nginx.md` — reverse proxy and domain setup
- `docs/observability.md` — structured logging and file output