---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [deployment, setup, postgresql, nginx]
status: active
---

# Deployment & Setup

## Local Development (First Run)

```bash
# 1. Clone and install
cp .env.example .env
npm install

# 2. Start PostgreSQL
docker compose up -d postgres

# 3. Run migrations
npm run db:migrate:postgres

# 4. Start dev server (predev builds the DB worker automatically)
npm run dev
# → http://localhost:3000
```

The `predev` hook runs `build:db-worker` before the Next.js dev server starts.

## Environment Variables

Copy `.env.example` to `.env`. Key variables:

| Variable | Required | Notes |
|---|---|---|
| `DATABASE_URL` | Yes | PostgreSQL connection string (`127.0.0.1:5432` default) |
| `SENDGRID_API_KEY` | Optional | Leave empty for local dev without email |
| `SESSION_SECRET` | Yes | Secret for signing session cookies |
| `NEXT_PUBLIC_APP_URL` | Yes | Base URL used in email links |
| `BUG_TRACKER_TEST_DB_BACKEND` | Test only | `memory` (default for tests) or `postgres` |

## Production Build

```bash
npm run build          # Next.js build + DB worker bundle
npm run start          # Start production server
```

The build produces:
- `.next/` — Next.js output
- `.next/db/postgres-worker.mjs` — standalone PostgreSQL worker

## Nginx Reverse Proxy

See `docs/deployment-nginx.md` for domain-based setup with Nginx in front of `next start`.

Typical configuration proxies `localhost:3000` and handles:
- TLS termination
- Static asset caching
- WebSocket upgrade (if needed)

## Docker Compose

`compose.yaml` defines at minimum a `postgres` service. The app itself is not containerized by default — it runs directly on the host via `npm run start`.

## Database Migrations

Migrations are applied manually before starting the app:

```bash
npm run db:migrate:postgres
```

There is no auto-migration on startup. Run this after every deployment that includes schema changes.

## CLI Tools (scripts/)

| Script | Command | Purpose |
|---|---|---|
| `scripts/bug-agent-cli.ts` | `npm run bugs:cli` | Agent CLI for bug operations |
| `scripts/alerts-cli.ts` | `npm run alerts:cli` | Alerts management CLI |
| `scripts/mcp-server.ts` | `npm run mcp:server` | Start MCP server for LLM agent integration |

## Observability Output

Structured logs are written to `log/observability.ndjson` (NDJSON format). See `docs/observability.md` and the [Monitoring page](../monitoring.md) for details.