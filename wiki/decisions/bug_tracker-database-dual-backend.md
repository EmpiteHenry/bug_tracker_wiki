---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [database, testing, postgresql, in-memory]
status: active
---

# ADR-002: Dual Database Backend (In-Memory + PostgreSQL)

## Decision

The application selects its database backend at runtime via the `BUG_TRACKER_TEST_DB_BACKEND` environment variable:

- `memory` → in-memory adapter (default for `npm test`)
- unset / `postgres` → PostgreSQL via worker thread

## Context

Two concerns drove this design:

1. **Fast unit tests without infrastructure**: Running the full test suite against Docker PostgreSQL requires starting a container, applying migrations, and managing state between tests. This is slow in a local tight-feedback loop.

2. **Correctness against production DB**: Some bugs (constraint violations, query planner behaviour, JSONB operators) only manifest against real PostgreSQL.

## Implementation

- `src/lib/db/database-client.ts` — exports `getDatabase()` which returns the correct backend
- `src/lib/db/local-test-database.ts` — in-memory adapter implementation
- `src/lib/db/test-database.ts` — unified interface both backends implement
- `src/lib/db/postgres-test-reset.ts` — truncates tables between postgres tests
- `src/lib/db/postgres-test-setup.test.ts` — validates schema parity between backends

## Trade-offs

| | In-memory | PostgreSQL |
|---|---|---|
| Speed | Fast (no I/O) | Slower (network + disk) |
| Infrastructure | None | Docker required |
| Fidelity | Lower | Production-equivalent |
| Use case | Default `npm test` | `npm run test:unit:postgres` |

## When to use each

Run `npm run test:unit:postgres` when a change touches:
- Raw SQL queries or query builder logic
- Schema migrations
- Constraint or index behaviour
- JSONB operations