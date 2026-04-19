---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [settings, api-keys, jwt, integrations, openapi]
status: active
---

# Settings

## API Access

Users can generate API keys for programmatic access. Managed by:
- `api-key-service.ts` — create, list, revoke keys
- `api-access-config-service.ts` — org-level API access configuration
- `GET|POST|DELETE /api/settings/api-access`

UI: `src/components/settings/api-key-settings-panel.tsx`

## JWT Settings

`auth-jwt-service.ts` manages JWT signing configuration. UI: `src/components/settings/auth-jwt-settings-panel.tsx`.

## API Documentation

The app ships an OpenAPI spec at `public/openapi.json`. A Swagger UI viewer is available at `/settings/api-docs` and `/api-docs` (using `swagger-ui-react`).

UI: `src/components/settings/api-docs-viewer.tsx`

## Integrations

Settings page at `/settings/integrations` — integration configuration panel (source not shown in detail).

## Password Management

`src/components/settings/password-management-panel.tsx` — UI for the change-password flow (calls `POST /api/auth/change-password`).
