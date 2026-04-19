---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [nextjs, typescript, postgresql, react]
status: active
---

# ADR: Technology Stack

## Decision

Build the bug tracker as a **Next.js 16 full-stack application** using the App Router, TypeScript, React 19, PostgreSQL, and Tailwind CSS.

## Why

- Next.js App Router colocates API routes and UI in one repo, reducing operational complexity for a team-sized product.
- TypeScript throughout enables shared types between API route handlers and client components without a separate schema layer.
- PostgreSQL provides relational integrity needed for multi-tenant data (orgs → projects → bugs → history).
- Tailwind CSS + shadcn/ui (Radix primitives) gives a component library without a large design-system dependency.

## Consequences

- **Webpack mode** is explicitly forced (`--webpack` in dev and build scripts), suggesting Turbopack was evaluated and had issues.
- The MCP SDK dependency (`@modelcontextprotocol/sdk`) ties the project to LLM tooling from the start.
- No ORM is used — all DB access is raw SQL via `pg`, keeping query control but requiring manual schema management.
- Dual-database testing (in-memory vs PostgreSQL) adds complexity but enables fast unit test feedback loops.
