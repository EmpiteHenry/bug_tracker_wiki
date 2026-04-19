---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 38
tags: [architecture, patterns, service-layer, store-layer]
status: active
---

# Architecture: Service and Store Layers

The codebase follows a strict three-layer pattern throughout `src/lib/`:

```
API Route (src/app/api/...)
    │
    ▼
Service  (src/lib/<domain>/<name>-service.ts)
    │
    ▼
Store    (src/lib/<domain>/<name>-store.ts)
    │
    ▼
Database (src/lib/db/database-client.ts)
```

## Layer Responsibilities

### API Routes (`src/app/api/`)
- Parse and validate HTTP request inputs (body, params, query)
- Call service functions
- Map service errors to HTTP response codes
- Wrapped with `withRequestObservability()` for all routes

### Services (`*-service.ts`)
- Orchestrate business logic across stores
- Enforce authorization and domain rules
- Emit notification events and audit log entries
- Throw typed error classes (e.g., `BugServiceError`, `AuthenticationError`)

### Stores (`*-store.ts`)
- Direct database reads/writes using the database client
- No business logic — data access only
- Accept a `db` parameter injected by the service (testable)

## Error Handling Convention

Each domain exports a typed error class:

```ts
// Example pattern used across all domains
export class BugServiceError extends Error { ... }
export class AuthenticationError extends Error { ... }
export class ProjectServiceError extends Error { ... }
export class OperationalAlertServiceError extends Error { ... }
```

API routes catch these typed errors and return appropriate HTTP status codes, re-throwing unexpected errors.

## Request Observability Wrapper

Every API route handler is exported through `withRequestObservability`:

```ts
export const GET = withRequestObservability("GET", GETHandler);
export const POST = withRequestObservability("POST", POSTHandler);
```

This records request timing, method, path, and outcome to the operational log store.

## Domain Modules in `src/lib/`

| Module | Key files |
|---|---|
| `auth/` | auth-service, session, password, forgot/reset/change/verify flows |
| `bugs/` | bug-service, bug-comment-service, bug-history-service, attachment-store, agent-api, mcp-server |
| `organizations/` | organization-service, membership, invitation, billing, seat, access |
| `projects/` | project-service, project-store, project-membership-service |
| `notifications/` | dispatcher, sendgrid-delivery, template-engine, recipient-service |
| `monitoring/` | observability-logger, operational-log, operational-alert, error-fingerprint, performance |
| `settings/` | api-key-service, auth-jwt-service, api-access-config-service |
| `db/` | database-client, postgres-client, schema, migrations, test database |
| `storage/` | attachment-storage abstraction |
| `audit/` | organization-audit-log-service |
| `users/` | user-service (QA user management) |
| `extension/` | extension-service, extension-version |
| `releases/` | whats-new |