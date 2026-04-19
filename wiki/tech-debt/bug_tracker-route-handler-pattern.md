---
type: tech-debt
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [api, routes, consistency]
status: active
---

# Inconsistent Route Handler Observability Wrapping

## Issue

Most Route Handlers follow the pattern:

```ts
async function GETHandler(request: NextRequest) { ... }
export const GET = withRequestObservability("GET", GETHandler);
```

However, `src/app/api/bugs/[bugId]/history/route.ts` exports the handler directly:

```ts
export async function GET(request: NextRequest, context: RouteContext) { ... }
```

This means the bug history endpoint is not covered by request observability — its timing, errors, and access patterns are not recorded in operational logs.

## Risk

Low functional risk, but monitoring blind spot. A slow or erroring history endpoint will not appear in the admin performance dashboard.

## Fix

Refactor `history/route.ts` to use `withRequestObservability` consistent with all other routes.