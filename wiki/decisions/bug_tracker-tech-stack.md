---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [nextjs, react, postgresql, typescript, architecture]
status: active
---

# ADR-001: Technology Stack

## Decision

Bug Tracker is built as a **Next.js 16 (App Router) monolith** using TypeScript, React 19, and PostgreSQL.

## Rationale

**Why Next.js App Router:**
The App Router allows co-locating API routes (`src/app/api/`) and React Server Components alongside client pages in a single repository, eliminating a separate backend service while keeping the deployment footprint small.

**Why webpack mode:**
`next dev --webpack` and `next build --webpack` are explicitly specified in all scripts. This avoids Turbopack compatibility issues with the project's current dependency set.

**Why PostgreSQL:**
- Relational data model maps naturally to the org → project → bug hierarchy
- JSONB columns for flexible fields (job summaries, options)
- Transaction support critical for multi-step operations (signup, import)
- `pg` driver is mature and well-typed (`@types/pg`)

**Why a worker thread for the DB:**
Next.js server components and API routes run on the Node.js event loop. Blocking PostgreSQL queries are offloaded to a dedicated worker thread (`postgres-worker.ts`) to prevent latency spikes on concurrent requests.

**Why dual DB backend (in-memory + PostgreSQL):**
Running `npm test` against the in-memory backend keeps the fast unit test suite independent of Docker, enabling rapid TDD. PostgreSQL-specific behavior is validated separately via `npm run test:unit:postgres`.

See [Database Choice ADR](bug_tracker-database-dual-backend.md) for details on the dual-backend pattern.

**Why Zod 4:**
Schema validation at API boundaries with TypeScript inference eliminates hand-written type guards on request bodies.

**Why shadcn/ui:**
Provides accessible, composable primitives (via Radix/Base UI) that can be copied and customised rather than imported as an opaque library dependency.