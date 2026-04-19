---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 20
tags: [bugs, crud, attachments, comments, history, import, export]
status: active
---

# Bug Management

## Bug Lifecycle

Bugs progress through these statuses (defined in `all-bugs-filters.ts`):

```
New → Triaged → In Progress → Ready for QA → Closed
```

Bugs can also be **archived** (`is_archived` flag) without closing.

## Core Fields

| Field | Type | Notes |
|---|---|---|
| `title` | string | Required |
| `description` | string | Required |
| `severity` | string | e.g., Critical, High, Medium, Low |
| `status` | string | See lifecycle above |
| `projectId` | number | Required — scopes bug to a project |
| `sectionId` | number | Optional section within project |
| `reporterUserId` | number | Optional override |
| `assigneeUserId` | number \| null | Optional |
| `is_archived` | boolean | Soft archive |

## Services and Stores

| File | Role |
|---|---|
| `bug-service.ts` | Core CRUD: create, get, update, list, bulk update, delete attachment |
| `bug-comment-service.ts` | Comment CRUD |
| `bug-comment-store.ts` | DB access for comments |
| `bug-history-service.ts` | Timeline of field changes |
| `bug-history-store.ts` | DB access for history |
| `attachment-store.ts` | DB records for attachments |
| `attachment-storage.ts` (`src/lib/storage/`) | File system storage for attachment blobs |

## Attachments

- Uploaded via `POST /api/bugs/[bugId]/attachments` (multipart form)
- Stored on disk via `attachmentStorage` from `src/lib/storage/`
- Downloaded via `GET /api/bugs/[bugId]/attachments/[attachmentId]` (streams file)
- Deleted via `DELETE` on the same path

## Bug History / Audit Trail

Every field change is recorded in `bug_history`. The timeline is exposed at `GET /api/bugs/[bugId]/history` (admin only).

Helper formatters in `bug-history.helpers.ts`:
- `formatBugHistoryEventLabel` — human-readable event name
- `getBugHistoryEntrySource` — "User", "Import", or "System"
- `formatBugHistoryChange` — diff string for a changed field

## Bulk Operations

`PATCH /api/bugs/bulk` accepts `{ bugIds: number[], status: string }` and updates multiple bugs in one call.

## Testing Sessions

QA testers can create **testing sessions** to group bugs found during a structured test run:

- `testing-session-service.ts` manages session lifecycle
- `testing-session-log-service.ts` manages per-session log entries
- Sessions are surfaced via the extension and the testing session API

## Filters and Sorting

`src/app/(authenticated)/bugs/all-bugs-filters.ts` defines:
- Filter state: project, status, reporter, date range, archived flag
- Sort fields: `createdAt`, `updatedAt`, `status`, `project`
- Sort directions: `asc` / `desc`
- Validation and URLSearchParams serialization helpers