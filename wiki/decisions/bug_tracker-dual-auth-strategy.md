---
type: decision
owner: engineering
last_updated: 2026-04-19
source_count: 4
tags: [decision, auth, api-keys, jwt, session]
status: active
---

# ADR-002: Dual Authentication Strategy

## Decision

Use **session cookies** for the browser app and **API keys / JWT tokens** for programmatic access (agent API, extension, external integrations).

## Rationale

| Client | Auth method | Reason |
|---|---|---|
| Browser (authenticated users) | HTTP-only session cookie | CSRF-safe, no token management needed in JS |
| Chrome extension | `Authorization` header token | Extensions can't reliably set/read cookies cross-origin |
| Agent API | API key or JWT | Stateless, scriptable, revocable per key |
| MCP server | API key | Machine-to-machine, no interactive login |

## Implementation

- `requireAuthenticatedUser()` — validates session cookie
- `getAuthenticatedSessionFromExtensionRequest()` — validates `Authorization` header token
- `requireAgentApiUser()` — validates API key or JWT
- `requireAdminUser()` — validates session cookie + admin role check

API keys are managed in `src/lib/settings/api-key-service.ts`. JWT configuration is in `auth-jwt-service.ts`. Both are surfaced in the Settings UI at `/settings/api-docs`.

## Trade-offs

- Two auth code paths to maintain
- API key rotation and revocation must be handled explicitly
- JWT expiry must be configured appropriately for agent use cases