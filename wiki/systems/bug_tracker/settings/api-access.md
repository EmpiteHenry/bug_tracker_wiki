---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [settings, api-key, jwt, access-control]
status: active
---

# Settings & API Access

## API Keys

`src/lib/settings/api-key-service.ts` manages API key generation and validation.

UI: `src/components/settings/api-key-settings-panel.tsx`

API route: `GET/POST /api/settings/api-access`

## JWT Auth

`src/lib/settings/auth-jwt-service.ts` — JWT-based auth tokens for programmatic access.

UI: `src/components/settings/auth-jwt-settings-panel.tsx`

## API Access Config

`src/lib/settings/api-access-config-service.ts` — controls which API access methods are enabled per organization.

## API Documentation

OpenAPI spec at `public/openapi.json`.

Rendered via Swagger UI at `/settings/api-docs`.

Component: `src/components/settings/api-docs-viewer.tsx`.