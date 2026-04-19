---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [testing, database, postgresql, in-memory]
status: active
---

# ADR-002: Dual Database Backend for Testing

## Decision

Support two database backends controlled by `BUG_TRACKER_TEST_DB_BACKEND`:
- `memory` — in-memory database for fast unit tests (default for `npm test`)
- `postgres` — real PostgreSQL for database-specific tests (`npm run test:unit:postgres`)

## Rationale

Running all tests against PostgreSQL requires Docker to be running and adds significant latency. Most unit tests validate service and business logic, not database specifics. The in-memory backend lets the default test suite run quickly without any infrastructure dependency.

A separate postgres-backed suite (`scripts/run-postgres-unit-tests.sh`) exists for changes that depend on PostgreSQL-specific behavior (e.g., migrations, constraints, full-text search).

## Consequences

- Service layer stores accept an injected `db` parameter — they do not import the client directly, making them backend-agnostic
- `database-client.ts` provides the unified interface both backends implement
- Teams must run `npm run test:unit:postgres` when touching database schema or store files
- Risk: in-memory and postgres behavior divergence (schema mismatches won't be caught by fast tests)