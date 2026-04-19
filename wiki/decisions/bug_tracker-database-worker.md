---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [database, worker-thread, postgresql, build]
status: active
---

# ADR-002: PostgreSQL Worker Thread Pattern

## Decision

Run PostgreSQL client operations in a Node.js worker thread via `postgres-worker.ts`, bundled separately to `.next/db/postgres-worker.mjs`.

## Context

Next.js edge runtime and certain Next.js compilation contexts cannot use native Node.js modules like `pg` directly. Running the PG client in a worker thread isolates it from the main Next.js runtime.

## Build Step

```bash
esbuild src/lib/db/postgres-worker.ts \
  --bundle --platform=node --format=esm \
  --packages=external \
  --outfile=.next/db/postgres-worker.mjs
```

This runs as a `predev` hook (before `next dev`) and as part of `npm run build`.

## Tradeoffs

**Pro:** Avoids native module compatibility issues with Next.js bundler.  
**Con:** Requires an extra build step; worker must be rebuilt when worker source changes.

## Worker Bootstrap

`postgres-worker-bootstrap.mjs` — initialisation logic run inside the worker.  
`postgres-bootstrap.ts` — schema bootstrap on first run.