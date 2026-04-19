---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [monitoring, observability, alerts, performance, logging]
status: active
---

# Monitoring & Observability

## Request Observability

Every API route is wrapped with `withRequestObservability(method, handler)` from `src/lib/monitoring/request-observability.ts`. This middleware records:
- HTTP method, path, status code
- Duration
- Errors (with fingerprint for grouping)

Output goes to `operational-log-store.ts` (DB) and optionally `observability-file-sink.ts` (NDJSON file at `log/observability.ndjson`).

Page load events are tracked via `page-load-observability.ts` and the `PageLoadObservability` component, reported to `POST /api/observability/page-load`.

## Operational Logs

Admin endpoint: `GET /api/admin/logs`

Queryable by severity: `debug | info | warn | error | critical`  
Results are paginated (`PaginatedLogQueryOptions`).

## Error Grouping

Errors are fingerprinted via `error-fingerprint.ts` and grouped by `grouped-errors-service.ts`.

Admin endpoints:
- `GET /api/admin/errors` — grouped error summary (severity filter: `error | critical`)
- `PATCH /api/admin/errors/[fingerprint]/bug` — link an error group to an existing bug

## Operational Alerts

Alerts are raised when monitored conditions breach thresholds.

| API | Description |
|---|---|
| `GET /api/admin/alerts` | List alerts (filter by severity, status, source, date range, limit) |
| `GET /api/admin/alerts/[id]` | Alert detail |
| `PATCH /api/admin/alerts/[id]` | Update alert status |
| `POST /api/admin/alerts/[id]/bugs` | Create a bug from an alert |

Alert statuses and the alert CLI are implemented in `operational-alert-service.ts` and `operational-alert-cli.ts`.

## Performance Tracking

- **Endpoint performance**: `GET /api/admin/performance/endpoints` — latency by endpoint over a configurable window
- **Org usage stats**: `GET /api/admin/performance/org-usage` — per-organization usage metrics

Both call services in `src/lib/monitoring/`.

## Dashboard Summary

`GET /api/admin/dashboard/summary` returns aggregated counts (bugs, alerts, errors) for the admin dashboard home.

## Data Redaction

`observability-redaction.ts` strips sensitive fields (passwords, tokens) from log payloads before persistence.
