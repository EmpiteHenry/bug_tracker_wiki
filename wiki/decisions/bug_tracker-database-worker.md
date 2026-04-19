---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [postgresql, worker-thread, esbuild, architecture]
status: active
---

# ADR-002: PostgreSQL Worker Thread Architecture

## Context

Next.js API routes run in a serverless-style execution environment. Long-lived PostgreSQL connection pools do not work well across cold-starts and the Next.js module lifecycle.

## Decision

Database operations that need a persistent connection pool run in a dedicated Node.js worker thread (`src/lib/db/postgres-worker.ts`). The worker is compiled separately by esbuild into `.next/db/postgres-worker.mjs` and loaded at runtime.

This is why `package.json` has two build steps:
1. `next build` — compiles the Next.js app
2. `build:db-worker` — bundles the worker with esbuild (`--platform=node --format=esm --packages=external`)

The `predev` hook ensures the worker bundle exists before `next dev` starts.

## Tradeoffs

**Pro**: Connection pool lifecycle is independent of the Next.js request handler lifecycle. The worker persists between requests.

**Con**: Additional build step. Worker must be rebuilt when `postgres-worker.ts` changes. Adds complexity to the test setup (`local-test-database-runtime.ts` handles this for tests).

## Status

Active.