---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [auth, session, password, email-verification, jwt]
status: active
---

# Authentication System

## Session Model

Sessions are cookie-based. The cookie name and expiry are controlled by `src/lib/auth/session.ts` (`getSessionCookieName`, `getSessionExpirationDate`, `getSessionMaxAgeSeconds`).

On login (`POST /api/auth/login`), the server sets an HTTP-only session cookie. On logout (`POST /api/auth/logout`), the token is deleted from the database and the cookie is cleared.

Two session extraction helpers exist:
- `getAuthenticatedSessionFromRequest` — reads the session cookie (browser app)
- `getAuthenticatedSessionFromExtensionRequest` — reads from request headers (Chrome extension)

## Auth Service Guards

All protected API routes call one of:

| Guard | File | Use |
|---|---|---|
| `requireAuthenticatedUser` | `auth-service.ts` | Any logged-in user |
| `requireAdminUser` | `auth-service.ts` | Admin-only routes |
| `requireAgentApiUser` | `agent-api.ts` | Agent API key auth |
| `requireExtensionAuthenticatedUser` | `auth-service.ts` | Extension header auth |

These throw `AuthenticationError` on failure; API routes catch this and return 401.

## Auth Flows

### Signup
`POST /api/auth/signup` → `registerUserWithOrganization` → `sendSignupNotificationsFromDatabase`

Creates the user, creates their organization, sets session cookie, fires welcome + verification emails.

### Email Verification
`POST /api/auth/verify-email` → `verifyEmailAddressFromTokenInDatabase`

Token-based; token stored in `auth-action-token-store.ts`.

### Forgot Password
`POST /api/auth/forgot-password` → `sendForgotPasswordNotificationFromDatabase`

Always returns 200 to avoid email enumeration.

### Reset Password
`POST /api/auth/reset-password` → `completePasswordResetFromTokenInDatabase`

Validates token from `auth-action-token-store`, hashes and updates password via `reset-password-service.ts`.

### Change Password
`POST /api/auth/change-password` → `changeAuthenticatedUserPasswordInDatabase`

Requires current password; uses `change-password-service.ts`. Sends notification email on success.

## Password Hashing

`src/lib/auth/password.ts` — utility for hashing and comparing passwords.

## Related Files

```
src/lib/auth/
├── auth-service.ts              — core auth logic, session guards
├── auth-user-store.ts           — user DB queries
├── auth-action-token-store.ts   — email action tokens (verify, reset)
├── auth-notification-service.ts — signup/change-password emails
├── session.ts                   — cookie name/expiry config
├── password.ts                  — hash + compare
├── email-verification-service.ts
├── forgot-password-service.ts
├── reset-password-service.ts
└── change-password-service.ts
```
