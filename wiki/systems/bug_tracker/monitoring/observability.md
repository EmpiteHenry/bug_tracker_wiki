---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 18
tags: [monitoring, observability, alerts, performance, logging]
status: active
---

# Monitoring & Observability

The monitoring subsystem lives in `src/lib/monitoring/`. It covers structured logging, error grouping, operational alerts, endpoint performance, and client-side page load metrics.

## Request Observability (`request-observability.ts`)

`withRequestObservability(method, handler)` wraps every Next.js route handler. It:

1. Records request start time
2. Calls the handler
3. Writes an `ObservabilityEvent` (method, path, status, duration) to the log store
4. Catches unhandled errors, logs them, and returns 500

All API routes use this wrapper — it is the single chokepoint for request-level telemetry.

## Observability Events (`observability-event.ts`)

`ObservabilityEvent` is the core structured log record. Fields include severity, event type, metadata map, and timestamp.

Redaction is applied before storage via `observability-redaction.ts` to strip sensitive fields (passwords, tokens) from logged request bodies.

## Log Storage

Two sinks:

- **Database sink** (`operational-log-store.ts`): writes to the `operational_logs` table in PostgreSQL. Queryable by severity, time range, and source via the admin API.
- **File sink** (`observability-file-sink.ts`): appends NDJSON to `log/observability.ndjson`. Useful for local inspection without a DB connection.

Valid severity levels: `debug | info | warn | error | critical`.

## Error Fingerprinting (`error-fingerprint.ts`)

Errors are normalised to a fingerprint (stack trace hash + message normalisation). `grouped-errors-service.ts` groups occurrences by fingerprint to surface recurring errors in the admin UI without flooding it with duplicate entries.

Admins can link an error group to a bug via `PATCH /api/admin/errors/[fingerprint]/bug`.

## Operational Alerts (`operational-alert-service.ts`, `operational-alert-store.ts`)

Alerts are distinct from log entries — they represent actionable conditions that need human review.

Alert fields: severity, status (`open | acknowledged | resolved`), source, `latestSeenAt`.

Admins can:
- List alerts with filters (severity, status, source, time range)
- Update alert status
- Create a bug directly from an alert via `createBugFromOperationalAlert`

Alert CLI: `scripts/alerts-cli.ts` (run via `npm run alerts:cli`).

## Dashboard Summary (`dashboard-summary-service.ts`)

`getDashboardSummary()` aggregates metrics for the admin dashboard: open alert counts by severity, recent error counts, active organization count, etc.

## Endpoint Performance (`endpoint-performance-service.ts`)

`getEndpointPerformance(window)` returns p50/p95/p99 latencies per route, computed from the observability log. The `window` parameter controls the lookback period. Exposed at `GET /api/admin/performance/endpoints`.

## Org Usage (`org-usage-service.ts`)

`getOrganizationUsageStats()` returns per-organization bug counts and activity metrics. Exposed at `GET /api/admin/performance/org-usage`.

## Page Load Observability

Client-side page load timing is captured by `src/components/app/page-load-observability.tsx`, which posts to `POST /api/observability/page-load`. The server handler writes to `observability-logger-service.ts`.

## Alert Filters (`alert-list-filters.ts`)

`MonitoringAlertFilterState` covers: severity, status, source, latestSeenFrom, latestSeenTo, limit. Date values are `datetime-local` strings converted to ISO 8601 before the API call.