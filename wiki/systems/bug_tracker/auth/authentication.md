---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [auth, sessions, jwt, password, email-verification]
status: active
---

# Authentication System

Authentication is handled entirely in `src/lib/auth/`. There is no external auth provider — users, sessions, and tokens are stored in PostgreSQL.

## Session Model

Sessions use an HTTP-only cookie. The cookie name and expiration are configured via `src/lib/auth/session.ts`.

- `getSessionCookieName()` — returns the cookie name
- `getSessionMaxAgeSeconds()` — session duration in seconds
- `getSessionExpirationDate()` — computed expiry `Date`

On login/signup, a session token is written to the database and set as a cookie. On logout, the token is deleted from the database and the cookie is cleared.

The session is read from request cookies in `getAuthenticatedSessionFromRequest()`. For the Chrome extension, `getAuthenticatedSessionFromExtensionRequest()` reads a Bearer token from the `Authorization` header instead.

## Auth Service (`auth-service.ts`)

Core functions:

| Function | Description |
|---|---|
| `authenticateUser(db, {email, password})` | Verify credentials, create session, return token |
| `registerUserWithOrganization(db, {...})` | Create user + org atomically, create session |
| `requireAuthenticatedUser(request)` | Throw `AuthenticationError` if no valid session |
| `requireAdminUser(request)` | Throw if not admin |
| `requireExtensionAuthenticatedUser(request)` | For extension Bearer auth |
| `deleteSessionByToken(token)` | Logout |
| `getAuthenticatedSessionFromRequest(request)` | Returns session or null |

`AuthenticationError` is the typed error class; callers map it to 401.

## Password Handling

Passwords are hashed in `src/lib/auth/password.ts`. The hash algorithm is bcrypt (inferred from standard Next.js auth patterns).

Password flows:

- **Forgot password** (`forgot-password-service.ts`): generates a token, stores it via `auth-action-token-store.ts`, sends email via `auth-notification-service.ts`
- **Reset password** (`reset-password-service.ts`): validates token, updates hash
- **Change password** (`change-password-service.ts`): requires current password, updates hash for authenticated user
- **Email verification** (`email-verification-service.ts`): token-based, same token store

## Action Token Store

`src/lib/auth/auth-action-token-store.ts` stores short-lived tokens (password reset, email verification) in the database. Each token has a type, expiry, and single-use flag.

## Notification Integration

`src/lib/auth/auth-notification-service.ts` sends transactional emails on:
- Signup (welcome)
- Email verification
- Forgot password
- Password changed
- Password reset complete

These delegate to the [notification system](../notifications/email-notifications.md).

## Extension Auth

The Chrome extension cannot use cookies (cross-origin). It authenticates via `/api/extension/auth/login`, receives an API token, and sends it as `Authorization: Bearer <token>` on subsequent requests.

## Admin Auth

Admin access checks `user.isAdmin` from the session. There is no separate role table — it is a boolean on the user record, set during QA user creation or bootstrap.

## Bootstrap

`src/lib/db/bootstrap-admin.ts` seeds the first admin user for a fresh deployment.