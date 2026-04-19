---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [auth, session, jwt, password, email-verification]
status: active
---

# Authentication System

Handles all user identity flows: registration, login, session management, and credential recovery.

## Flows

### Registration (`/api/auth/signup`)
1. POST `{ name, email, password, organizationName, organizationSlug }`
2. `registerUserWithOrganization()` creates user + org atomically
3. `sendSignupNotificationsFromDatabase()` sends welcome + verification email
4. Session cookie set on success

### Login (`/api/auth/login`)
1. POST `{ email, password }`
2. `authenticateUser()` validates credentials
3. Session cookie (`getSessionCookieName()`) set with expiry from `getSessionExpirationDate()`

### Session Validation
- **Web app**: `getAuthenticatedSessionFromRequest()` — reads session cookie
- **Extension**: `getAuthenticatedSessionFromExtensionRequest()` — reads `Authorization` header token
- Session data available at `GET /api/auth/session`

### Password Recovery
| Step | Endpoint | Service |
|---|---|---|
| Request reset | `POST /api/auth/forgot-password` | `sendForgotPasswordNotificationFromDatabase()` |
| Complete reset | `POST /api/auth/reset-password` | `completePasswordResetFromTokenInDatabase()` |
| Change password | `POST /api/auth/change-password` | `changeAuthenticatedUserPasswordInDatabase()` |

Tokens stored via `auth-action-token-store.ts` with expiry.

### Email Verification
- `POST /api/auth/verify-email` with `{ token }`
- `verifyEmailAddressFromTokenInDatabase()` marks address as verified

### Logout
- `POST /api/auth/logout` — deletes server-side session, clears cookie

## Key Files

| File | Role |
|---|---|
| `src/lib/auth/auth-service.ts` | Core: `authenticateUser`, `requireAdminUser`, `requireAuthenticatedUser` |
| `src/lib/auth/session.ts` | Cookie name, expiry, max-age helpers |
| `src/lib/auth/auth-user-store.ts` | User persistence layer |
| `src/lib/auth/auth-action-token-store.ts` | One-time tokens (reset, verify) |
| `src/lib/auth/password.ts` | Hashing utilities |

## Authorization Guards

- `requireAuthenticatedUser()` — any logged-in user
- `requireAdminUser()` — platform admin (used by all `/api/admin/*` routes)
- `requireAgentApiUser()` — agent API key auth (used by `/api/agent/*` routes)
- `requireActiveOrganizationSession()` — user with active org context