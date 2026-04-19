---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [bugs, lifecycle, status, severity, filters]
status: active
---

# Bug Lifecycle

## Statuses

Bugs move through these statuses (defined in `all-bugs-filters.ts`):

```
New → Triaged → In Progress → Ready for QA → Closed
```

Bugs can also be archived (`is_archived`).

## Severity Levels

Rendered via `severity-badge.tsx`. Levels come from the `severity` field on the bug record.

## Core API

| Method | Route | Purpose |
|---|---|---|
| GET | `/api/bugs` | List bugs with filters |
| POST | `/api/bugs` | Create a bug |
| GET | `/api/bugs/[bugId]` | Get bug detail |
| PATCH | `/api/bugs/[bugId]` | Update bug fields |
| PATCH | `/api/bugs/bulk` | Bulk status update |
| GET | `/api/bugs/[bugId]/comments` | List comments |
| POST | `/api/bugs/[bugId]/comments` | Add comment |
| POST | `/api/bugs/[bugId]/attachments` | Upload attachment |
| GET | `/api/bugs/[bugId]/attachments/[id]` | Download attachment |
| DELETE | `/api/bugs/[bugId]/attachments/[id]` | Delete attachment |
| GET | `/api/bugs/[bugId]/history` | Audit timeline |

## Filtering (`all-bugs-filters.ts`)

Filter state fields: `projectId`, `status`, `reporterUserId`, `startDate`, `endDate`, `showArchived`, `sortField`, `sortDirection`.

Sort fields: `createdAt`, `updatedAt`, `status`, `project`.  
Sort directions: `asc`, `desc` (default: `createdAt desc`).

`buildAllBugSearchParams(filters)` serializes a filter state into `URLSearchParams` after validation.  
`validateAllBugFilters(filters)` throws on invalid values (bad dates, bad IDs, unknown sort fields).

## Bug History

`src/lib/bugs/bug-history-service.ts` tracks field-level changes. Each `BugHistoryTimelineEntry` has:
- `eventType` — e.g. `bug_created`, `bug_status_changed`, `bug_import_*`
- `changedFields` — array of `{ previousValue, nextValue }`

`getBugHistoryEntrySource(entry)` classifies source as "Import", "User", or "System".

## Comments

`src/lib/bugs/bug-comment-service.ts` and `bug-comment-store.ts`. Comments have `body` and optional `visibility`.

## Attachments

`src/lib/bugs/attachment-store.ts` + `attachment-storage.ts`. Files stored via `attachmentStorage` abstraction (local path in dev).

## Bug Data Import / Export

Admin-only. Jobs tracked in `bug-data-job-store.ts` with statuses: `queued`, `running`, `completed`, `completed_with_warnings`, `failed`.

Import supports `skip_existing` and `update_existing` duplicate strategies.  
Export produces a manifest with `bugCount`, `historyEntryCount`, `attachmentCount`, `missingFileCount`.

## Testing Sessions

`src/lib/bugs/testing-session-service.ts` — QA testing sessions attach evidence (screenshots, logs) to bugs via `POST /api/extension/bugs/[bugId]/evidence`.