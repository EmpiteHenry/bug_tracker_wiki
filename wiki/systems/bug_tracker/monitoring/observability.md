---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 18
tags: [monitoring, observability, logging, alerts, performance]
status: active
---

# Monitoring & Observability

## Request Observability

Every API route is wrapped with `withRequestObservability(method, handler)` from `src/lib/monitoring/request-observability.ts`. This middleware:
- Records request method, path, duration, and HTTP status to the operational log store
- Redacts sensitive fields before storage (via `observability-redaction.ts`)
- Associates requests with organization context when available

## Operational Logs

Structured log entries stored in the database via `operational-log-store`.

**Severity levels**: `debug`, `info`, `warn`, `error`, `critical`

Admin access: `GET /api/admin/logs` — paginated, filterable by severity.

## Observability File Sink

In addition to the database, structured NDJSON logs are written to `log/observability.ndjson` via `observability-file-sink.ts`. This file is suitable for log aggregation tools.

## Error Grouping

The `grouped-errors-service.ts` groups error/critical log entries by error fingerprint (`error-fingerprint.ts`). Fingerprinting normalizes stack traces to identify recurring errors.

Admin access: `GET /api/admin/errors` — grouped error list with occurrence counts.

## Operational Alerts

Alerts are generated from error groups and external signals, stored in `operational-alert-store`.

**Alert fields**: severity, status, source, grouping key (fingerprint), latest seen timestamp.

Admin operations:
- `GET /api/admin/alerts` — list with filters (severity, status, source, date range, limit)
- `PATCH /api/admin/alerts/[alertId]` — update status (e.g., acknowledge, resolve)
- `POST /api/admin/alerts/[alertId]/bugs` — promote alert to a bug

Alert CLI: `npm run alerts:cli` → `scripts/alerts-cli.ts` → `operational-alert-cli.ts`

## Page Load Observability

Client-side page load timing is posted to `POST /api/observability/page-load` by the `PageLoadObservability` component (`src/components/app/page-load-observability.tsx`). Server processes via `page-load-observability.ts`.

## Performance Monitoring

`endpoint-performance-service.ts` aggregates request timing by endpoint over configurable time windows. Admin access: `GET /api/admin/performance/endpoints`.

`org-usage-service.ts` computes per-organization usage stats. Admin access: `GET /api/admin/performance/org-usage`.

## Dashboard Summary

`dashboard-summary-service.ts` aggregates key metrics for the admin dashboard. Admin access: `GET /api/admin/dashboard/summary`.

## Admin Monitoring Filters (`alert-list-filters.ts`)

| Filter | Type |
|---|---|
| `severity` | string |
| `status` | string |
| `source` | string |
| `latestSeenFrom` / `latestSeenTo` | datetime-local → ISO8601 |
| `limit` | integer (default 25) |