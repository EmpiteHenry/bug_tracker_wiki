---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [agent, api, mcp, cli]
status: active
---

# ADR: Agent API Design (Three-Interface Approach)

## Decision

Expose agent functionality through **three parallel interfaces**: a REST API (`/api/agent/`), a CLI (`npm run bugs:cli`), and an MCP server (`npm run mcp:server`).

## Why

- **REST API** allows any HTTP client (including LLM-based agents) to interact without a local binary.
- **CLI** enables scripted local workflows and testing agent logic without an HTTP server.
- **MCP Server** integrates with LLM tooling that speaks the Model Context Protocol natively, enabling tool-use by Claude and other models.

The claim-before-work pattern (`POST /api/agent/bugs/[bugId]/claim`) prevents multiple agents from picking up the same bug without a distributed lock.

## Consequences

- Three entry points to keep in sync when bug operations change.
- The `agent-api.ts` helper module centralizes auth, error mapping, and response shaping so the three interfaces share common behavior.
- Multi-agent coordination remains partially unresolved — see [open question](../open-questions/bug_tracker-agent-coordination.md).
