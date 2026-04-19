---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 6
tags: [api, agent, automation, mcp]
status: active
---

# Agent API

The Agent API (`/api/agent/`) allows automated systems and AI agents to interact with bugs. Authentication uses API keys or JWT tokens rather than session cookies (see `requireAgentApiUser()`).

## Endpoints

### GET /api/agent/bugs
List bugs that can be claimed by an agent (claimable bugs only).

### GET /api/agent/bugs/[bugId]
Fetch details of a specific bug.

### POST /api/agent/bugs/[bugId]/claim
Claim a bug for an agent to work on.

**Body**: `{ claimedBy: string }` — identifier for the claiming agent.

### PATCH /api/agent/bugs/[bugId]/status
Update the agent-work state on a bug.

**Body**: `{ claimedBy: string, state: string, statusNote?: string }`

## CLI Interface

`scripts/bug-agent-cli.ts` wraps the agent API for command-line use:
```bash
npm run bugs:cli
```

## MCP Server

`src/lib/bugs/mcp-server.ts` exposes bug operations as MCP tools via the `@modelcontextprotocol/sdk`. Start with:
```bash
npm run mcp:server
```

This enables AI assistants (e.g., Claude) to interact with bugs using structured tool calls.

## Related Files

| File | Purpose |
|---|---|
| `src/lib/bugs/agent-api.ts` | Auth, response helpers for agent routes |
| `src/lib/bugs/agent-cli.ts` | CLI runner logic |
| `src/lib/bugs/agent-integration.ts` | Integration helpers |
| `src/lib/bugs/mcp-server.ts` | MCP server definition |