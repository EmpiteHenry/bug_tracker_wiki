---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [database, testing, postgresql, in-memory]
status: active
---

# ADR-002: Dual Database Backend (PostgreSQL + In-Memory)

## Decision

The `getDatabase()` factory selects between a PostgreSQL client and an in-memory implementation based on `BUG_TRACKER_TEST_DB_BACKEND`.

## Context

Running full PostgreSQL for every unit test would require Docker and incur significant test startup time.

**Why:** An in-memory backend allows `npm run test` to run quickly without infrastructure. PostgreSQL-specific tests (`npm run test:unit:postgres`) are run separately only when database behaviour matters.

**Why not mock:** Using a real interface (same `DatabaseClient` type) rather than mocks means tests exercise actual query logic. The tradeoff is that in-memory tests may miss PostgreSQL-specific edge cases — covered by the separate postgres suite.

**How to apply:** Default new tests to the memory backend. Only reach for `test:unit:postgres` when the test specifically exercises SQL syntax, transactions, constraints, or migrations.