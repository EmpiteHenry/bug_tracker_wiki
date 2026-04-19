---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 3
tags: [storage, attachments, files]
status: active
---

# File Storage

Bug attachments are stored on the local filesystem.

## Implementation

`src/lib/storage/attachment-storage.ts` provides the `attachmentStorage` singleton, exported from `src/lib/storage/index.ts`.

The storage layer is used by bug attachment API routes to:
- Save uploaded files to disk
- Stream files back on download requests
- Delete files when attachments are removed

## Path Conventions

File paths are recorded in the `attachment` database table as `resultFilePath`. The export job also stores result files on disk with a path stored in `BugDataManagementJob.resultFilePath`.

## Known Consideration

The current implementation uses local disk storage, which means attachments are node-local and not replicated. For multi-node deployments, an external object store (S3 etc.) would be needed.

See [tech-debt: storage](../../tech-debt/bug_tracker-local-storage.md) for the tracked item.