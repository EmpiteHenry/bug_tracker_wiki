---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 18
tags: [bugs, attachments, comments, history, import, export]
status: active
---

# Bug Management System

## Bug Lifecycle

Bugs move through statuses: `New → Triaged → In Progress → Ready for QA → Closed`.

Bugs belong to a **project** and optionally a **section** within that project. They can be archived without closing.

## API Surface

| Method | Path | Description |
|---|---|---|
| GET | `/api/bugs` | List bugs (filter by project, status, reporter, date range, sort) |
| POST | `/api/bugs` | Create bug |
| GET | `/api/bugs/[bugId]` | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Update bug fields |
| PATCH | `/api/bugs/bulk` | Bulk status update |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[id]` | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[id]` | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | Bug change history (admin only) |

## Filter State

`src/app/(authenticated)/bugs/all-bugs-filters.ts` manages client-side filter state and URL serialization.

Sort fields: `createdAt`, `updatedAt`, `status`, `project`.  
Sort directions: `asc`, `desc` (default: `desc` by `createdAt`).

Filter validation throws descriptive errors surfaced in the UI before any API call.

## Bug History

Every mutation to a bug is recorded as a timeline entry in `bug-history-store.ts`. Each entry captures:
- `eventType` (e.g. `bug_created`, `bug_status_changed`, `bug_import_created`)
- `changedFields` — array of `{ field, previousValue, nextValue }`

Helper `getBugHistoryEntrySource` distinguishes user edits, import operations, and system events.

## Attachments

Files are stored via `attachmentStorage` (`src/lib/storage/attachment-storage.ts`). The store layer (`attachment-store.ts`) records metadata. Retrieval streams the file from disk.

## Comments

Comments have a `visibility` field (public/internal). Both the comment list and creation require an active organization session.

## Data Import / Export

Managed as async jobs tracked in `bug-data-job-store.ts`.

**Import flow**: validate CSV → create job → run import → record created/updated/skipped/failed counts  
**Export flow**: create job → serialize bugs + history + attachments → write zip → record manifest

Job statuses: `queued → running → completed | completed_with_warnings | failed`

Duplicate strategies on import: `skip_existing` or `update_existing`.

See type definitions in `src/app/(authenticated)/admin/bug-data/bug-data-management-panel.helpers.ts`.

## Key Files

```
src/lib/bugs/
├── bug-service.ts               — core CRUD, list, bulk update
├── bug-comment-service.ts       — comment creation and listing
├── bug-history-service.ts       — timeline generation
├── bug-data-import-service.ts   — CSV import logic
├── bug-data-export-service.ts   — export/zip generation
├── bug-data-job-store.ts        — async job tracking
├── attachment-store.ts          — attachment metadata
└── errors.ts                    — BugServiceError
```
