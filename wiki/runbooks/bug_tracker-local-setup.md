---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 1
tags: [setup, local, docker, postgresql, onboarding]
status: active
---

# Local Development Setup

## Prerequisites

- Node.js
- npm
- Docker + Docker Compose

## First-Time Setup

```bash
# 1. Clone and enter repo
cd bug_tracker

# 2. Copy environment file
cp .env.example .env

# 3. Install dependencies
npm install

# 4. Start PostgreSQL via Docker
docker compose up -d postgres

# 5. Apply database migrations
npm run db:migrate:postgres

# 6. Start dev server
npm run dev
```

Open http://localhost:3000.

## Environment Variables

`.env.example` contains defaults for local PostgreSQL (`127.0.0.1:5432`). Do not change unless you have a reason.

SendGrid variables can be left empty unless testing email delivery.

## Running Tests

```bash
# Fast unit tests (in-memory DB, no Docker needed)
npm run test

# PostgreSQL-backed unit tests (requires Docker postgres running)
npm run test:unit:postgres

# E2E tests (requires seeded test DB)
npm run test:e2e:seed
npm run test:e2e
```

## Other Useful Commands

```bash
npm run lint          # ESLint
npm run typecheck     # TypeScript check
npm run build         # Production build
npm run start         # Run production server locally

# Tooling
npm run bugs:cli      # Agent CLI
npm run alerts:cli    # Alerts CLI
npm run mcp:server    # MCP server
```

## Nginx / Domain Setup

See `docs/deployment-nginx.md` for reverse-proxy and domain-based access.

## Observability

See `docs/observability.md` for structured logging and file output configuration.
