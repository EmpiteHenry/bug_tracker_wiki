---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [testing, sqlite, postgresql, test-strategy]
status: active
---

# ADR-003: Dual Database Backend for Testing

## Context

Integration tests that require a live PostgreSQL instance are slow to set up in CI and locally. But pure mocks would hide real SQL bugs.

## Decision

The database client is abstracted. Two backends are implemented:

- `memory` (SQLite in-process) — default for `npm test` and `npm run test:unit`
- `postgres` — used by `npm run test:unit:postgres` and E2E tests

Selection is via `BUG_TRACKER_TEST_DB_BACKEND` environment variable.

`postgres-test-reset.ts` truncates all tables between tests when running in postgres mode, ensuring test isolation without recreating the schema.

## Rationale

- `npm test` runs without Docker — zero-friction for all contributors
- `npm run test:unit:postgres` catches DB-specific bugs (e.g., constraint violations, query planner differences) when explicitly opted into
- E2E tests (`npm run test:e2e`) always use PostgreSQL because they test the full stack

## Status

Active.