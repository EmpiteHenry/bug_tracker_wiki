---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 15
tags: [api, bugs, attachments, comments, history]
status: active
---

# Bug Management API

All bug endpoints require an active session cookie and an active organization session.

## Core Bug Endpoints

### GET /api/bugs
List bugs with optional filters.

**Query params**: `projectId`, `status`, `reporterUserId`, `startDate`, `endDate`, `showArchived`, `sortField`, `sortDirection`

### POST /api/bugs
Create a new bug.

**Body**: `{ title, description, severity, status?, reporterUserId?, assigneeUserId?, projectId, sectionId }`

### GET /api/bugs/[bugId]
Fetch a single bug by ID.

### PATCH /api/bugs/[bugId]
Update a bug's fields.

**Body** (all optional): `{ title, description, severity, status, assigneeUserId, projectId, sectionId, is_archived }`

### PATCH /api/bugs/bulk
Bulk-update status across multiple bugs.

**Body**: `{ bugIds: number[], status: string }`

## Attachments

### POST /api/bugs/[bugId]/attachments
Upload a file attachment (multipart form data).

### GET /api/bugs/[bugId]/attachments/[attachmentId]
Stream an attachment file back to the client.

### DELETE /api/bugs/[bugId]/attachments/[attachmentId]
Delete an attachment.

## Comments

### GET /api/bugs/[bugId]/comments
List all comments for a bug.

### POST /api/bugs/[bugId]/comments
Create a comment.

**Body**: `{ body: string, visibility?: string }`

## History

### GET /api/bugs/[bugId]/history
Returns a timeline of change events for a bug. Requires organization admin role.

## Bug Filter State

The client-side filter state type `AllBugsFilterState` is defined in `src/app/(authenticated)/bugs/all-bugs-filters.ts`. Valid statuses:
`""` | `"New"` | `"Triaged"` | `"In Progress"` | `"Ready for QA"` | `"Closed"`

## Service Files

| File | Responsibility |
|---|---|
| `src/lib/bugs/bug-service.ts` | Core CRUD, list, bulk update |
| `src/lib/bugs/bug-comment-service.ts` | Comment create/list |
| `src/lib/bugs/bug-history-service.ts` | Change timeline |
| `src/lib/bugs/attachment-store.ts` | Attachment DB records |
| `src/lib/storage/attachment-storage.ts` | Disk storage for files |
| `src/lib/bugs/errors.ts` | `BugServiceError` class |