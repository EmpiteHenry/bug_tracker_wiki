---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [observability, logging, monitoring]
status: active
---

# ADR-003: Built-In Observability Stack

## Decision

The app implements its own observability stack (operational logs, error fingerprinting, grouped errors, alerts, performance metrics) stored in PostgreSQL rather than relying on an external APM service.

## Rationale

**Why:** Keeps the deployment simple — no Datadog/Sentry/New Relic accounts required. The admin panel provides a first-class UI for logs, errors, and alerts without leaving the application.

**How to apply:** New API routes should always be wrapped with `withRequestObservability`. Significant business events should be logged via `ObservabilityLoggerService`. Do not add external APM agents without a clear decision to do so.

## Components

| Component | Purpose |
|---|---|
| `withRequestObservability` | Middleware wrapping all API handlers |
| `ObservabilityLoggerService` | Structured log emission |
| `ObservabilityFileSink` | NDJSON log file for external ingestion |
| `ObservabilityRedaction` | Strips PII/secrets before persistence |
| `ErrorFingerprint` | Stable hash for error deduplication |
| `GroupedErrorsService` | Aggregates errors by fingerprint |
| `OperationalAlertService` | Higher-level alert management |
| `EndpointPerformanceService` | Response time tracking |
| `PageLoadObservability` | Client-side metric collection |

## File Output

Logs are also written to `log/observability.ndjson` in NDJSON format, enabling external log shippers (Loki, CloudWatch, etc.) to ingest them without database access.