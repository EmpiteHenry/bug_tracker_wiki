---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [monitoring, observability, alerts, performance, logging, errors]
status: active
---

# Monitoring & Observability

## Overview

The monitoring subsystem captures structured operational events, groups errors by fingerprint, tracks endpoint performance, and surfaces operational alerts.

## Request Observability

Every API route is wrapped with `withRequestObservability(method, handler)` from `src/lib/monitoring/request-observability.ts`.

This wrapper:
- Logs request timing
- Captures errors with context
- Emits structured NDJSON to `log/observability.ndjson`

Client-side page load timing is reported to `POST /api/observability/page-load` and handled by the `PageLoadObservability` component.

## Structured Logging

Events are written via `observability-logger-service.ts` to a file sink (`observability-file-sink.ts`).

Log format: NDJSON at `log/observability.ndjson`.

Sensitive fields are redacted before logging via `observability-redaction.ts`.

Severity levels: `debug` | `info` | `warn` | `error` | `critical`

## Error Grouping

Errors are grouped by fingerprint using `error-fingerprint.ts`. The `grouped-errors-service.ts` provides:

- `getGroupedErrors()` — aggregated error clusters
- `getErrorGroupEntries()` — individual log entries within a group

Valid severities for error grouping: `error` | `critical`

Accessible via admin panel at `/admin/monitoring` and `GET /api/admin/errors`.

## Operational Alerts

Alerts represent actionable conditions that may require human response.

| Concept | File |
|---|---|
| Alert CRUD | `operational-alert-store.ts` |
| Business logic | `operational-alert-service.ts` |
| CLI tool | `operational-alert-cli.ts` |

Filters: `severity`, `status`, `source`, `latestSeenFrom`, `latestSeenTo`, `limit` (default 25).

Alerts can be **linked to bugs** via `POST /api/admin/alerts/[alertId]/bugs` — this calls `createBugFromOperationalAlert()`.

Alert status can be updated via `PATCH /api/admin/alerts/[alertId]`.

## Endpoint Performance

`endpoint-performance-service.ts` aggregates response time metrics by endpoint. Supports time window filtering via `isValidWindow()`.

Accessible at `GET /api/admin/performance/endpoints`.

## Dashboard Summary

`dashboard-summary-service.ts` provides aggregated metrics for the admin dashboard at `GET /api/admin/dashboard/summary`.

## Organisation Usage

`org-usage-service.ts` provides per-org usage statistics at `GET /api/admin/performance/org-usage`.

## Observability Event Model

Defined in `observability-event.ts` — the canonical shape of a logged event flowing through the system.