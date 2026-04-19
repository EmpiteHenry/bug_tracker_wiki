---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [observability, logging, monitoring]
status: active
---

# Observability Operations

> Detailed setup in `docs/observability.md` in the repository.

## Log File

Structured NDJSON logs are written to `log/observability.ndjson` by `observability-file-sink.ts`.

Each line is a JSON event with fields including severity, timestamp, and request metadata.

## Viewing Logs (Admin UI)

`/admin/monitoring` → Logs tab — paginated log viewer with severity filter.

## Viewing Errors (Admin UI)

`/admin/monitoring` → Errors tab — grouped errors by fingerprint. Click an error group to view individual entries or link to a bug.

## Viewing Alerts (Admin UI)

`/admin/monitoring` → Alerts tab — operational alerts with filter state (`severity`, `status`, `source`, date range, `limit`).

Alert actions:
- Update status (`PATCH /api/admin/alerts/[id]`)
- Create bug from alert (`POST /api/admin/alerts/[id]/bugs`)

## Performance Dashboard

`/admin/monitoring` → Performance tab — endpoint latency by window, org usage stats.

## Sensitive Data

`observability-redaction.ts` strips sensitive fields before any log is persisted or transmitted. Do not bypass this layer when adding new log events.