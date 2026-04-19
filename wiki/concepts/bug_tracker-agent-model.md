---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 3
tags: [agent, automation, ai, mcp]
status: active
---

# Agent Integration Model

## What "Agent" Means Here

An agent is an automated process (AI or script) that interacts with bugs programmatically. Agents authenticate via API key rather than user session and operate through the `/api/agent/*` REST API or the MCP tool server.

## Claim / Work Model

Agents use a claim-based workflow:

1. `GET /api/agent/bugs` — fetch claimable bugs
2. `POST /api/agent/bugs/:id/claim` — claim with `claimedBy` identifier (agent name/ID)
3. `PATCH /api/agent/bugs/:id/status` — update work state and optional `statusNote` as the agent progresses

This prevents two agents from working on the same bug simultaneously.

## MCP Server

`mcp-server.ts` exposes bug operations as structured tools consumable by LLMs via the Model Context Protocol (`@modelcontextprotocol/sdk`). Invoked via `npm run mcp:server`. This allows an LLM to list, read, and update bugs as tool calls within an agent loop.

## Ralph Agent

The `scripts/ralph/` directory contains a specialised internal agent with:
- `project-context.md` — project knowledge injected into the agent's context
- `prompt.md` — system instructions for Ralph's behaviour
- `ralph.sh` — shell runner that launches the agent process

Ralph appears to be a QA/intake agent operating on the bug tracker's own bugs.

## Recommended Multi-Agent Design (from product notes)

The raw sources describe a preferred architecture of specialised agents rather than one monolithic agent:

| Agent | Responsibility |
|---|---|
| Intake | Cleans and structures incoming reports |
| Triage | Prioritises and routes bugs |
| Investigation | Correlates logs, releases, stack traces |
| QA | Suggests verification test scenarios |
| Release | Assesses whether bug fixes are safe to ship |
| Support | Produces customer-facing summaries |
| Insights | Identifies recurring patterns |