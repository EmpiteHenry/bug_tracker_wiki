---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [next.js, postgresql, typescript, architecture]
status: active
---

# ADR-001: Technology Stack

## Context

Bug Tracker needed a modern, full-stack TypeScript application with a relational database, email delivery, and support for both a browser app and API integrations (agent, extension, MCP).

## Decisions

### Next.js App Router (monorepo)

The entire application — UI, API routes, background scripts — lives in one Next.js project. This avoids a separate backend service, simplifies deployment, and keeps TypeScript types shared across the full stack.

Webpack mode is used explicitly (`next dev --webpack`, `next build --webpack`) rather than Turbopack, likely for stability with the custom worker bundle step.

### PostgreSQL as primary database

PostgreSQL was chosen over a managed BaaS (Supabase, PlanetScale, etc.) to allow full schema control, custom migrations, and on-premise deployment. The database client is abstracted behind `database-client.ts` so the backing store can be replaced.

**Why not SQLite in production**: The worker-thread architecture (postgres-worker.ts bundled separately) suggests PostgreSQL-specific features (connection pooling, LISTEN/NOTIFY potential) were important.

### In-memory SQLite for tests

Running `npm test` uses an in-memory SQLite backend to avoid requiring Docker for the default test suite. PostgreSQL-backed tests are opt-in via `npm run test:unit:postgres`. This is a deliberate DX tradeoff: fast tests by default, real-DB tests when needed.

### SendGrid for email

Email delivery is handled by SendGrid with plain HTML templates. No React Email, MJML, or other template framework — templates are `.html` files with string interpolation. Simple and dependency-free.

### No Zod at API boundaries

Request body validation uses plain TypeScript `typeof` guards rather than Zod schemas. This is consistent throughout all route handlers. This reduces bundle size and keeps validation logic explicit, at the cost of more verbose validation code.

### shadcn/ui + Tailwind CSS 4

UI components use shadcn/ui (component source in `src/components/ui/`). Tailwind CSS 4 with PostCSS provides utility styling. This avoids a heavy component library runtime while keeping design consistent.

### MCP Server for LLM integration

`@modelcontextprotocol/sdk` is included as a production dependency, exposing bug tracker operations as MCP tools. This positions the product for LLM-driven agentic workflows without requiring a separate microservice.

## Status

Active — reflects the current codebase as of April 2026.