---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [auth, session, jwt, password, email-verification]
status: active
---

# Authentication System

## Auth Flows

### Standard User Auth (Browser)
1. `POST /api/auth/signup` — registers user + organization, sets session cookie, sends welcome + verification emails
2. `POST /api/auth/login` — validates credentials, creates session, sets `bug-tracker-session` cookie
3. `POST /api/auth/logout` — deletes session from DB, clears cookie
4. `GET /api/auth/session` — returns current user/org from session

### Email Verification
- On signup, a token is stored via `auth-action-token-store`
- `POST /api/auth/verify-email` consumes the token via `email-verification-service`

### Password Reset
- `POST /api/auth/forgot-password` → `forgot-password-service` → sends email with reset token
- `POST /api/auth/reset-password` → `reset-password-service` → validates token, updates hash
- `POST /api/auth/change-password` → `change-password-service` → requires current password

## Session Management (`src/lib/auth/session.ts`)

Sessions are stored in the database and identified by a cookie named `bug-tracker-session`. Key helpers:
- `getSessionCookieName()` — reads cookie name from env
- `getSessionMaxAgeSeconds()` — configures expiry duration
- `getSessionExpirationDate()` — returns absolute expiry `Date`

## Admin User Detection

`requireAdminUser(request)` in `auth-service.ts` — reads session, checks admin flag on user record. Used by all `/api/admin/*` routes.

## Extension Auth (`src/lib/auth/auth-service.ts`)

Extension uses a separate JWT-based auth flow:
- `getAuthenticatedSessionFromExtensionRequest(request)` — reads `Authorization: Bearer <token>` header
- Login returns a JWT instead of a session cookie
- Auth stored in `settings/auth-jwt-service`

## Agent Auth (`src/lib/bugs/agent-api.ts`)

- `requireAgentApiUser(request)` — validates API key from request headers
- API keys managed via `settings/api-key-service`
- All agent routes return structured `{ success, data }` / `{ success, error }` envelopes via `createAgentApiSuccessResponse` / `createAgentApiErrorResponse`

## Password Hashing (`src/lib/auth/password.ts`)

Standalone module for hashing and verification — no external dependency details exposed in source.

## Key Source Files

| File | Purpose |
|---|---|
| `src/lib/auth/auth-service.ts` | Core: authenticate, register, session lookup, admin/extension checks |
| `src/lib/auth/session.ts` | Cookie name, max-age, expiration helpers |
| `src/lib/auth/auth-user-store.ts` | DB reads/writes for users |
| `src/lib/auth/auth-action-token-store.ts` | Stores one-time tokens (verify, reset) |
| `src/lib/auth/forgot-password-service.ts` | Forgot password orchestration |
| `src/lib/auth/reset-password-service.ts` | Reset password orchestration |
| `src/lib/auth/change-password-service.ts` | Change password orchestration |
| `src/lib/auth/email-verification-service.ts` | Email verification orchestration |
| `src/lib/auth/auth-notification-service.ts` | Sends welcome/verification emails on signup |