---
type: decision
owner: engineering
last_updated: 2026-04-19
source_count: 3
tags: [decision, next.js, postgresql, typescript]
status: active
---

# ADR-001: Technology Stack

## Decision

Build Bug Tracker as a Next.js 16 full-stack application with PostgreSQL, TypeScript throughout, and shadcn/ui for the component library.

## Why Next.js App Router

- Co-locates API routes and UI in a single deployable unit — reduces operational overhead vs. separate frontend/backend repos
- React Server Components allow direct DB access in render without extra API roundtrips for read-heavy admin pages
- Built-in file-based routing matches the admin section hierarchy naturally

**Why Webpack mode**: `next dev --webpack` and `next build --webpack` are used explicitly (not Turbopack), suggesting a need for Webpack-specific plugin compatibility or stability.

## Why PostgreSQL over SQLite / other

- Multi-user concurrent writes require a proper RDBMS
- Existing `pg` ecosystem and full SQL support for complex queries (grouped errors, performance aggregation)
- Docker Compose makes local setup straightforward
- An in-memory test database (`BUG_TRACKER_TEST_DB_BACKEND=memory`) avoids requiring Docker for fast unit tests

## Why Postgres Worker Thread

The DB client runs in a separate worker thread (`postgres-worker.ts` bundled with esbuild). This prevents blocking the Next.js event loop on DB I/O, keeping API routes responsive under load.

## Why Zod 4

Request body validation uses Zod 4 (latest major). Provides runtime type safety at API boundaries without duplicating TypeScript types.

## Why shadcn/ui

Unstyled, composable primitives using Radix/Base UI. Components live in `src/components/ui/` and are owned by the project (not a runtime dependency), allowing full customization without forking.

## Why MCP SDK

`@modelcontextprotocol/sdk` enables exposing bug operations as structured tools for AI assistants. This is an intentional design choice to support AI-assisted bug triage workflows alongside the existing agent API.