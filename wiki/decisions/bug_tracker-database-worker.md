---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [database, worker-thread, postgresql, architecture]
status: active
---

# ADR-002: PostgreSQL Worker Thread Architecture

## Decision

PostgreSQL connections are managed in a **dedicated Worker thread** (`postgres-worker.ts`), pre-built to `.next/db/postgres-worker.mjs`.

## Rationale

Next.js App Router runs route handlers in a serverless/edge-like execution model where persistent connection pools can cause issues (connection leaks, cold start overhead, module reuse across requests).

Isolating PostgreSQL in a Worker thread:
- Keeps the connection pool alive across requests without polluting the main thread
- Avoids the "pg client shared across hot-reloaded modules" problem in `next dev`
- Allows the worker to be independently bundled by esbuild

## Consequences

- Worker must be built before `next dev` starts — handled by `predev` npm script
- Worker is also built as part of `npm run build`
- Tests use an in-memory backend to avoid this complexity entirely

## Build Commands

```bash
# Build worker only
npm run build:db-worker

# Runs automatically before dev
npm run dev  # predev hook builds worker first
```