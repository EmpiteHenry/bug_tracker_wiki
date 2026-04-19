---
type: runbook
owner: engineering
last_updated: 2026-04-19
source_count: 2
tags: [runbook, deployment, production, nginx]
status: active
---

# Runbook: Production Deployment

Full deployment details are in `docs/deployment-nginx.md` in the repository. This runbook summarizes the key steps.

## Build

```bash
# Installs deps, builds Next.js app, and bundles the DB worker
npm install
npm run build
```

The build produces:
- `.next/` — Next.js production bundle
- `.next/db/postgres-worker.mjs` — standalone DB worker (bundled by esbuild)

## Environment

Set all required environment variables (do not use `.env.example` directly in production):

```
POSTGRES_HOST=<host>
POSTGRES_PORT=5432
POSTGRES_DB=<db>
POSTGRES_USER=<user>
POSTGRES_PASSWORD=<secret>
SENDGRID_API_KEY=<key>
SESSION_SECRET=<secret>
```

## Database Migration

Run migrations before starting the app server:

```bash
node --env-file=.env --import tsx scripts/migrate-postgres.ts
```

## Start Server

```bash
npm run start
```

The Next.js production server starts on port 3000 by default.

## Nginx

For domain-based access and TLS termination, configure Nginx as a reverse proxy pointing to `localhost:3000`. See `docs/deployment-nginx.md` for the full Nginx config template.

## Health Check

Verify the app is up:
```bash
curl http://localhost:3000/api/auth/session
# Expect: {"error":"..."} or {"user":...} — either means the server is responding
```

## Agent / CLI Tools

The scripts require `tsx` and access to `.env`:

```bash
# Bug agent CLI
node --env-file=.env ./node_modules/.bin/tsx scripts/bug-agent-cli.ts

# Alerts CLI
node --env-file=.env ./node_modules/.bin/tsx scripts/alerts-cli.ts

# MCP server
node --env-file=.env ./node_modules/.bin/tsx scripts/mcp-server.ts
```