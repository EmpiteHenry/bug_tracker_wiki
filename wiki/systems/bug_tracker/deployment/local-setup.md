---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 2
tags: [deployment, local, docker, setup]
status: active
---

# Local Development Setup

See also: [runbook](../../../runbooks/bug_tracker-local-setup.md)

## Prerequisites

- Node.js
- npm
- Docker + Docker Compose

## Quick Start

```bash
cp .env.example .env
npm install
docker compose up -d postgres
npm run db:migrate:postgres
npm run dev
```

App available at `http://localhost:3000`.

## Environment Variables

Key variables from `.env.example`:

| Variable | Default | Required |
|---|---|---|
| `POSTGRES_HOST` | `127.0.0.1` | Yes |
| `POSTGRES_PORT` | `5432` | Yes |
| `POSTGRES_DB` | (set in example) | Yes |
| `POSTGRES_USER` | (set in example) | Yes |
| `POSTGRES_PASSWORD` | (set in example) | Yes |
| `SENDGRID_API_KEY` | (blank) | Only for email testing |

## npm Scripts Reference

| Command | Purpose |
|---|---|
| `npm run dev` | Dev server (runs `build:db-worker` first via `predev`) |
| `npm run build` | Production build + DB worker bundle |
| `npm run start` | Production server (after build) |
| `npm run lint` | ESLint |
| `npm run typecheck` | TypeScript check (clears `.next` first) |
| `npm run test` | Unit tests (in-memory DB) |
| `npm run test:unit:postgres` | Unit tests against real PostgreSQL |
| `npm run test:e2e` | Playwright E2E tests |
| `npm run bugs:cli` | Bug agent CLI |
| `npm run alerts:cli` | Alerts CLI |
| `npm run mcp:server` | MCP server for AI tooling |

## E2E Test Setup

```bash
npm run test:e2e:seed    # seed test DB
# dev server on port 3001:
npm run dev:playwright
npm run test:e2e
```

## Further Reading

- `docs/deployment-nginx.md` — Nginx reverse proxy setup
- `docs/observability.md` — Structured logging and observability