---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [auth, session, api-key, extension, agent]
status: active
---

# ADR-003: Dual Authentication Strategy

## Decision

The platform uses two distinct authentication mechanisms:

1. **Session cookies** — for human users via the web app
2. **Bearer tokens / API keys** — for the Chrome extension and agent API

## Rationale

- Session cookies with `HttpOnly` are appropriate for browser-based flows and provide CSRF protection
- The Chrome extension cannot reliably share session cookies across origins, so it uses a Bearer token returned at login
- The agent API (`/api/agent/*`) is machine-to-machine — it uses a static API key to avoid requiring browser session infrastructure

## Implementation

- `getAuthenticatedSessionFromRequest()` — reads cookie
- `getAuthenticatedSessionFromExtensionRequest()` — reads `Authorization: Bearer <token>` header
- `requireAgentApiUser()` — validates API key from request
- `requireAdminUser()` — validates admin role from either session type

## Consequences

- Three separate auth code paths must be maintained
- Extension login creates a token stored like a session but returned in the response body (not as a cookie)
- Agent API keys are managed separately from user sessions