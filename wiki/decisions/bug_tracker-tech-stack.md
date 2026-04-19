---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [nextjs, typescript, react, tech-stack]
status: active
---

# ADR-001: Tech Stack Selection

## Decision

Use Next.js 16 (App Router) with TypeScript, React 19, Tailwind CSS 4, and PostgreSQL.

## Context

The application needs:
- Authenticated multi-tenant workspace (server-rendered pages + API routes)
- A single deployable unit covering both UI and backend API
- Strong typing for correctness across a complex domain model

## Choices Made

### Next.js App Router
Full-stack in one repo. API routes and React Server Components coexist. Reduces infra complexity for a team-sized product.

**Webpack mode** (`--webpack`) used explicitly — `next dev --webpack` and `next build --webpack`. The default Turbopack is not used, likely for plugin compatibility.

### PostgreSQL over SQLite/NoSQL
Relational model fits the domain (orgs → projects → bugs → comments/attachments/history). PostgreSQL enables complex queries for monitoring, grouping, and audit.

### In-Memory DB for Tests
`BUG_TRACKER_TEST_DB_BACKEND=memory` lets the default test suite run without Docker. PostgreSQL tests are opt-in via `npm run test:unit:postgres`.

### shadcn/ui + Tailwind CSS 4
Component primitives from shadcn with Radix UI under the hood. Tailwind 4 for styling.

### Zod 4 for Validation
Used for schema validation at API boundaries and form inputs.

### SendGrid for Email
Transactional email via SendGrid. Configurable via env vars; disabled locally when key is absent.