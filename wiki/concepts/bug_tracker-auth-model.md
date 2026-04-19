---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [auth, session, api-keys, jwt]
status: active
---

# Authentication Model

The bug tracker supports three authentication methods for different consumers.

## 1. Session-Based Auth (Browser App)

Used by the main web UI.

- **Login**: `POST /api/auth/login` — validates credentials, creates a session record, returns a session cookie
- **Session cookie**: name from `getSessionCookieName()`, contains a signed token
- **Expiry**: `getSessionMaxAgeSeconds()` / `getSessionExpirationDate()`
- **Logout**: `POST /api/auth/logout` — deletes session record and clears cookie
- **Guard**: `requireAuthenticatedUser(request)` — used in all protected API routes

## 2. Extension Token Auth (Chrome Extension)

Uses `Authorization: Bearer <token>` header instead of cookies. The extension obtains a token via `POST /api/extension/auth/login` using the same credentials.

Guard: `requireExtensionAuthenticatedUser(request)` / `getAuthenticatedSessionFromExtensionRequest(request)`

## 3. Agent API Key Auth

Used by automated agents hitting `/api/agent/*`. API keys are organization-scoped and managed in settings.

Guard: `requireAgentApiUser(request)` in agent routes.

## Email Verification Flow

1. Signup → verification email sent with token
2. User clicks link → `POST /api/auth/verify-email` with token
3. Account marked verified

## Password Reset Flow

1. `POST /api/auth/forgot-password` with email → sends reset email
2. User clicks link with token → `POST /api/auth/reset-password` with `{token, password}`
3. `AuthActionTokenStore` validates and consumes the one-time token

## JWT API Access

Separate from session auth. Users can configure JWT signing settings in `/settings/api-docs`. Used to authenticate external API consumers. Managed by `AuthJwtService`.

## Admin Access

`requireAdminUser(request)` is used across all `/api/admin/*` routes. Admin users have a boolean flag in the users table set during bootstrap (`src/lib/db/bootstrap-admin.ts`).