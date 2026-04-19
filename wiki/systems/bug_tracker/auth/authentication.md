---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 15
tags: [auth, session, jwt, api-key, extension]
status: active
---

# Authentication

Bug Tracker supports three authentication modes depending on the client type.

## 1. Session Cookie (Web UI)

- On login/signup a session token is created in the database and set as an `HttpOnly` cookie.
- Session cookie name and expiry come from `src/lib/auth/session.ts` (`getSessionCookieName`, `getSessionMaxAgeSeconds`, `getSessionExpirationDate`).
- Requests are authenticated via `requireAuthenticatedUser` / `getAuthenticatedSessionFromRequest` in `src/lib/auth/auth-service.ts`.
- Logout deletes the session row and clears the cookie.

## 2. Extension Bearer Token

- The Chrome extension uses a separate login endpoint (`/api/extension/auth/login`) that returns a token in the response body.
- Subsequent requests include the token as a `Authorization: Bearer <token>` header.
- Authenticated via `getAuthenticatedSessionFromExtensionRequest` / `requireExtensionAuthenticatedUser`.

## 3. Agent / API Key

- Headless agent callers use an API key issued through Settings → API Access.
- Keys are managed via `src/lib/settings/api-key-service.ts`.
- Routes use `requireAgentApiUser` from `src/lib/bugs/agent-api.ts`.

## Password Flows

| Flow | Service | Notes |
|---|---|---|
| Registration | `auth-service.ts → registerUserWithOrganization` | Creates user + default org; sends verification email |
| Email verification | `email-verification-service.ts` | Token consumed once via `/api/auth/verify-email` |
| Forgot password | `forgot-password-service.ts` | Sends action token via SendGrid |
| Reset password | `reset-password-service.ts` | Consumes token, updates hash |
| Change password | `change-password-service.ts` | Requires current password for authenticated users |
| Password hashing | `src/lib/auth/password.ts` | Wraps bcrypt-compatible hashing |

## Action Tokens

Short-lived one-time tokens for email verification and password reset are stored in `auth_action_tokens` table, managed by `src/lib/auth/auth-action-token-store.ts`.

## JWT Settings

Administrators can configure JWT signing parameters in Settings → Auth JWT. This is managed by `src/lib/settings/auth-jwt-service.ts` and surfaced via `components/settings/auth-jwt-settings-panel.tsx`.

## Organization Access Control

- Users must belong to an active organization session to access most routes (`requireActiveOrganizationSession`).
- Admin routes require admin role via `requireAdminUser`.
- Organization-level admin access uses `requireOrganizationAdminAccessForUser` from `organization-access-service.ts`.