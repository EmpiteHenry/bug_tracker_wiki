---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [deployment, nginx, production]
status: active
---

# Deployment

## Production Build

```bash
npm run build
# Runs: next build --webpack && build:db-worker
# Output: .next/ + .next/db/postgres-worker.mjs
```

## Start Production Server

```bash
npm run start
# Runs: next start
```

## Environment Variables

Ensure all required env vars are set before starting. Key variables (see `.env.example`):

| Variable | Purpose |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string |
| `SENDGRID_API_KEY` | Email delivery (optional for non-email flows) |
| `SESSION_SECRET` | Cookie signing secret |

## Database Migrations

Apply migrations before each deployment that includes schema changes:

```bash
npm run db:migrate:postgres
```

Migrations run the script at `scripts/migrate-postgres.ts` against the `DATABASE_URL` in `.env`.

## Nginx / Reverse Proxy

For domain-based deployments with HTTPS termination and reverse proxy, follow the guide in [`docs/deployment-nginx.md`](../../docs/deployment-nginx.md).

## Observability Output

The app writes structured NDJSON logs to `log/observability.ndjson`. Configure log rotation and forwarding as described in [`docs/observability.md`](../../docs/observability.md).

## run.sh

A `run.sh` script is present at the repo root. This is likely a convenience wrapper for production start commands — review its contents before deploying to ensure it matches your environment.