---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 12
tags: [auth, api, session, password]
status: active
---

# Authentication API

All auth endpoints live under `/api/auth/`. They use JSON request/response bodies. Sessions are stored as HTTP-only cookies.

## Endpoints

### POST /api/auth/signup
Register a new user and create their organization in one step.

**Body**: `{ name, email, password, organizationName, organizationSlug }`  
**Success**: Sets session cookie, returns session info.  
**Errors**: `AuthenticationError` (email already taken, etc.)

### POST /api/auth/login
Authenticate an existing user.

**Body**: `{ email, password }`  
**Success**: Sets session cookie (`getSessionCookieName()`), expiration from `getSessionMaxAgeSeconds()`.  
**Errors**: 401 on bad credentials.

### POST /api/auth/logout
Clears the session cookie and deletes the server-side session record.

### GET /api/auth/session
Returns current session info if cookie is valid; 401 otherwise.

### POST /api/auth/verify-email
Verify an email address using a one-time token.

**Body**: `{ token }`

### POST /api/auth/forgot-password
Sends a password-reset email. Always returns 200 to prevent user enumeration.

**Body**: `{ email }`

### POST /api/auth/reset-password
Completes the password reset flow.

**Body**: `{ token, password }`

### POST /api/auth/change-password
Changes the password for the currently authenticated user.

**Body**: `{ currentPassword, newPassword }`  
**Auth**: Requires active session cookie.

## Session Model

- Cookie name: determined by `getSessionCookieName()`
- Expiry: `getSessionExpirationDate()` / `getSessionMaxAgeSeconds()`
- Server-side session records are stored in the database via `auth-user-store.ts`

## Service Files

| File | Responsibility |
|---|---|
| `src/lib/auth/auth-service.ts` | Core authenticate/register/session logic |
| `src/lib/auth/session.ts` | Cookie name, expiry helpers |
| `src/lib/auth/forgot-password-service.ts` | Forgot-password token generation + email |
| `src/lib/auth/reset-password-service.ts` | Token validation + password update |
| `src/lib/auth/email-verification-service.ts` | Email verification token flow |
| `src/lib/auth/change-password-service.ts` | Authenticated password change |
| `src/lib/auth/auth-action-token-store.ts` | Stores one-time tokens (reset, verify) |
| `src/lib/auth/auth-notification-service.ts` | Sends auth emails (welcome, verify) |