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
npm run build        # Next.js build + esbuild postgres-worker
npm run start        # Start production server
```

The `build` script also compiles the PostgreSQL worker to `.next/db/postgres-worker.mjs`.

## Nginx / Reverse Proxy

Full reverse-proxy and domain setup is documented in `docs/deployment-nginx.md` in the repository. This covers:

- Nginx configuration for the Next.js server
- Domain and SSL setup
- Port forwarding

## Observability in Production

Structured logs are written to `log/observability.ndjson` (NDJSON format) via `observability-file-sink.ts`. See `docs/observability.md` for log rotation, monitoring integration, and alerting setup.

## Database

Run migrations against the production database before deploying a new version:

```bash
NODE_ENV=production npm run db:migrate:postgres
```

Ensure `DATABASE_URL` (or `POSTGRES_*` vars) points to the production instance.

## Admin Bootstrap

On first deployment, `bootstrap-admin.ts` seeds the initial admin account from environment variables. Run once after the first migration.

## run.sh

`run.sh` in the repo root is a convenience wrapper for production start. Review its contents before use in a new environment.