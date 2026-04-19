---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [nextjs, react, typescript, postgresql]
status: active
---

# ADR-001: Technology Stack

## Decision

Build Bug Tracker as a **Next.js 16 monolith** (App Router) with TypeScript, React 19, PostgreSQL, and SendGrid.

## Context

The project needed a full-stack TypeScript application that could ship both the authenticated web UI and the REST API from a single codebase with minimal operational overhead.

## Rationale

**Why:** Next.js App Router collocates API Route Handlers with UI pages, eliminating a separate API service and reducing deployment surface. TypeScript throughout enables shared types between client UI, API routes, and service layer with no duplication.

**Why PostgreSQL:** Relational data model (orgs → projects → bugs → comments/history) maps naturally to SQL. The in-memory test backend (swapped via `BUG_TRACKER_TEST_DB_BACKEND=memory`) allows fast unit tests without Docker.

**Why SendGrid:** Transactional email with template management; optional for local dev (variables left empty).

**How to apply:** Avoid adding a separate Express/Fastify API server. New backend logic belongs in `src/lib/*` service/store files, surfaced via Next.js Route Handlers.