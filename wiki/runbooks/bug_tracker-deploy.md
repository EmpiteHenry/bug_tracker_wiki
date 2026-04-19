---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 1
tags: [deployment, nginx, production]
status: active
---

# Deployment

> Full deployment walkthrough is in `docs/deployment-nginx.md` in the repository.

## Build

```bash
npm run build
```

This runs:
1. `next build --webpack` — Next.js production build
2. `build:db-worker` — bundles `postgres-worker.ts` to `.next/db/postgres-worker.mjs`

Both artifacts are required for production.

## Start

```bash
npm run start
```

Runs `next start` — serves the production build.

## Nginx

The repo includes an Nginx-based reverse proxy setup documented in `docs/deployment-nginx.md`. This handles:
- Domain-based routing
- SSL termination
- Proxying to `next start`

## CI Script

```bash
npm run ci          # full local CI (lint + typecheck + test + build)
npm run ci:quick    # skips slower checks
npm run ci:e2e      # includes Playwright e2e
```

## Database

Run migrations before starting the app on a new environment:

```bash
npm run db:migrate:postgres
```

Bootstrap admin user (first deploy):

See `src/lib/db/bootstrap-admin.ts`.