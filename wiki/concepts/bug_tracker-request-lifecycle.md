---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [api, observability, auth, request-lifecycle]
status: active
---

# Request Lifecycle

Every API request flows through the same chain:

```
1. Next.js routes request to handler
2. withRequestObservability wraps handler
3. Handler validates request body / params
4. Auth guard called (requireAuthenticatedUser / requireAdminUser / requireAgentApiUser)
5. Active organization resolved (requireActiveOrganizationSession) if needed
6. Service function called
7. Response returned
8. withRequestObservability records method, path, status, duration to operational_logs
```

## Observability Wrapper

`withRequestObservability(method, handler)` in `src/lib/monitoring/request-observability.ts`:
- Wraps every exported handler
- Catches unhandled errors and records them with fingerprints
- Does not suppress errors — re-throws after logging

## Error Handling Pattern

API routes follow a consistent try/catch pattern:

```typescript
try {
  // business logic
} catch (error) {
  if (error instanceof AuthenticationError) return 401
  if (error instanceof SomeServiceError) return 400 / 404 / 409
  throw error  // unexpected — let observability catch it
}
```

Service-specific error classes (`BugServiceError`, `ProjectServiceError`, etc.) carry typed codes that handlers map to HTTP status codes.

## Request Validation

No schema library (e.g. Zod) is used in API routes. Validation is done via explicit type guard conditions on the parsed JSON body:

```typescript
if (!body || typeof body.name !== "string") {
  return NextResponse.json({ error: "..." }, { status: 400 })
}
```

This pattern is consistent across all routes.
