---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [deployment, production, nginx]
status: active
---

# Production Deployment

## Build & Start

```bash
npm run build   # Next.js production build + DB worker bundle
npm run start   # Start production server on port 3000
```

The build step produces two artifacts:
- `.next/` — Next.js app
- `.next/db/postgres-worker.mjs` — standalone PostgreSQL worker thread

Both must be present for the app to start correctly.

## Environment

Copy and configure `.env` for production:

| Variable | Notes |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string |
| `SESSION_SECRET` | Strong random secret (min 32 chars) |
| `SENDGRID_API_KEY` | Required for email delivery |
| `NEXT_PUBLIC_APP_URL` | Full public URL (used in email links) |

## Database Migrations

Run migrations before starting the app after every release that changes the schema:

```bash
npm run db:migrate:postgres
```

Migrations are not applied automatically on startup.

## Nginx Configuration

See `docs/deployment-nginx.md` for the full Nginx setup. The standard pattern:

```nginx
server {
    listen 443 ssl;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## Admin Bootstrap

On first deployment, bootstrap the admin user:

```bash
# See src/lib/db/bootstrap-admin.ts for the mechanism
# Typically run once via a migration or startup script
```

## Checking Health

- Admin dashboard: `/admin/dashboard`
- Operational logs: `/admin/monitoring`
- `log/observability.ndjson` — structured log file for external ingestion