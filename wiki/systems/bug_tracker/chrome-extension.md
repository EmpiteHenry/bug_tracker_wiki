---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [chrome-extension, browser-extension, evidence, auth]
status: active
---

# Chrome Extension

## Overview

A browser extension (`extension/`) that lets users submit bugs and attach evidence (screenshots/recordings) directly from their browser without switching to the main app.

## Extension Files

```
extension/
├── manifest.json    — Chrome extension manifest
├── popup.html       — Extension popup UI
├── popup.css        — Popup styles
└── popup.js         — Popup logic (calls extension API)
```

A dedicated page at `/chrome-extension` provides installation instructions.

## Extension API

Extension requests use header-based authentication (not session cookies). The server reads auth from headers via `getAuthenticatedSessionFromExtensionRequest`.

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Authenticate and get token |
| POST | `/api/extension/auth/logout` | Invalidate token |
| GET | `/api/extension/auth/session` | Validate current token |
| GET | `/api/extension/projects` | List available projects |
| POST | `/api/extension/bugs` | Submit a bug |
| POST | `/api/extension/bugs/[bugId]/evidence` | Upload evidence for a bug |
| GET | `/api/extension/version` | Extension version check |

## Evidence Upload

`POST /api/extension/bugs/[bugId]/evidence` requires an active testing session (`requireActiveBugTestingSession`). Evidence is stored via `attachmentStorage` and linked to the bug as an attachment.

## Testing Sessions

`src/lib/bugs/testing-session-service.ts` manages active testing sessions. A session must be active for evidence to be uploaded — this ties extension bug submissions to a structured QA session.
