---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [deployment, nginx, production, operations]
status: active
---

# Deployment

## Overview

The app runs as a standard Next.js production server behind an Nginx reverse proxy.

Full deployment details are in the repository at `docs/deployment-nginx.md`.

## Production Start

```bash
# Build application + DB worker bundle
npm run build

# Start production server
npm run start
```

## Database Migrations

Run migrations against production PostgreSQL before starting the app:

```bash
node --env-file=.env --import tsx scripts/migrate-postgres.ts
```

Or via npm script (requires `.env`):

```bash
npm run db:migrate:postgres
```

## Nginx Setup

See `docs/deployment-nginx.md` for:
- Domain-based routing configuration
- SSL termination
- Reverse proxy to `http://localhost:3000`
- Named tunnel setup via `tunnel-cli` (Cloudflare Tunnel)

## Exposing Locally via Tunnel

Use `tunnel-cli` (at `~/RanjanWork/tunnel-cli`) to expose the local dev server:

```bash
tunnel expose 3000       # Quick HTTPS URL (trycloudflare.com)
tunnel start myapp 3000  # Stable named tunnel (requires Cloudflare login)
```

## Observability in Production

Structured logs written to `log/observability.ndjson`.

See `docs/observability.md` for log rotation, monitoring configuration, and alerting setup.

## Admin Bootstrap

First run: `src/lib/db/bootstrap-admin.ts` seeds the initial admin user. Configure credentials via environment variables before first boot.