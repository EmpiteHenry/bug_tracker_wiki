---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [chrome-extension, browser-extension, integration]
status: active
---

# Chrome Extension

## Overview

A browser extension that lets users capture bugs directly from any web page and submit them to the Bug Tracker workspace.

## Extension Source (`extension/`)

| File | Purpose |
|---|---|
| `manifest.json` | Chrome extension manifest (permissions, popup registration) |
| `popup.html` | Extension popup UI shell |
| `popup.css` | Popup styles |
| `popup.js` | Popup logic (auth, bug submission) |

## Extension API (`/api/extension/`)

The extension authenticates separately from the browser app using a JWT (not a session cookie):

1. `POST /api/extension/auth/login` — email + password → returns JWT
2. JWT is stored by the extension and sent as `Authorization: Bearer <token>` on subsequent calls
3. `GET /api/extension/auth/session` — validate token and get user info
4. `POST /api/extension/auth/logout` — invalidate token

Bug operations:
- `GET /api/extension/projects` — list projects for selection in popup
- `POST /api/extension/bugs` — create bug (includes evidence from active testing session)
- `GET/POST /api/extension/bugs/[bugId]/evidence` — testing session evidence

Version check:
- `GET /api/extension/version` — returns current expected extension version for update prompts

## Extension Service (`src/lib/extension/`)

- `extension-service.ts` — validates extension auth tokens, resolves user context
- `extension-version.ts` — manages current version string

## Installation Page

`/chrome-extension` page (`src/app/chrome-extension/page.tsx`) — provides installation instructions and download link. Also accessible via the dashboard extension install panel (`extension-install-panel.tsx`).

## Testing Sessions

When the extension is actively testing a specific bug, it maintains a "testing session" (via `testing-session-service`). Evidence (screenshots, log snippets) captured during the session is uploaded as attachments and stored in `testing-session-log-store`.