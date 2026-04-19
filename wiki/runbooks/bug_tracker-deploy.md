---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [deployment, nginx, production, aws, ec2]
status: active
---

# Deployment

## Production Build

```bash
npm run build
npm run start
```

`npm run build` compiles Next.js and also builds the PostgreSQL worker bundle:
```
.next/db/postgres-worker.mjs
```

This worker must be present at runtime; the `predev` script also builds it before `npm run dev`.

## Nginx / Reverse Proxy

See `docs/deployment-nginx.md` for full nginx configuration, including:
- Domain-based access
- SSL termination
- Proxy pass to Next.js

## AWS EC2 Deployment

A PRD for AWS EC2 deployment exists at `tasks/prd-aws-ec2-deployment.md`. Refer to it for planned infrastructure setup.

## Database

Before first run against a new PostgreSQL instance:

```bash
npm run db:migrate:postgres
```

Bootstrap admin user via `src/lib/db/bootstrap-admin.ts` (run once after migrations).

## Environment Variables Required for Production

| Variable | Purpose |
|---|---|
| `POSTGRES_*` | PostgreSQL connection |
| `SENDGRID_API_KEY` | Email delivery |
| Session secret | Cookie signing (see `session.ts`) |
