---
type: tech-debt
owner: engineering
last_updated: 2026-04-19
source_count: 2
tags: [tech-debt, storage, attachments, scalability]
status: active
---

# Tech Debt: Local Disk Storage for Attachments

## Problem

File attachments (bug evidence, export archives) are stored on the local filesystem via `src/lib/storage/attachment-storage.ts`. This works for a single-node deployment but creates several problems:

- **No redundancy** — disk failure loses all attachments
- **No horizontal scaling** — adding a second app server means each server has a different set of files
- **No CDN** — file downloads are proxied through the Next.js server, adding load

## Affected Code

- `src/lib/storage/attachment-storage.ts`
- `src/lib/storage/index.ts`
- `src/app/api/bugs/[bugId]/attachments/` routes (GET streams files)
- `src/lib/bugs/bug-data-export-service.ts` (writes export archives to disk)

## Suggested Fix

Replace `attachment-storage.ts` with an S3-compatible adapter (AWS S3, Cloudflare R2, MinIO for self-hosted). The interface is already abstracted behind `attachmentStorage` singleton, so the swap would be localized to that module.

For exports, use pre-signed URLs so downloads don't flow through the app server.

## Priority

Medium — acceptable for single-node deployments but blocks any scale-out work.