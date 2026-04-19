---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [auth, session, password, email-verification]
status: active
---

# Authentication System

## Session Model

Sessions are stored in PostgreSQL via `auth-user-store.ts`. A session cookie is set on login/signup and cleared on logout.

Key helpers in `src/lib/auth/session.ts`:
- `getSessionCookieName()` — returns the cookie name
- `getSessionExpirationDate()` — absolute expiry date
- `getSessionMaxAgeSeconds()` — max-age for Set-Cookie

`getAuthenticatedSessionFromRequest(request)` — reads session from standard cookie header (browser flows).  
`getAuthenticatedSessionFromExtensionRequest(request)` — reads session from `Authorization` header (extension flows).

## Auth Flows

### Login
`POST /api/auth/login` — validates credentials, creates session, sets cookie.  
`POST /api/extension/auth/login` — same but returns token in JSON body for the extension.

### Signup
`POST /api/auth/signup` — registers user + organization in one step, fires welcome/verification emails via `auth-notification-service.ts`.

### Email Verification
`POST /api/auth/verify-email` — consumes a token from `auth-action-token-store.ts` to verify the email address.

### Password Flows
`POST /api/auth/forgot-password` → sends reset email  
`POST /api/auth/reset-password` → completes reset with token  
`POST /api/auth/change-password` → changes password for authenticated user

### Logout
`POST /api/auth/logout` — deletes session from DB, clears cookie.

## Guards

- `requireAuthenticatedUser(request)` — throws `AuthenticationError` if no valid session
- `requireAdminUser(request)` — throws `AuthenticationError` if not admin
- `requireExtensionAuthenticatedUser(request)` — for extension routes
- `requireActiveOrganizationSession(request)` — ensures user has an active org context; throws `ActiveOrganizationSessionError`

## Error Types

`AuthenticationError` — unauthenticated or unauthorized  
`ChangePasswordServiceError`, `ForgotPasswordServiceError`, `ResetPasswordServiceError`, `EmailVerificationServiceError` — domain-specific errors thrown by respective services