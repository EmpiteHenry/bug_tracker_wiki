---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 5
tags: [extension, chrome, browser]
status: active
---

# Chrome Extension

## Overview

The browser extension allows users to report bugs directly from any web page without leaving the browser. Source lives in `extension/` at the repo root.

| File | Purpose |
|---|---|
| `extension/manifest.json` | Chrome extension manifest |
| `extension/popup.html` | Extension popup UI |
| `extension/popup.js` | Popup logic (auth, bug submission) |
| `extension/popup.css` | Popup styles |

## Extension API

Separate API routes prefixed `/api/extension/` provide:

| Endpoint | Purpose |
|---|---|
| `POST /api/extension/auth/login` | Authenticate and return a bearer token |
| `POST /api/extension/auth/logout` | Invalidate the extension session |
| `GET /api/extension/auth/session` | Validate existing session |
| `GET /api/extension/projects` | List projects available to the user |
| `POST /api/extension/bugs` | Create a bug with evidence |
| `POST /api/extension/bugs/:bugId/evidence` | Attach screenshot/recording to existing bug during a testing session |
| `GET /api/extension/version` | Current supported extension version |

Extension auth uses `Authorization: Bearer <token>` headers (not cookies).

## Testing Sessions

When the extension is active during a QA session, `requireActiveBugTestingSession` gates evidence uploads. The testing session model (`testing-session-*.ts`) tracks active QA sessions per user.

## Version Gating

`extension-version.ts` compares the extension's reported version against the server's minimum supported version. Outdated extensions receive a version-mismatch response.

## Install Prompt

`app/(authenticated)/dashboard/extension-install-panel.tsx` surfaces an install CTA to users who have not yet installed the extension.