---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 16
tags: [monitoring, observability, alerts, performance, logging]
status: active
---

# Monitoring & Observability

## Request Observability

All API routes are wrapped with `withRequestObservability("METHOD", handler)` from `src/lib/monitoring/request-observability.ts`. This middleware logs structured request/response events.

## Operational Logs

`src/lib/monitoring/operational-log-service.ts` + `operational-log-store.ts`

Severity levels: `debug`, `info`, `warn`, `error`, `critical`.

Admin endpoint: `GET /api/admin/logs` — paginated log query with severity filter.

## Error Grouping

`src/lib/monitoring/grouped-errors-service.ts` — groups `error` and `critical` log entries by fingerprint.

`src/lib/monitoring/error-fingerprint.ts` — fingerprint algorithm for deduplication.

Admin endpoint: `GET /api/admin/errors` — returns grouped error clusters with entry detail.

`PATCH /api/admin/errors/[fingerprint]/bug` — links a grouped error to a bug (sets `projectId`).

## Operational Alerts

`src/lib/monitoring/operational-alert-service.ts` + `operational-alert-store.ts`

Alerts have `severity`, `status`, `source`, `latestSeen` timestamp.

Alert filter state (`alert-list-filters.ts`): `severity`, `status`, `source`, `latestSeenFrom`, `latestSeenTo`, `limit`.

Admin endpoints:
- `GET /api/admin/alerts` — list alerts with filters
- `GET /api/admin/alerts/[alertId]` — alert detail
- `PATCH /api/admin/alerts/[alertId]` — update alert status
- `POST /api/admin/alerts/[alertId]/bugs` — create bug from alert via `createBugFromOperationalAlert()`

Alert CLI: `npm run alerts:cli` → `scripts/alerts-cli.ts`

## Performance Monitoring

`src/lib/monitoring/endpoint-performance-service.ts` — tracks per-endpoint latency.  
`src/lib/monitoring/performance.ts` — performance measurement utilities.

Admin endpoints:
- `GET /api/admin/performance/endpoints?window=...` — endpoint latency aggregates
- `GET /api/admin/performance/org-usage` — per-org usage stats from `org-usage-service.ts`

## Page Load Observability

`src/lib/monitoring/page-load-observability.ts` + `src/components/app/page-load-observability.tsx`

Client sends timing data to `POST /api/observability/page-load`.

## File Sink

`src/lib/monitoring/observability-file-sink.ts` — writes NDJSON to `log/observability.ndjson`.  
See [observability runbook](../../../runbooks/bug_tracker-observability.md) for details.

## Redaction

`src/lib/monitoring/observability-redaction.ts` — strips sensitive fields before logging.

## Dashboard Summary

`GET /api/admin/dashboard/summary` → `getDashboardSummary()` — aggregated admin home stats.