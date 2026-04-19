---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [import, export, data, admin]
status: active
---

# Bug Data Import & Export

Admin users can bulk-import bugs from CSV and export bugs to ZIP archives via the admin panel at `/admin/bug-data`.

## Import Flow

1. **Upload CSV** — admin selects file in the bug data management panel
2. **Validation** — system validates each row; reports invalid rows with field-level issues
3. **Review** — admin sees validation summary: total / valid / invalid row counts; invalid rows listed with issues
4. **Execute** — admin chooses duplicate strategy and confirms
5. **Job tracking** — job status polled until `completed`, `completed_with_warnings`, or `failed`

### Duplicate Strategies

| Strategy | Behaviour |
|---|---|
| `skip_existing` | Skip any row where a matching bug already exists |
| `update_existing` | Overwrite fields on matching bugs |

### Import Summary Fields

- `createdCount` — new bugs created
- `updatedCount` — existing bugs updated
- `skippedCount` — rows skipped (duplicate strategy)
- `failedCount` — rows that could not be processed

## Export Flow

1. Admin triggers export from the bug data management panel
2. Job is queued and runs asynchronously
3. On completion, a download link is available (`downloadUrl` on the job record)
4. Export manifest includes: bug count, history entry count, attachment count, missing file count

## Job Statuses

```
queued → running → completed
                 → completed_with_warnings
                 → failed
```

Jobs are stored in `bug_data_jobs`. Running jobs show progress via `bugCount`, `attachmentCount`, and `historyEntryCount` in the summary.

## Checking Job Issues

Any errors or warnings on a job are accessible via the issue summary panel in the admin UI. The `getBugDataJobIssueMessages()` helper merges errors and warnings into a flat list.