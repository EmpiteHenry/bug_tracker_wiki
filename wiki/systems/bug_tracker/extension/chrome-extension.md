---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [extension, chrome, browser-extension]
status: active
---

# Chrome Extension API

The browser extension (`extension/`) allows users to report bugs directly from any web page.

## Extension Files

```
extension/
  manifest.json   — Chrome extension manifest
  popup.html      — extension popup UI
  popup.css       — popup styles
  popup.js        — popup logic
```

## Extension API Routes

All under `/api/extension/`:

| Route | Purpose |
|---|---|
| `POST /api/extension/auth/login` | Login with email/password, returns token in JSON |
| `POST /api/extension/auth/logout` | Invalidate extension session |
| `GET /api/extension/auth/session` | Check current session |
| `GET /api/extension/projects` | List projects for bug assignment |
| `POST /api/extension/bugs` | Submit a bug from extension |
| `POST /api/extension/bugs/[bugId]/evidence` | Attach evidence to a bug during a testing session |
| `GET /api/extension/version` | Extension version info |

## Auth Difference

Extension uses `Authorization` header (Bearer token) instead of a session cookie. `getAuthenticatedSessionFromExtensionRequest()` reads from this header.

## Testing Session Integration

`requireActiveBugTestingSession()` — extension evidence uploads are gated on an active QA testing session for the bug.

## Version Management

`src/lib/extension/extension-version.ts` + `extension-service.ts` — tracks compatible extension versions.

## Install UI

`/chrome-extension` page and `/dashboard` → `extension-install-panel.tsx` guide users through installation.