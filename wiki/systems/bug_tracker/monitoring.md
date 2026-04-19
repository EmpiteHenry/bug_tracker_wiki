---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 14
tags: [monitoring, observability, alerts, performance, logging]
status: active
---

# Monitoring & Observability

The monitoring subsystem provides structured logging, error grouping, performance tracking, and operational alerting — all scoped to the running app instance.

## Request Observability

Every API route handler is wrapped with `withRequestObservability("METHOD", handler)` from `src/lib/monitoring/request-observability.ts`. This middleware:
- Records request timing and status
- Logs structured events to the operational log
- Redacts sensitive fields before logging (see `observability-redaction.ts`)

## Structured Logging

**Service**: `observability-logger-service.ts`  
**Store**: `operational-log-store.ts`  
**File sink**: `observability-file-sink.ts` — writes NDJSON to `log/observability.ndjson`

Log severity levels: `debug | info | warn | error | critical`

Page-load events are captured client-side and posted to `/api/observability/page-load`.

## Error Grouping

Errors are fingerprinted by `error-fingerprint.ts` and grouped in `grouped-errors-service.ts`. The admin errors view shows deduplicated error groups with occurrence counts. Each group can be linked to a bug via `PATCH /api/admin/errors/[fingerprint]/bug`.

## Operational Alerts

Alerts represent notable events requiring human review (e.g., repeated errors, threshold breaches).

- **Store**: `operational-alert-store.ts` — persists alerts with status tracking
- **Service**: `operational-alert-service.ts` — list, update status, create bug from alert
- **CLI**: `operational-alert-cli.ts` / `scripts/alerts-cli.ts` (`npm run alerts:cli`)

Alert statuses: `open | acknowledged | resolved`

## Performance Metrics

`endpoint-performance-service.ts` computes latency statistics per endpoint over configurable time windows. Exposed at `GET /api/admin/performance/endpoints`.

Per-organization usage stats are in `org-usage-service.ts`.

## Dashboard Summary

`dashboard-summary-service.ts` aggregates counts across bugs, alerts, and users for the admin dashboard (`GET /api/admin/dashboard/summary`).

## Observability Docs

See [`docs/observability.md`](../../../docs/observability.md) for file output paths and structured log format details.