---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [observability, middleware, api]
status: active
---

# ADR-003: withRequestObservability Wrapper on All Routes

## Decision

Every exported route handler is wrapped: `export const GET = withRequestObservability("GET", GETHandler)`.

**Why:** Consistent timing, error capture, and structured logging across all API routes without requiring each handler to remember to call observability hooks. A handler that throws unexpectedly is still recorded.

**Why not middleware:** Next.js middleware runs on the edge and lacks access to the full Node.js runtime (filesystem, pg client). The wrapper pattern runs in the Node.js runtime alongside the handler.

**How to apply:** When adding a new Route Handler, always export via `withRequestObservability`. Never export the raw handler function directly.