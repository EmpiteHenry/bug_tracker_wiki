---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [testing, database, in-memory, postgresql]
status: active
---

# ADR: Dual Database Testing Strategy

## Decision

Maintain **two database backends**: an in-memory adapter (default for unit tests) and PostgreSQL (for integration/postgres-specific tests).

Controlled by environment variable: `BUG_TRACKER_TEST_DB_BACKEND=memory|postgres`

## Why

Fast unit test feedback: the in-memory backend runs tests without requiring Docker or a live PostgreSQL instance. Developers can run `npm run test` immediately after checkout.

PostgreSQL-specific behavior (constraints, transactions, advanced queries) is tested separately with `npm run test:unit:postgres`, which requires the Docker service to be running.

## How to Apply

- Default `npm run test` → in-memory, fast, no Docker needed
- `npm run test:unit:postgres` → real PostgreSQL, use when changing DB schema, queries, or migration scripts
- E2E tests (`npm run test:e2e`) always use PostgreSQL via Docker

## Consequences

- Risk of in-memory/PostgreSQL behavioral divergence — mitigated by running postgres tests in CI.
- `database-client.ts` must maintain compatible interfaces across both adapters.
- Test isolation: `postgres-test-reset.ts` truncates tables; in-memory resets on process.
