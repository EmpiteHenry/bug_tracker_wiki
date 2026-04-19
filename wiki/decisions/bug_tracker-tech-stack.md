---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [tech-stack, nextjs, typescript, postgresql, react]
status: active
---

# ADR-001: Technology Stack

## Decision

Build Bug Tracker as a Next.js 16 (App Router) TypeScript monolith with PostgreSQL as the primary database.

## Rationale

**Next.js App Router** — co-locates API routes and UI in a single deployable unit. Eliminates a separate backend service for an internal workspace tool. webpack mode is explicitly selected (`--webpack` flag in dev and build commands), opting out of Turbopack for stability.

**TypeScript** — enforced across the entire codebase (strict typecheck CI step via `npm run typecheck`).

**PostgreSQL** — relational data model with foreign keys, needed for bug/project/org relationships and audit history. Docker Compose used for local development.

**React 19 + Tailwind CSS v4 + shadcn/ui** — modern component primitives with utility CSS. shadcn components are inlined into `src/components/ui/` (not imported from a package) for full customization control.

**Node.js built-in test runner** — chosen over Jest/Vitest for zero-config unit testing. Tests run with `--import tsx` for TypeScript support.

**Playwright** — e2e testing against a real seeded database.

## Consequences

- Full-stack TypeScript in one repo simplifies onboarding but requires disciplined layer separation (see [Architecture: Layers](../systems/bug_tracker/architecture/layers.md))
- webpack mode adds build overhead compared to Turbopack — accepted for ecosystem maturity
- In-memory test backend (`BUG_TRACKER_TEST_DB_BACKEND=memory`) makes unit tests fast but requires a separate postgres test suite for DB-specific behavior