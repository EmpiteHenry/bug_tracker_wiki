---
type: decision
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [agent, api, mcp, automation]
status: active
---

# ADR-003: Separate Agent API Surface

## Decision

Provide a dedicated `/api/agent/` route group for automated agents, separate from the browser-session API.

## Context

Automated agents (CI bots, LLM agents, external integrations) need to operate on bugs programmatically. Mixing this with the browser session API would complicate auth and response formats.

## Design

- Agent auth via `requireAgentApiUser()` — token-based, no cookie dependency
- Responses via `createAgentApiSuccessResponse()` / `createAgentApiErrorResponse()` — consistent machine-readable envelope
- Scoped operations: list claimable bugs, claim a bug, update agent work state
- MCP server (`npm run mcp:server`) exposes operations as tools for LLM agents via `@modelcontextprotocol/sdk`

## Claim Model

Agents explicitly claim a bug (`POST /api/agent/bugs/[id]/claim`) with a `claimedBy` identifier before updating it. This prevents two agents from working the same bug concurrently.

## Future Direction

Product roadmap calls for specialised agents (intake, triage, investigation, QA, release, support). The current agent API is the foundation for this multi-agent coordination layer.