---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [import, export, jobs, data-management]
status: active
---

# Bug Data Import / Export

## Overview

The admin bug-data panel (`app/(authenticated)/admin/bug-data/`) allows bulk import and export of bug data via asynchronous jobs.

## Job Model

`BugDataManagementJob` fields:

| Field | Description |
|---|---|
| `jobType` | `"import"` or `"export"` |
| `status` | `queued → running → completed / completed_with_warnings / failed` |
| `sourceFileName` | Input file for imports |
| `resultFilePath` | Output archive for exports |
| `summary` | JSON object with counts (bugCount, historyEntryCount, attachmentCount, missingFileCount) |
| `warnings` / `errors` | Per-job issue lists |
| `downloadUrl` | Presigned URL for export download (populated after completion) |

## Import Flow

1. Upload CSV → validation pass (`bug-data-import-service.ts`) returns `BugImportValidationResult`
   - per-row validation with field-level issues
2. User confirms → execution pass returns `BugImportExecutionResult`
   - `duplicateStrategy`: `skip_existing` or `update_existing`
   - summary: `createdCount`, `updatedCount`, `skippedCount`, `failedCount`

## Export Flow

1. Admin triggers export → job queued
2. Worker processes bugs, history, and attachments into an archive
3. `BugExportManifestSummary` is stored in job summary
4. Download URL made available on job completion

## Job Store

`bug-data-job-store.ts` persists job records. `bug-data-admin-service.ts` exposes admin-level operations.

## Helper Utilities (`bug-data-management-panel.helpers.ts`)

- `summarizeBugDataJobs(jobs)` — counts by status, identifies latest import/export
- `formatBugDataJobSummary(job)` — human-readable summary string
- `formatBugImportValidationSummary(validation)` — total/valid/invalid row counts
- `getBugImportRowsWithIssues(validation)` — rows with validation failures
- `formatBugImportExecutionSummary(summary)` — created/updated/skipped/failed counts
- `getBugDataJobIssueMessages(job)` — merged error + warning message list