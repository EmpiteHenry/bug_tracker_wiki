---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [local-dev, setup, onboarding]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js (LTS)
- npm
- Docker + Docker Compose

## First-Time Setup

```bash
# Clone the repo, then:
cp .env.example .env
npm install
docker compose up -d postgres
npm run db:migrate:postgres
npm run dev
```

Open http://localhost:3000.

## Daily Development Loop

```bash
docker compose up -d postgres   # ensure DB is running
npm run dev                     # starts Next.js dev server
```

## Running Tests

```bash
# Fast in-memory unit tests (no Docker needed)
npm run test

# PostgreSQL-backed unit tests (requires running postgres container)
npm run test:unit:postgres

# E2E tests (requires seeded test DB)
npm run test:e2e:seed
npm run test:e2e
```

The default test command uses `BUG_TRACKER_TEST_DB_BACKEND=memory` which avoids the Docker dependency for fast local iteration.

## Type Checking & Lint

```bash
npm run typecheck   # full TypeScript check (slow — clears .next first)
npm run lint        # ESLint
```

## E2E Playwright Dev Server

For E2E test development, run the app on port 3001 with seeded data:

```bash
npm run dev:playwright
# then in another terminal:
npm run test:e2e:ui
```

## Resetting the Database

No built-in reset script. To reset:
```bash
docker compose down -v            # removes postgres volume
docker compose up -d postgres
npm run db:migrate:postgres
```

## Troubleshooting

**`predev` build:db-worker fails** — ensure `src/lib/db/postgres-worker.ts` has no TypeScript errors.

**Database connection refused** — confirm `docker compose ps` shows postgres as healthy and `.env` `DATABASE_URL` points to `127.0.0.1:5432`.

**Test failures with postgres backend** — confirm postgres container is running and `npm run db:migrate:postgres` has been run recently.