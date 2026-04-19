---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [extension, chrome, browser-extension, evidence]
status: active
---

# Chrome Extension

A browser extension enabling users to submit bugs and attach evidence directly from their browser.

## Files

| File | Role |
|---|---|
| `extension/manifest.json` | Chrome extension manifest |
| `extension/popup.html` + `popup.js` + `popup.css` | Extension popup UI |

## API Surface (`/api/extension/*`)

The extension has its own API surface, separate from the web app, using **Bearer token auth** (not session cookies).

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Login, returns auth token |
| POST | `/api/extension/auth/logout` | Logout |
| GET | `/api/extension/auth/session` | Get current session |
| GET | `/api/extension/projects` | List available projects |
| POST | `/api/extension/bugs` | Submit a bug |
| GET/POST | `/api/extension/bugs/[bugId]/evidence` | Attach evidence to a bug during a testing session |
| GET | `/api/extension/version` | Extension version check |

## Version Management

`src/lib/extension/extension-version.ts` — version compatibility checking between extension and server.

`src/lib/extension/extension-service.ts` — service layer for extension operations.

## Testing Session Integration

When a testing session is active, evidence (screenshots, recordings) submitted via `POST /api/extension/bugs/[bugId]/evidence` is associated with both the bug and the active testing session.

Requires `requireActiveBugTestingSession()` guard from `testing-session-service.ts`.

## Install Guide

User-facing install instructions are surfaced at `/chrome-extension` and via the dashboard's `extension-install-panel.tsx`.