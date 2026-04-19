---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 20
tags: [bugs, comments, attachments, history, import, export, testing-session]
status: active
---

# Bug Management

The bug subsystem lives in `src/lib/bugs/`. It covers the full lifecycle: creation, triage, comments, attachments, history tracking, bulk operations, data import/export, and agent-driven work.

## Bug Service (`bug-service.ts`)

Central service. Key operations:

| Function | Description |
|---|---|
| `createBug` | Create bug, record initial history event |
| `getBugById` | Fetch single bug with org membership check |
| `listBugs` | Filtered, sorted, paginated list |
| `updateBug` | Update fields, record history diff |
| `bulkUpdateBugs` | Status update for multiple bugs at once |
| `deleteBugAttachment` | Remove attachment file + record |
| `getAttachmentForBug` | Retrieve attachment with access check |
| `createBugAttachment` | Store uploaded file |
| `listClaimableBugs` | Agent-facing: bugs available for agent pickup |
| `claimBug` | Mark bug as claimed by an agent |
| `updateBugAgentWork` | Update agent state/note on a bug |

`BugServiceError` is thrown for domain violations (not found, permission denied, invalid state).

## Bug Status Values

```
New → Triaged → In Progress → Ready for QA → Closed
```

Bugs can also be archived (`is_archived`). Archived bugs are excluded from default list queries unless `showArchived=true`.

## Severity Levels

Defined in filter constants: used in `all-bugs-filters.ts`. Displayed via `components/bugs/severity-badge.tsx` and `components/bugs/status-badge.tsx`.

## Comments (`bug-comment-service.ts`, `bug-comment-store.ts`)

Comments have a `body` and optional `visibility` field (e.g., internal vs. public). `createBugComment` fires notification events.

## Attachments (`attachment-store.ts`)

Attachment metadata is stored in the database. File bytes are stored via `src/lib/storage/attachment-storage.ts`. The storage module is abstracted behind `src/lib/storage/index.ts` so the backend can be swapped (local disk, S3, etc.).

## History (`bug-history-service.ts`, `bug-history-store.ts`)

Every mutation to a bug is recorded as a history event. `listBugHistoryTimelineEntries` returns a timeline with:

- `eventType` — e.g. `bug_created`, `bug_status_changed`, `bug_import_created`
- `changedFields` — array of `{ field, previousValue, nextValue }`
- source attribution (user, agent, or import)

Helper functions in `src/app/(authenticated)/bugs/[bugId]/bug-history.helpers.ts` format events for display.

## Import / Export (`bug-data-import-service.ts`, `bug-data-export-service.ts`)

Admins can bulk-import bugs from CSV and export bugs to a ZIP archive.

**Import flow:**
1. Upload CSV → validation pass (returns `BugImportValidationResult` with per-row issues)
2. If valid, execute import → `BugImportExecutionResult` with counts (created/updated/skipped/failed)
3. Duplicate strategy: `skip_existing` or `update_existing`

**Export flow:**
1. Request export → creates job record in `bug-data-job-store.ts`
2. Job runs, produces ZIP at `resultFilePath`
3. Manifest records bug count, history entries, attachments, missing files

Jobs are tracked in `BugDataManagementJob` with statuses: `queued → running → completed | completed_with_warnings | failed`.

## Testing Sessions (`testing-session-service.ts`)

A testing session groups bugs submitted by a tester during a defined time window. The extension uses this to correlate bugs reported in the same session. Sessions have a log (`testing-session-log-service.ts`) recording actions taken during the session.

## Filters (`all-bugs-filters.ts`)

`AllBugsFilterState` covers: projectId, status, reporterUserId, startDate, endDate, showArchived, sortField, sortDirection.

`buildAllBugSearchParams` serialises filters to URL search params. `parseAllBugFilters` parses them back. `validateAllBugFilters` throws on invalid values before any DB call.

Sort fields: `createdAt | updatedAt | status | project`. Sort directions: `asc | desc`.