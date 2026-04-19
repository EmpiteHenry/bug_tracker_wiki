---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [tech-stack, next.js, typescript, postgresql, react]
status: active
---

# ADR-001: Technology Stack

## Decision

Use **Next.js 16** (App Router, webpack bundler) with **TypeScript 5**, **PostgreSQL**, and **React 19**.

## Rationale

- **Next.js App Router**: Unified server/client rendering, built-in API routes eliminates separate backend service
- **TypeScript**: Type safety across API contracts, service layer, and UI components
- **PostgreSQL**: Relational integrity for multi-tenant data; organization/project/bug hierarchy maps naturally to relational model
- **React 19 + shadcn/ui**: Modern component primitives with Tailwind CSS 4
- **Zod 4**: Runtime schema validation at API boundaries (all request body parsing uses Zod-compatible manual checks)

## Consequences

- Webpack bundler (not Turbopack) chosen for stability — noted in `npm run dev` (`--webpack` flag)
- PostgreSQL worker thread architecture needed due to Next.js process model (see [Database Architecture](../systems/bug_tracker/database/schema.md))
- In-memory SQLite test backend added to keep unit tests fast without Docker

## Alternatives Considered

- Separate Express API backend — rejected to keep deployment simple
- Turbopack — not yet adopted, webpack used explicitly