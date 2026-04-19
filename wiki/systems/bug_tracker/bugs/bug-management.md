---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 20
tags: [bugs, crud, comments, attachments, history, import, export]
status: active
---

# Bug Management

Core domain: creating, reading, updating, and tracking software bugs.

## Data Model (key fields)

| Field | Type | Notes |
|---|---|---|
| `id` | number | Primary key |
| `title` | string | Required |
| `description` | string | Required |
| `severity` | string | e.g. `low`, `medium`, `high`, `critical` |
| `status` | string | `New` → `Triaged` → `In Progress` → `Ready for QA` → `Closed` |
| `reporterUserId` | number | User who filed it |
| `assigneeUserId` | number \| null | Assigned engineer |
| `projectId` | number | Required |
| `sectionId` | number | Project section |
| `is_archived` | boolean | Soft delete |

## API Surface (`/api/bugs/*`)

| Method | Path | Action |
|---|---|---|
| GET | `/api/bugs` | List bugs (filterable) |
| POST | `/api/bugs` | Create bug |
| GET | `/api/bugs/[bugId]` | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Update bug fields |
| PATCH | `/api/bugs/bulk` | Bulk status update |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[id]` | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[id]` | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | Audit timeline |

## Bug List Filters (`all-bugs-filters.ts`)

Filters available on the list view:

- `projectId` — positive integer
- `status` — one of `BUG_FILTER_STATUSES`
- `reporterUserId` — positive integer
- `startDate` / `endDate` — ISO date strings
- `showArchived` — boolean
- `sortField` — `createdAt` | `updatedAt` | `status` | `project`
- `sortDirection` — `asc` | `desc`

Default sort: `createdAt desc`.

## Bug History / Audit Trail

`GET /api/bugs/[bugId]/history` returns a timeline of `BugHistoryTimelineEntry` objects. Each entry includes:
- `eventType` — e.g. `bug_created`, `bug_status_changed`, `bug_import_*`
- `changedFields[]` — `{ field, previousValue, nextValue }`

Source: `bug-history-service.ts` + `bug-history-store.ts`

History entry source is derived by `getBugHistoryEntrySource()`:
- `bug_import_*` events → "Import"
- `bug_*` events → "User"
- Other → "System"

## Bulk Operations

`PATCH /api/bugs/bulk` accepts `{ bugIds: number[], status: string }` — updates status on multiple bugs at once.

## Comments

Comments have a `visibility` field (public/internal). Listed at `GET /api/bugs/[bugId]/comments`.

## Attachments

Files stored via `attachmentStorage` (configured in `src/lib/storage/`). The store layer is `attachment-store.ts`.

## Import / Export

Managed via `bug-data-import-service.ts` and `bug-data-export-service.ts`. Jobs tracked in `bug-data-job-store.ts`.

Job statuses: `queued` → `running` → `completed` | `completed_with_warnings` | `failed`

Export manifest includes: bug count, history entry count, attachment count, missing file count.

Import supports duplicate strategies: `skip_existing` | `update_existing`.

## Key Service Files

| File | Role |
|---|---|
| `src/lib/bugs/bug-service.ts` | Core CRUD: `createBug`, `getBugById`, `updateBug`, `listBugs`, `bulkUpdateBugs` |
| `src/lib/bugs/bug-comment-service.ts` | Comment management |
| `src/lib/bugs/bug-history-service.ts` | Timeline entries |
| `src/lib/bugs/bug-data-import-service.ts` | CSV/batch import |
| `src/lib/bugs/bug-data-export-service.ts` | Export to archive |
| `src/lib/bugs/errors.ts` | `BugServiceError` typed errors |