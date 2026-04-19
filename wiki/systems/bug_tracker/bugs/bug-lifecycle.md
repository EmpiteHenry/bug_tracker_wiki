---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 16
tags: [bugs, lifecycle, status, history, comments, attachments]
status: active
---

# Bug Lifecycle

## Bug Statuses

```
New → Triaged → In Progress → Ready for QA → Closed
                                           ↘ (reopen) → In Progress
```

Valid filter statuses (from `all-bugs-filters.ts`):
- `New`, `Triaged`, `In Progress`, `Ready for QA`, `Closed`

## Severity Levels

Rendered as badges via `src/components/bugs/severity-badge.tsx`. Specific values are defined in the DB schema and enforced by `bug-service`.

## Bug Fields

| Field | Type | Notes |
|---|---|---|
| `title` | string | Required |
| `description` | string | Required |
| `severity` | string | Validated by service |
| `status` | string | One of the valid statuses |
| `projectId` | number | Required — bugs belong to a project |
| `sectionId` | number | Required — bugs belong to a project section |
| `assigneeUserId` | number \| null | Optional |
| `reporterUserId` | number | Set at creation |
| `is_archived` | boolean | Soft-archive flag |

## Bug History

Every mutation to a bug is recorded in `bug-history-store`. History entries are exposed via:
- `GET /api/bugs/[bugId]/history` (admin only)
- `listBugHistoryTimelineEntries()` in `bug-history-service`

History entries have:
- `eventType` — prefixed with `bug_` for user actions or `bug_import_` for import-originated changes
- `changedFields[]` — array of `{ field, previousValue, nextValue }`

Display helpers in `bug-history.helpers.ts`:
- `formatBugHistoryEventLabel(eventType)` — human-readable event label
- `getBugHistoryEntrySource(entry)` — "System", "Import", or user-sourced
- `formatBugHistoryChange(field)` — formats a field change as `"Old → New"`

## Bug Comments

- `GET /api/bugs/[bugId]/comments` — list comments
- `POST /api/bugs/[bugId]/comments` — create comment with `body` and optional `visibility`
- Served by `bug-comment-service` + `bug-comment-store`

## Attachments

- Uploaded via `POST /api/bugs/[bugId]/attachments` (multipart form)
- Stored via `attachmentStorage` abstraction (`src/lib/storage/attachment-storage.ts`)
- Downloaded via `GET /api/bugs/[bugId]/attachments/[attachmentId]` (streams file)
- Deleted via `DELETE /api/bugs/[bugId]/attachments/[attachmentId]`

## Bulk Operations

`PATCH /api/bugs/bulk` accepts `{ bugIds: number[], status: string }` to update status on multiple bugs at once via `bulkUpdateBugs()`.

## Bug Filters (All Bugs View)

Defined in `all-bugs-filters.ts`:

| Filter | Type |
|---|---|
| `projectId` | positive integer |
| `status` | one of valid statuses |
| `reporterUserId` | positive integer |
| `startDate` / `endDate` | ISO date strings |
| `showArchived` | boolean |
| `sortField` | `createdAt` \| `updatedAt` \| `status` \| `project` |
| `sortDirection` | `asc` \| `desc` |

## Testing Sessions

The extension can start a "testing session" for a bug, uploading evidence (screenshots, logs) via:
- `requireActiveBugTestingSession()` in `testing-session-service`
- Evidence stored as attachments in `testing-session-log-store`