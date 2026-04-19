---
type: runbook
owner: engineering
last_updated: 2026-04-19
source_count: 2
tags: [runbook, local, setup, onboarding]
status: active
---

# Runbook: Local Development Setup

## Prerequisites

Install before starting:
- Node.js (LTS)
- npm
- Docker Desktop (for PostgreSQL)

## Steps

### 1. Clone and configure environment

```bash
cd bug_tracker
cp .env.example .env
# Edit .env if you need non-default PostgreSQL settings
```

### 2. Install dependencies

```bash
npm install
```

### 3. Start PostgreSQL

```bash
docker compose up -d postgres
```

Verify it's running:
```bash
docker compose ps
```

### 4. Run migrations

```bash
npm run db:migrate:postgres
```

### 5. Start development server

```bash
npm run dev
```

The `predev` script automatically builds the DB worker bundle first.

### 6. Open the app

Navigate to `http://localhost:3000`. Create an account on first run — this creates your user and organization.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Migration fails with connection refused | Check Docker is running: `docker compose up -d postgres` |
| `Cannot find module '.next/db/postgres-worker.mjs'` | Run `npm run build:db-worker` manually |
| TypeScript errors on startup | Run `npm run typecheck` to see full diagnostics |
| Email not sending | Leave SendGrid vars empty for local dev; emails are logged but not sent |

## Running Tests

```bash
# Fast unit tests (in-memory DB, no Docker needed)
npm run test

# PostgreSQL-backed unit tests (needs Docker running)
npm run test:unit:postgres

# E2E tests
npm run test:e2e:seed
npm run dev:playwright   # in separate terminal
npm run test:e2e
```

## Related

- [System overview](../systems/bug_tracker/overview.md)
- `docs/deployment-nginx.md` for reverse-proxy setup
- `docs/observability.md` for log file locations