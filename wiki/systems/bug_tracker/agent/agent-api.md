---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, api, automation, mcp, cli]
status: active
---

# Agent API & Automation

Bug Tracker exposes dedicated surfaces for AI agents and automated tooling to interact with bugs.

## Agent REST API (`/api/agent/`)

API-key authenticated. All responses use a structured envelope:
```json
{ "success": true, "data": { ... } }
{ "success": false, "error": "..." }
```

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/agent/bugs` | List bugs available to claim |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug; body: `{ claimedBy: string }` |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update work state; body: `{ claimedBy, state, statusNote? }` |

Helper functions in `src/lib/bugs/agent-api.ts`:
- `requireAgentApiUser(request)` — validates API key
- `parseAgentBugId(value)` — returns `null` for invalid IDs (not thrown, checked by route)
- `handleAgentApiError(error)` — maps known errors to agent response format

## Agent CLI (`npm run bugs:cli`)

`scripts/bug-agent-cli.ts` → `src/lib/bugs/agent-cli.ts`

A CLI script for running agent operations from the terminal. Uses the same `agent-api` service layer. Tested in `agent-cli.test.ts`.

## MCP Server (`npm run mcp:server`)

`scripts/mcp-server.ts` → `src/lib/bugs/mcp-server.ts`

Exposes bug operations as [Model Context Protocol](https://modelcontextprotocol.io) tools, allowing Claude and other MCP-compatible AI assistants to directly read and update bugs.

Uses `@modelcontextprotocol/sdk` (v1.27.1).

## Agent Integration (`src/lib/bugs/agent-integration.ts`)

Shared logic used by both the REST API and MCP server for agent-originated bug interactions.

## API Key Management

API keys are managed via:
- `src/lib/settings/api-key-service.ts` — create, list, revoke keys
- `GET/PATCH /api/settings/api-access` — user-facing settings UI
- Admin panel at `/settings/integrations`