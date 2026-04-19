---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 5
tags: [api, chrome-extension, browser]
status: active
---

# Chrome Extension API

The Extension API (`/api/extension/`) serves the companion Chrome browser extension. It uses `Authorization` header tokens rather than cookies (via `getAuthenticatedSessionFromExtensionRequest()`).

## Authentication Endpoints

| Method | Path | Description |
|---|---|---|
| POST | `/api/extension/auth/login` | Login — returns token for extension use |
| POST | `/api/extension/auth/logout` | Invalidate extension session |
| GET | `/api/extension/auth/session` | Check current extension session |

## Bug Submission

### POST /api/extension/bugs
Submit a new bug from the extension (JSON body with title, description, severity, project, etc.)

### POST /api/extension/bugs/[bugId]/evidence
Attach screenshot or other evidence to an existing bug. Requires an active **testing session**.

## Testing Session Support

Extension-submitted bugs can be associated with a testing session (see `testing-session-service.ts`). The API validates `requireActiveBugTestingSession()` before accepting evidence.

## Projects

### GET /api/extension/projects
List projects available to the authenticated user (used to populate the extension's project dropdown).

## Version Check

### GET /api/extension/version
Returns the current expected extension version, used to prompt users to upgrade outdated extensions. Version logic is in `src/lib/extension/extension-version.ts`.

## Extension Source

The extension itself lives in `extension/`:
- `manifest.json` — Chrome manifest
- `popup.html` / `popup.js` / `popup.css` — extension popup UI