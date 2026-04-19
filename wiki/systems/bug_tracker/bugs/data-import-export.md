---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [import, export, data, jobs, admin]
status: active
---

# Bug Data Import / Export

Admins can bulk-import bugs from CSV and export the full bug dataset as a ZIP archive. Both operations run as async jobs tracked in `bug_data_jobs`.

## Job Model

```ts
type BugDataManagementJob = {
  id: number;
  organizationId: number;
  jobType: "import" | "export";
  status: "queued" | "running" | "completed" | "completed_with_warnings" | "failed";
  sourceFileName: string | null;
  resultFilePath: string | null;
  summary: Record<string, unknown> | null;
  warnings: Array<{ message: string }>;
  errors: Array<{ message: string }>;
  startedAt: string | null;
  completedAt: string | null;
};
```

## Import Flow

1. Admin uploads a CSV file
2. `bug-data-import-service.ts` validates rows: checks required fields, data types
3. Validation result returns per-row issues (`BugImportValidationRow[]`)
4. If valid, admin confirms and triggers execution
5. Duplicate handling strategy: `skip_existing` or `update_existing`
6. Execution summary: created / updated / skipped / failed counts

**Helper formatters** (`bug-data-management-panel.helpers.ts`):
- `formatBugImportValidationSummary` — total / valid / invalid row counts
- `getBugImportRowsWithIssues` — filter to invalid rows
- `formatBugImportExecutionSummary` — created • updated • skipped • failed

## Export Flow

1. Admin triggers export
2. `bug-data-export-service.ts` packages bugs, history entries, and attachments into a ZIP
3. Job completes with a manifest: bug count, history entry count, attachment count, missing file count
4. Admin downloads the file via `downloadUrl` on the job record

**Helper formatters**:
- `formatBugExportManifestSummary` — bugs • history entries • attachments • missing files

## Job Store

`bug-data-job-store.ts` handles job CRUD. `bug-data-admin-service.ts` exposes admin-facing operations (list jobs, trigger import/export, download).

## UI

The admin panel is at `/admin/bug-data`. Summary helpers in `bug-data-management-panel.helpers.ts` drive the display logic without touching React directly.