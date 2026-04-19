---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [deployment, production, nginx, build]
status: active
---

# Deployment

## Production Build

```bash
# Build Next.js app + DB worker bundle
npm run build

# Start production server
npm run start
```

The build step runs two commands:
1. `next build --webpack` — compiles the Next.js application
2. `build:db-worker` — bundles `postgres-worker.ts` into `.next/db/postgres-worker.mjs` via esbuild

Both must succeed before starting the production server.

## Environment

Set all required environment variables before starting. At minimum:
- PostgreSQL connection details
- Session secret / cookie config
- SendGrid credentials (if email is required in production)

## Database Migrations

Run migrations before starting the new app version:

```bash
npm run db:migrate:postgres
```

This uses `node --env-file=.env` so the `.env` file must be present and contain valid PostgreSQL credentials.

## Nginx Reverse Proxy

See `docs/deployment-nginx.md` in the repository for:
- Domain-based access configuration
- SSL termination
- Proxy pass to `localhost:3000`
- Static asset caching headers

## CI Pipeline

```bash
# Full local CI (lint + typecheck + tests + build)
npm run ci

# Quick CI (skip E2E)
npm run ci:quick

# With E2E
npm run ci:e2e
```

Scripts are in `scripts/local-ci.sh`.

## AWS EC2 Deployment

See `tasks/prd-aws-ec2-deployment.md` for the planned EC2 deployment specification.

## Observability in Production

Structured logs are written to `log/observability.ndjson`. See `docs/observability.md` for log rotation, alerting integration, and monitoring setup.

## run.sh

`run.sh` in the repo root is a convenience script for starting the application in a server environment. Review before use in production to confirm it matches your deployment target.