---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 18
tags: [monitoring, observability, alerts, logging, performance]
status: active
---

# Monitoring & Observability

## Request Observability

All API routes are wrapped with `withRequestObservability(method, handler)` from `src/lib/monitoring/request-observability.ts`. This middleware captures:

- HTTP method, route, status code
- Response time
- Errors and their fingerprints

Structured events are written via `observability-logger-service.ts` to the operational log store and optionally to `observability-file-sink.ts` (NDJSON at `log/observability.ndjson`).

## Operational Logs

`src/lib/monitoring/operational-log-store.ts` persists structured log entries. Severity levels: `debug`, `info`, `warn`, `error`, `critical`.

Admin API: `GET /api/admin/logs` (paginated, filterable by severity).

## Error Grouping

`src/lib/monitoring/error-fingerprint.ts` computes a deterministic fingerprint per error (stack, message, route). `grouped-errors-service.ts` aggregates log entries by fingerprint into error groups.

Admin API: `GET /api/admin/errors` (filtered to `error`/`critical` severity by default).

## Operational Alerts

`src/lib/monitoring/operational-alert-store.ts` and `operational-alert-service.ts` manage discrete alerts with severity and status. Alerts can be promoted to bugs via `createBugFromOperationalAlert`.

Admin API:
- `GET /api/admin/alerts` — list with filters (severity, status, source, date range)
- `GET /api/admin/alerts/:alertId` — detail
- `PATCH /api/admin/alerts/:alertId` — update status
- `POST /api/admin/alerts/:alertId/bugs` — create bug from alert

CLI: `npm run alerts:cli` → `operational-alert-cli.ts`

## Performance Tracking

`src/lib/monitoring/performance.ts` records timing samples. `endpoint-performance-service.ts` aggregates by endpoint over configurable windows.

Admin API: `GET /api/admin/performance/endpoints`

## Page Load Observability

Client-side page load events are recorded via `components/app/page-load-observability.tsx` and sent to `POST /api/observability/page-load`. Server-side processing in `page-load-observability.ts`.

## Organisation Usage

`org-usage-service.ts` computes per-org usage statistics.

Admin API: `GET /api/admin/performance/org-usage`

## Admin Dashboard Summary

`dashboard-summary-service.ts` aggregates key metrics into a single summary payload.

Admin API: `GET /api/admin/dashboard/summary`

## Data Redaction

`observability-redaction.ts` strips sensitive fields (passwords, tokens) before log events are persisted.