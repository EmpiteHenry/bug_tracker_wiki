---
type: tech-debt
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [tech-debt, chrome-extension]
status: active
---

# Chrome Extension — Limited Implementation

## Current State

The Chrome extension (`extension/` directory) has a basic popup UI (`popup.html`, `popup.js`, `popup.css`) and a manifest. The backend API routes under `/api/extension/` exist for auth, bug submission, project listing, and version checking.

## Known Gaps

- Extension popup implementation details unknown from source review
- No automated tests for extension flows in the test suite
- Extension version compatibility check endpoint exists (`/api/extension/version`) but compatibility rules are in `extension-version.ts` — needs verification that version negotiation is implemented

## Impact

Extension users may encounter issues if the popup JS does not correctly call the extension API endpoints.

## Recommended Action

- Add E2E tests covering extension auth and bug submission flows
- Document the extension installation and testing process in a runbook