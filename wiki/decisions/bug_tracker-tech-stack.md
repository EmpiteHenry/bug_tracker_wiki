---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [tech-stack, next.js, typescript]
status: active
---

# ADR-002: Technology Stack

## Decisions

### Next.js App Router (not Pages Router)

All routes use the App Router pattern with route handlers (`route.ts` files) for APIs and React Server Components for UI.

Webpack mode is used explicitly (`next dev --webpack`, `next build --webpack`) rather than Turbopack — likely for compatibility with specific Node.js addons (e.g. `pg` native bindings) or build plugins.

### TypeScript Throughout

Both frontend and backend are TypeScript. No JavaScript files in `src/`. `tsconfig.json` enforces strict checks via `npm run typecheck`.

### PostgreSQL Worker Thread

The `pg` client runs in a dedicated worker thread (`postgres-worker.ts`) to avoid blocking the Node.js event loop during heavy queries. This is bundled separately via esbuild.

### Zod for Validation

Runtime validation at API boundaries uses Zod v4. Type inference from schemas feeds TypeScript types.

### shadcn/ui Component Library

UI components under `src/components/ui/` are shadcn/ui components (Radix primitives + Tailwind). These are copied into the repo (not a runtime dependency) and can be customised freely.

### SendGrid for Email

Transactional email via SendGrid. No self-hosted SMTP. The integration is optional for local development.

### Node Built-in Test Runner

Tests use `node --test` (no Jest or Vitest). This avoids a heavy test framework dependency and works natively in Node 22+.

### Playwright for E2E

End-to-end tests use `@playwright/test`. A separate seeded test database is used for E2E runs on port 3001.