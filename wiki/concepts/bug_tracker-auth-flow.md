---
type: concept
owner: engineering
last_updated: 2026-04-19
source_count: 6
tags: [auth, session, security, password]
status: active
---

# Authentication Flows

## Registration Flow

1. User submits `POST /api/auth/signup` with name, email, password, org name, org slug
2. `registerUserWithOrganization()` creates user + org + membership atomically
3. Session cookie set immediately (user is logged in)
4. `sendSignupNotificationsFromDatabase()` sends welcome + verification emails async

## Login Flow

1. `POST /api/auth/login` with email + password
2. `authenticateUser()` verifies password hash
3. New session record created in DB; session token set as HTTP-only cookie
4. Cookie lifetime controlled by `getSessionMaxAgeSeconds()`

## Session Validation

Every authenticated route calls `requireAuthenticatedUser(request)` which:
1. Reads the session cookie by name (`getSessionCookieName()`)
2. Looks up session in DB, checks expiry
3. Throws `AuthenticationError` if invalid → route returns 401

Extension routes use `getAuthenticatedSessionFromExtensionRequest()` which reads the token from the `Authorization` header instead.

## Email Verification

1. On signup, a verification token is saved via `auth-action-token-store.ts`
2. Email sent with link containing token
3. `POST /api/auth/verify-email` with `{ token }` calls `verifyEmailAddressFromTokenInDatabase()`
4. Token is consumed (one-use)

## Password Reset Flow

1. `POST /api/auth/forgot-password` — always returns 200 (no enumeration)
2. If email exists, reset token created and email sent (template: `forgot-password.html`)
3. User clicks link → `POST /api/auth/reset-password` with `{ token, password }`
4. `completePasswordResetFromTokenInDatabase()` validates token, updates hash, invalidates token
5. Confirmation email sent (template: `password-reset-complete.html`)

## Password Change (authenticated)

1. `POST /api/auth/change-password` requires active session
2. `changeAuthenticatedUserPasswordInDatabase()` verifies current password before updating
3. Notification email sent (template: `password-changed.html`)

## Agent Authentication

Agent API routes use `requireAgentApiUser()` which validates API keys or JWT tokens stored in `src/lib/settings/api-key-service.ts` / `auth-jwt-service.ts`.