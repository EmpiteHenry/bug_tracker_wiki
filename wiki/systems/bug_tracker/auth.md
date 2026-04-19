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

- Cookie-based sessions (`getSessionCookieName()`).
- Session token is stored in a cookie; expiry controlled by `getSessionMaxAgeSeconds()` / `getSessionExpirationDate()`.
- The session cookie is parsed manually from the `Cookie` header in Route Handlers — Next.js cookie helpers are not used directly.

## Auth Flows

| Flow | Entry point | Service |
|---|---|---|
| Sign up | `POST /api/auth/signup` | `registerUserWithOrganization` |
| Login (web) | `POST /api/auth/login` | `authenticateUser` |
| Login (extension) | `POST /api/extension/auth/login` | `authenticateUser` |
| Logout | `POST /api/auth/logout` | `deleteSessionByToken` |
| Email verification | `POST /api/auth/verify-email` | `verifyEmailAddressFromTokenInDatabase` |
| Forgot password | `POST /api/auth/forgot-password` | `sendForgotPasswordNotificationFromDatabase` |
| Reset password | `POST /api/auth/reset-password` | `completePasswordResetFromTokenInDatabase` |
| Change password | `POST /api/auth/change-password` | `changeAuthenticatedUserPasswordInDatabase` |

## Key Functions

- `requireAuthenticatedUser(request)` — throws `AuthenticationError` if no valid session; used in user-facing routes.
- `requireAdminUser(request)` — additionally checks for admin role; used in all `/api/admin/*` routes.
- `requireAgentApiUser(request)` — validates agent API key; used in `/api/agent/*`.
- `requireExtensionAuthenticatedUser(request)` — reads auth from `Authorization: Bearer` header; used in extension routes.
- `getAuthenticatedSessionFromRequest(request)` — non-throwing session read.

## Token Store

Short-lived action tokens (email verification, password reset) live in `src/lib/auth/auth-action-token-store.ts` and are separate from session tokens stored in `auth-user-store.ts`.

## Signup Side Effects

On successful signup, `sendSignupNotificationsFromDatabase` fires the welcome email and email-verification email.