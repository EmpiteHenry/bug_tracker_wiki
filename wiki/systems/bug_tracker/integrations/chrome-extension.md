---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [extension, chrome, browser, integration]
status: active
---

# Chrome Extension

A Manifest V3 Chrome extension allows users to submit bugs directly from the browser without leaving the page they are testing.

## Extension Source

Located in `extension/`:

| File | Purpose |
|---|---|
| `manifest.json` | Chrome extension manifest (V3) |
| `popup.html` | Extension popup UI shell |
| `popup.css` | Popup styles |
| `popup.js` | Popup logic — login, project picker, bug submission |

## Extension API Routes

The extension communicates with the main app via dedicated routes under `/api/extension/`:

| Endpoint | Purpose |
|---|---|
| `POST /api/extension/auth/login` | Authenticate; returns Bearer token |
| `POST /api/extension/auth/logout` | Invalidate token |
| `GET /api/extension/auth/session` | Validate current token |
| `GET /api/extension/projects` | List accessible projects |
| `POST /api/extension/bugs` | Submit a bug |
| `GET /api/extension/version` | Version compatibility check |

Authentication uses Bearer tokens (not cookies) because the extension cannot share the app's cookie jar cross-origin.

## Evidence Upload

Bugs submitted from the extension can include screenshot evidence. The route `POST /api/extension/bugs/[bugId]/evidence` handles multipart evidence uploads and requires an active testing session (`requireActiveBugTestingSession`).

## Extension Service

`src/lib/extension/extension-service.ts` and `extension-version.ts` handle server-side extension logic:
- Version compatibility validation
- Shared utilities for extension request handling

## In-App Pages

- `/chrome-extension` — installation guide page
- `/dashboard` includes an extension install prompt panel (`extension-install-panel.tsx`)