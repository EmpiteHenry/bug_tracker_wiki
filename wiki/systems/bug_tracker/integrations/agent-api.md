---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, automation, api, mcp, cli]
status: active
---

# Agent Integration

Bug Tracker exposes a dedicated API surface and CLI tooling for automated agents to interact with bugs without a human in the loop.

## Agent REST API

Base path: `/api/agent/bugs`

Authentication: API key via `Authorization: Bearer <key>` header, validated by `requireAgentApiUser`.

| Endpoint | Purpose |
|---|---|
| `GET /api/agent/bugs` | List bugs available for an agent to claim |
| `GET /api/agent/bugs/[bugId]` | Get full bug detail |
| `POST /api/agent/bugs/[bugId]/claim` | Claim a bug (`{ claimedBy: string }`) |
| `PATCH /api/agent/bugs/[bugId]/status` | Update agent work state (`{ claimedBy, state, statusNote? }`) |

Helper functions in `src/lib/bugs/agent-api.ts`:
- `parseAgentBugId` — validates and parses bug ID from URL
- `createAgentApiSuccessResponse` / `createAgentApiErrorResponse` — standardised response shapes
- `handleAgentApiError` — maps service errors to HTTP status codes

## Agent CLI

`src/lib/bugs/agent-cli.ts` provides a command-line interface for agent operations, invoked via:

```bash
npm run bugs:cli
```

Entry point: `scripts/bug-agent-cli.ts`

## Agent Integration Module

`src/lib/bugs/agent-integration.ts` contains shared logic for agent workflows (e.g., state machine transitions for claimed bugs).

## MCP Server

`src/lib/bugs/mcp-server.ts` implements an MCP (Model Context Protocol) server using `@modelcontextprotocol/sdk`. This allows Claude or other LLM agents to interact with Bug Tracker as an MCP tool provider.

Start the MCP server:

```bash
npm run mcp:server
# → tsx scripts/mcp-server.ts
```

## Alerts CLI

Operational alerts can be managed from the CLI:

```bash
npm run alerts:cli
# → tsx scripts/alerts-cli.ts → operational-alert-cli.ts
```