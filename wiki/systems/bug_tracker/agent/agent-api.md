---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, api, automation, mcp]
status: active
---

# Agent API

The agent API provides a programmatic interface for automated agents to operate on bugs without a browser session.

## Authentication

`requireAgentApiUser(request)` — agents authenticate via a separate mechanism from browser sessions (see `agent-api.ts`). Errors are returned as structured JSON via `createAgentApiErrorResponse()`.

## Endpoints

| Method | Route | Purpose |
|---|---|---|
| GET | `/api/agent/bugs` | List claimable bugs |
| GET | `/api/agent/bugs/[bugId]` | Get bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug (`claimedBy: string`) |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update agent work state |

### Claim Request
```json
{ "claimedBy": "agent-name" }
```

### Status Update Request
```json
{
  "claimedBy": "agent-name",
  "state": "...",
  "statusNote": "optional note"
}
```

## Agent CLI

`npm run bugs:cli` → `scripts/bug-agent-cli.ts` (wraps `src/lib/bugs/agent-cli.ts`)

`agent-integration.ts` — higher-level integration helpers.

## MCP Server

`npm run mcp:server` → `scripts/mcp-server.ts` → `src/lib/bugs/mcp-server.ts`

Exposes bug tracker operations as MCP tools for Claude and other LLM agents via the `@modelcontextprotocol/sdk`.

## Design Intent (from product roadmap)

The agent layer is designed to support multiple specialised agents:
- **Intake agent** — structures incoming reports
- **Triage agent** — prioritises and routes
- **Investigation agent** — correlates logs, releases, code changes
- **QA agent** — suggests verification steps
- **Release agent** — checks fix safety

See [Agent Architecture concept](../../../concepts/bug_tracker-agent-architecture.md) for the full vision.