---
type: runbook
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [import, export, csv, bulk, admin]
status: active
---

# Bug Data Import & Export

## Import

Admin workflow:

1. Prepare a CSV file with bug data
2. Upload via Admin → Bug Data → Import
3. System validates the CSV (row by row)
4. Review validation result:
   - Total / valid / invalid row counts
   - Per-row issues with field + message
5. Choose duplicate strategy: `skip_existing` or `update_existing`
6. Execute import
7. Monitor job status: `queued → running → completed | completed_with_warnings | failed`
8. Review execution summary: created / updated / skipped / failed counts

## Export

1. Admin → Bug Data → Export
2. System creates an async export job
3. Job serializes bugs + history entries + attachments into a zip
4. Manifest records: bugCount, historyEntryCount, attachmentCount, missingFileCount
5. Download link available once job status is `completed`

## Job Statuses

| Status | Meaning |
|---|---|
| `queued` | Waiting to start |
| `running` | In progress |
| `completed` | Success |
| `completed_with_warnings` | Success with non-fatal issues |
| `failed` | Fatal error — check job errors array |

## Troubleshooting

- Check `job.errors` and `job.warnings` arrays on a failed job
- `missingFileCount > 0` in export manifest means some attachment files were not found on disk
- Invalid rows in import are skipped; details in `validation.rows` filtered by `issues.length > 0`
