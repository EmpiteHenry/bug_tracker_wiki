---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [deployment, nginx, production, build]
status: active
---

# Runbook: Deployment

## Production Build

```bash
npm run build
```

This runs:
1. `next build --webpack` — builds the Next.js app
2. `npm run build:db-worker` — bundles the PostgreSQL worker to `.next/db/postgres-worker.mjs`

## Start Production Server

```bash
npm run start
```

Requires `.env` with production PostgreSQL credentials and SendGrid keys.

## Apply Migrations on Deploy

```bash
npm run db:migrate:postgres
```

Run this against the production database before starting the new server version.

## Nginx Reverse Proxy

For domain-based access and Nginx setup, see:
```
docs/deployment-nginx.md
```
(in the repository root)

## Observability File Output

Structured NDJSON logs are written to `log/observability.ndjson`. Configure log rotation or shipping from this path. See:
```
docs/observability.md
```

## run.sh

The repository includes a `run.sh` convenience script for production startup. Review its contents before using in a new environment to ensure it matches your deployment target.

## AWS EC2 Deployment

See `tasks/prd-aws-ec2-deployment.md` for the planned EC2 deployment PRD.