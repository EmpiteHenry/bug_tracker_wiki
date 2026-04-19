---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [monitoring, observability, alerting, logging]
status: active
---

# Monitoring & Observability

The app has a built-in observability stack that captures request telemetry, application logs, errors, and performance metrics without external APM dependencies.

## Components

### Request Observability (`withRequestObservability`)

Every API route handler is wrapped with `withRequestObservability(method, handler)`. This middleware:
- Records request start/end timestamps
- Captures HTTP method, path, status code
- Logs errors with context
- Writes to the operational log

### Operational Log

Structured log entries stored in the `operational_logs` table with severity levels:
`debug` | `info` | `warn` | `error` | `critical`

Also written to `log/observability.ndjson` via `ObservabilityFileSink` for external log ingestion.

### Error Fingerprinting

`src/lib/monitoring/error-fingerprint.ts` — normalizes stack traces and error messages into a stable fingerprint for grouping repeated errors. Enables:
- Deduplication of similar errors
- Trend tracking over time
- Linking error groups to bug records

### Grouped Errors

`GroupedErrorsService` aggregates error log entries by fingerprint, providing:
- Occurrence count
- First/last seen timestamps
- Sample stack trace

Accessible via admin API: `GET /api/admin/errors`

### Operational Alerts

Higher-level alerts derived from error patterns. States: `open` → `acknowledged` → `resolved`.

Alerts can be linked to bugs via `POST /api/admin/alerts/[alertId]/bugs`.

### Performance Metrics

`EndpointPerformanceService` tracks response time percentiles per API endpoint over configurable time windows. Available at `GET /api/admin/performance/endpoints`.

### Page Load Observability

Client-side performance metrics are posted to `POST /api/observability/page-load` and stored for admin review.

### Org Usage Stats

`OrgUsageService` tracks per-organization API usage. Available at `GET /api/admin/performance/org-usage`.

## Redaction

`ObservabilityRedaction` (`src/lib/monitoring/observability-redaction.ts`) strips sensitive fields (passwords, tokens, auth headers) before logs are persisted.

## Admin Dashboard

Summary of key system health metrics: `GET /api/admin/dashboard/summary`

UI: `/admin/monitoring` and `/admin/dashboard`