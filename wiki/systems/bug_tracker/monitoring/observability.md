---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 18
tags: [monitoring, observability, logging, alerts, performance]
status: active
---

# Observability & Monitoring

## Request Observability

Every API route is wrapped with `withRequestObservability(method, handler)` from `src/lib/monitoring/request-observability.ts`. This middleware:

- Records request start/end timestamps and HTTP status
- Logs structured NDJSON entries to `log/observability.ndjson` via the file sink
- Captures unhandled errors and re-throws after logging

The client-side counterpart (`src/components/app/page-load-observability.tsx`) sends page load timing metrics to `/api/observability/page-load`.

## Structured Logging

`src/lib/monitoring/observability-logger-service.ts` provides the logging interface. Entries are typed via `src/lib/monitoring/observability-event.ts`.

**Sinks:**
- File sink: `src/lib/monitoring/observability-file-sink.ts` — appends NDJSON to `log/observability.ndjson`
- Deployment docs: see [`docs/observability.md`](../../../docs/observability.md)

**Sensitive fields are redacted** before writing: `src/lib/monitoring/observability-redaction.ts`.

## Error Grouping

`src/lib/monitoring/error-fingerprint.ts` derives a stable fingerprint from error stack traces. `grouped-errors-service.ts` aggregates log entries by fingerprint for the admin error browser (`/admin/monitoring`).

Admins can link an error group to a bug/project via `PATCH /api/admin/errors/[fingerprint]/bug`.

## Operational Alerts

Alerts are persistent records derived from log patterns or triggered manually:

- Store: `src/lib/monitoring/operational-alert-store.ts`
- Service: `src/lib/monitoring/operational-alert-service.ts`
- CLI: `src/lib/monitoring/operational-alert-cli.ts` (invoked via `npm run alerts:cli`)
- Alert severities and statuses match those in `OperationalAlertStatus` type

Admins can:
- List / filter alerts (`GET /api/admin/alerts`)
- Update alert status (`PATCH /api/admin/alerts/[alertId]`)
- Promote an alert to a tracked bug (`POST /api/admin/alerts/[alertId]/bugs`)

## Performance Tracking

`src/lib/monitoring/performance.ts` and `endpoint-performance-service.ts` aggregate per-endpoint latency statistics exposed at `GET /api/admin/performance/endpoints`.

Per-org usage stats (bug counts, seat usage) are served from `org-usage-service.ts` at `GET /api/admin/performance/org-usage`.

## Admin Dashboard

`dashboard-summary-service.ts` computes the summary metrics shown on the admin dashboard (`/admin/dashboard`), combining bug counts, user counts, and recent alert activity.