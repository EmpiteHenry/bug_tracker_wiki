---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [database, testing, architecture]
status: active
---

# ADR-001: Dual Database Backend (PostgreSQL + In-Memory)

## Decision

The app ships with two database backends selectable via `BUG_TRACKER_TEST_DB_BACKEND`:

- **`postgres`** — PostgreSQL via `pg` driver (production and postgres-specific tests)
- **`memory`** — in-process in-memory store (default for unit tests)

## Rationale

Running PostgreSQL for every unit test requires Docker and adds significant test startup time. An in-memory backend allows the fast test suite (`npm run test`) to run without any infrastructure.

**Why:** Fast feedback loops during development. The in-memory backend provides sub-second test startup vs. waiting for Docker PostgreSQL to be ready.

**How to apply:** Default all unit tests to `memory` backend. Only use `npm run test:unit:postgres` when the change is specifically testing database-level behaviour (schema constraints, SQL queries, migrations).

## Trade-offs

| | In-Memory | PostgreSQL |
|---|---|---|
| Speed | Very fast | Slower (network + process) |
| Fidelity | May miss SQL-specific behaviour | Matches production exactly |
| Infrastructure | None | Docker required |

## Implementation

- `src/lib/db/database-client.ts` — abstraction interface
- `src/lib/db/local-test-database.ts` — in-memory implementation
- `src/lib/db/postgres-client.ts` — PostgreSQL implementation
- `src/lib/db/test-database.ts` — selects backend based on env var