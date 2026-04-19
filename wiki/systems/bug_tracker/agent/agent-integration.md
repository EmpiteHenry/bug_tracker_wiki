---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 6
tags: [agent, mcp, automation, api-key, cli]
status: active
---

# Agent Integration

The platform exposes a dedicated API surface and tooling for automated (AI/agent) bug operations.

## Agent API (`/api/agent/*`)

Authentication uses API key auth via `requireAgentApiUser()` (distinct from session-cookie auth used by the web app).

Helper functions in `src/lib/bugs/agent-api.ts`:
- `requireAgentApiUser()` — validate API key
- `parseAgentBugId()` — parse and validate bug ID
- `createAgentApiSuccessResponse()` / `createAgentApiErrorResponse()` — standard response shapes
- `handleAgentApiError()` — typed error handling

### Endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs (`listClaimableBugs()`) |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug: `{ claimedBy: string }` |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update work status: `{ claimedBy, state, statusNote? }` |

## MCP Server

`src/lib/bugs/mcp-server.ts` implements a **Model Context Protocol** server exposing bug tracker tools to AI agents.

Run via: `npm run mcp:server` → `scripts/mcp-server.ts`

This allows Claude and other MCP-compatible agents to interact with the bug tracker as a tool.

## Agent CLI

`src/lib/bugs/agent-cli.ts` provides a command-line interface for agent operations.

Run via: `npm run bugs:cli` → `scripts/bug-agent-cli.ts`

## Agent Integration Module

`src/lib/bugs/agent-integration.ts` contains shared integration logic used by both the API and CLI.

## Alerts CLI

`src/lib/monitoring/operational-alert-cli.ts` provides CLI access to operational alerts.

Run via: `npm run alerts:cli` → `scripts/alerts-cli.ts`

## Testing Sessions

The extension and agent can create **testing sessions** to group evidence (attachments, logs) captured during a QA run:

| File | Role |
|---|---|
| `testing-session-service.ts` | Session lifecycle |
| `testing-session-store.ts` | Persistence |
| `testing-session-log-service.ts` | Log entries within a session |

## Vision (from Product Docs)

See [Agent Vision](../../../concepts/bug_tracker-agent-vision.md) for the planned roadmap toward multi-agent, autonomous bug triage and investigation.