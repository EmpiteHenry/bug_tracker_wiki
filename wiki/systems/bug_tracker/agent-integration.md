---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [agent, mcp, cli, automation, coordination]
status: active
---

# Agent Integration

## Overview

The bug tracker exposes three agent interfaces:
1. **REST Agent API** — HTTP endpoints under `/api/agent/`
2. **Agent CLI** — `npm run bugs:cli` (scripts via `tsx scripts/bug-agent-cli.ts`)
3. **MCP Server** — `npm run mcp:server` (Model Context Protocol for LLM tooling)

## Agent REST API

All agent routes use `requireAgentApiUser` for authentication (API key, not session cookie).

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable (unassigned, open) bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug (set `claimedBy`) |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state + status note |

### Claim Request Body
```json
{ "claimedBy": "agent-name" }
```

### Status Update Body
```json
{
  "claimedBy": "agent-name",
  "state": "in_progress | completed | ...",
  "statusNote": "optional string"
}
```

Helper functions in `src/lib/bugs/agent-api.ts`:
- `parseAgentBugId` — validates and parses bug ID from route params
- `createAgentApiSuccessResponse` / `createAgentApiErrorResponse`
- `handleAgentApiError` — maps service errors to HTTP responses

## Agent CLI

`src/lib/bugs/agent-cli.ts` implements a CLI mode for scripted agent workflows (fetch, claim, update bugs from terminal). Run via `npm run bugs:cli`.

## MCP Server

`src/lib/bugs/mcp-server.ts` exposes bug tracker operations as MCP tools, enabling LLM clients that speak the Model Context Protocol to interact with bugs directly. Run via `npm run mcp:server`.

## Multi-Agent Coordination

From raw source notes (`shared/agent_bugtracker copy.md`):

> Agent should be automated and if multiple agents working on this it should coordinate with others how to perform its task.

The claim mechanism (`POST /api/agent/bugs/[bugId]/claim` with `claimedBy`) is the primary coordination primitive — agents claim bugs before working on them to prevent duplicate work. See [open question on agent coordination](../../open-questions/bug_tracker-agent-coordination.md) for unresolved design gaps.

## Agent Integration Files

```
src/lib/bugs/
├── agent-api.ts           — shared auth + response helpers
├── agent-cli.ts           — CLI interface
├── agent-integration.ts   — shared integration utilities
└── mcp-server.ts          — MCP server entry point
```
