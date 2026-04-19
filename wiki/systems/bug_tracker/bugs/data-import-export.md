---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [import, export, data-management, jobs, admin]
status: active
---

# Bug Data Import / Export

## Overview

Admins can bulk import and export bugs through a job-based system. Jobs are tracked asynchronously and can be monitored in the admin panel (`/admin/bug-data`).

## Job Model (`BugDataManagementJob`)

```ts
{
  id: number
  organizationId: number
  requestedByUserId: number | null
  jobType: "import" | "export"
  status: "queued" | "running" | "completed" | "completed_with_warnings" | "failed"
  sourceFileName: string | null    // import: uploaded CSV filename
  resultFilePath: string | null    // export: path to output file
  options: Record<string, unknown> // job-specific options
  summary: Record<string, unknown> // counts after completion
  warnings: Array<{ message: string }>
  errors: Array<{ message: string; rowNumber?: number }>
  startedAt: string | null
  completedAt: string | null
  downloadUrl?: string | null      // export: download link
}
```

## Import Flow

1. Admin uploads a CSV file
2. Service validates rows (`bug-data-import-service`) → returns `BugImportValidationResult`
3. Validation result shows: total rows, valid rows, invalid rows, per-row issues
4. If valid, import executes with a `duplicateStrategy`:
   - `skip_existing` — skip rows matching existing bugs
   - `update_existing` — overwrite existing bugs
5. Result contains `BugImportExecutionSummary`: created, updated, skipped, failed counts

## Export Flow

1. Admin triggers export
2. `bug-data-export-service` collects bugs, history entries, and attachments
3. Result: `BugExportManifestSummary` with bug count, history count, attachment count, missing file count
4. Output file available at `resultFilePath` / `downloadUrl`

## Helper Functions (`bug-data-management-panel.helpers.ts`)

| Function | Purpose |
|---|---|
| `summarizeBugDataJobs(jobs)` | Counts jobs by status, finds latest import/export |
| `formatBugDataJobSummary(job)` | Human-readable export summary string |
| `formatBugImportValidationSummary(validation)` | Validation counts string |
| `getBugImportRowsWithIssues(validation)` | Filter rows with validation errors |
| `formatBugImportExecutionSummary(summary)` | Import execution counts string |
| `formatBugExportManifestSummary(manifest)` | Export manifest counts string |
| `formatBugDataJobProgress(job)` | Progress description during running job |
| `formatBugDataJobIssueSummary(job)` | "No issues" or error/warning count |
| `getBugDataJobIssueMessages(job)` | Array of all error + warning messages |

## Key Source Files

| File | Purpose |
|---|---|
| `src/lib/bugs/bug-data-import-service.ts` | CSV parsing and import execution |
| `src/lib/bugs/bug-data-export-service.ts` | Export collection and manifest |
| `src/lib/bugs/bug-data-job-store.ts` | Job record persistence |
| `src/lib/bugs/bug-data-admin-service.ts` | Admin orchestration |