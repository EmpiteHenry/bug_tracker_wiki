---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [bugs, lifecycle, history, comments, attachments]
status: active
---

# Bug Lifecycle

## Statuses

Bugs move through these states (defined in `all-bugs-filters.ts`):

```
New → Triaged → In Progress → Ready for QA → Closed
```

Bugs can also be **archived** (`is_archived` flag) independently of status.

## Core Operations

| Operation | API | Service function |
|---|---|---|
| Create | `POST /api/bugs` | `createBug` |
| Read | `GET /api/bugs/:bugId` | `getBugById` |
| Update | `PATCH /api/bugs/:bugId` | `updateBug` |
| List | `GET /api/bugs` | `listBugs` |
| Bulk status update | `PATCH /api/bugs/bulk` | `bulkUpdateBugs` |
| Add comment | `POST /api/bugs/:bugId/comments` | `createBugComment` |
| List comments | `GET /api/bugs/:bugId/comments` | `listBugCommentsForBug` |
| Add attachment | `POST /api/bugs/:bugId/attachments` | `createBugAttachment` |
| Download attachment | `GET /api/bugs/:bugId/attachments/:id` | `getAttachmentForBug` |
| Delete attachment | `DELETE /api/bugs/:bugId/attachments/:id` | `deleteBugAttachment` |
| View history | `GET /api/bugs/:bugId/history` | `listBugHistoryTimelineEntries` |

## History Tracking

Every mutation records a `BugHistoryTimelineEntry` via `bug-history-service.ts`. Entries carry `eventType`, `changedFields` (with `previousValue` / `nextValue`), and a source classification (user action vs. import vs. system).

Helper `getBugHistoryEntrySource` in `bug-history.helpers.ts` classifies entries:
- `bug_import_*` events → "Import"
- `bug_*` events → user name
- everything else → "System"

## Filtering & Sorting

`AllBugsFilterState` (in `all-bugs-filters.ts`) controls the list view:

- Filter by: `projectId`, `status`, `reporterUserId`, `startDate`, `endDate`, `showArchived`
- Sort by: `createdAt`, `updatedAt`, `status`, `project` (asc/desc)

`buildAllBugSearchParams` validates then serialises the filter state into `URLSearchParams`.

## Severity

Severity badges rendered by `components/bugs/severity-badge.tsx`. Values defined by the bug service (not enumerated in the truncated source, but used in API body validation as strings).

## Attachments

Files stored via `attachmentStorage` (`src/lib/storage/attachment-storage.ts`). Served through the authenticated attachment route; access requires an active organization session and ownership verification.