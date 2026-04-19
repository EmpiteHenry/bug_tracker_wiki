---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [settings, api-keys, jwt, access]
status: active
---

# Settings & API Access

## API Keys

`src/lib/settings/api-key-service.ts` — CRUD for personal API keys.  
`src/lib/settings/api-access-config-service.ts` — org-level API access configuration.

UI: `components/settings/api-key-settings-panel.tsx`

## JWT Auth Settings

`src/lib/settings/auth-jwt-service.ts` — JWT signing config for API consumers.  
UI: `components/settings/auth-jwt-settings-panel.tsx`

## API Access Routes

| Route | Purpose |
|---|---|
| `GET/POST /api/settings/api-access` | List / create API keys |

## API Documentation

OpenAPI spec at `public/openapi.json`. Rendered at `/settings/api-docs` using `swagger-ui-react`.  
Component: `components/settings/api-docs-viewer.tsx`

## Password Management

`components/settings/password-management-panel.tsx` — change password UI (calls `POST /api/auth/change-password`).