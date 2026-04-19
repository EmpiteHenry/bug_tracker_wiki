---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [chrome-extension, browser-extension, evidence]
status: active
---

# Chrome Extension

The Chrome extension allows testers to report bugs directly from any web page without switching to the bug tracker UI.

## Extension Files

```
extension/
├── manifest.json   — extension manifest (Manifest V3)
├── popup.html      — extension popup UI
├── popup.css       — popup styles
└── popup.js        — popup logic
```

There is also an install guide page at `src/app/chrome-extension/page.tsx` and a dashboard prompt in `src/app/(authenticated)/dashboard/extension-install-panel.tsx`.

## Authentication

The extension cannot share cookies with the web app (cross-origin). It authenticates separately:

1. User opens popup, enters credentials
2. Extension calls `POST /api/extension/auth/login` → receives a session token
3. Token is stored in extension storage and sent as `Authorization: Bearer <token>` on all subsequent requests

Logout: `POST /api/extension/auth/logout` invalidates the server-side session.
Session check: `GET /api/extension/auth/session` returns current user or 401.

## Bug Submission

From the popup:
1. `GET /api/extension/projects` — load available projects
2. User fills out bug form
3. `POST /api/extension/bugs` — submit bug
4. Optional: `POST /api/extension/bugs/[bugId]/evidence` — upload screenshot

Evidence uploads attach the image to the bug and link it to the active testing session.

## Version Check

`GET /api/extension/version` returns the expected extension version. The extension service (`src/lib/extension/extension-service.ts`) and version module (`src/lib/extension/extension-version.ts`) manage version compatibility checks — useful for prompting users to update an outdated extension.

## Testing Sessions

When a tester is active, the extension associates submitted bugs with the current testing session (see [Bug Management](../bugs/bug-management.md#testing-sessions)). This allows grouping all bugs found in a single testing pass.