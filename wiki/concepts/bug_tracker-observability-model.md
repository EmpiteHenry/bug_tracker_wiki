---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [observability, logging, alerts, performance, monitoring]
status: active
---

# Observability Model

The observability model distinguishes three levels of operational data:

## 1. Operational Logs

Every HTTP request produces a log entry via `withRequestObservability`. Entries record: method, path, status code, duration, and any error details.

Sensitive fields are redacted before storage (via `observability-redaction.ts`) — passwords, tokens, and other configured field names are stripped from logged request bodies.

Logs are dual-written:
- **Database** (`operational_logs` table) — queryable by admins via `GET /api/admin/logs`
- **File** (`log/observability.ndjson`) — newline-delimited JSON for external log aggregators

Severity scale: `debug < info < warn < error < critical`.

## 2. Error Groups

Errors with stack traces are fingerprinted and grouped. A fingerprint is a stable identifier derived from the normalised stack trace + error message. Grouping prevents the admin error view from being flooded with identical errors.

Each group tracks: occurrence count, first/last seen, representative stack trace, and optionally a linked bug.

## 3. Operational Alerts

Alerts are higher-signal than log entries. They represent conditions requiring human attention. Unlike log entries (append-only), alerts have a mutable status: `open → acknowledged → resolved`.

Alerts include a severity (`info | warn | error | critical`) and a source tag. Admins can create bugs directly from alerts, creating a traceable link from operational signal to tracked work item.

## Client-Side Observability

Page load timing from the browser is posted to `POST /api/observability/page-load` and stored alongside server-side events. This gives end-to-end visibility: server request time + client render time.

## Performance Metrics

Endpoint performance is derived from the operational log — no separate APM agent. `getEndpointPerformance(window)` computes p50/p95/p99 from stored log entries within the time window. This means metrics are only as granular as the log retention period.

## Limitations

- Log retention is database-limited — no built-in rotation or archival policy
- Performance percentiles are computed at query time (no pre-aggregation) — may be slow on high-traffic instances with large log tables
- No distributed tracing (no trace IDs across service calls)