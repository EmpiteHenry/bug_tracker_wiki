---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, api-key, mcp, automation, llm]
status: active
---

# Agent API & MCP Server

The agent subsystem allows automated agents (LLMs, scripts, CI tools) to interact with the bug tracker programmatically. It has two interfaces: a REST API and a Model Context Protocol (MCP) server.

## Agent REST API

Routes under `/api/agent/bugs/`. Authentication uses an API key (configured via Settings → API Access).

### Endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/api/agent/bugs` | List bugs available for agent pickup (`listClaimableBugs`) |
| GET | `/api/agent/bugs/[bugId]` | Get full bug detail |
| POST | `/api/agent/bugs/[bugId]/claim` | Claim a bug; body: `{ claimedBy: string }` |
| PATCH | `/api/agent/bugs/[bugId]/status` | Update work state; body: `{ claimedBy, state, statusNote? }` |

### Agent Auth Helpers (`agent-api.ts`)

- `requireAgentApiUser(request)` — validates API key, returns agent identity
- `parseAgentBugId(value)` — validates and parses bug ID from path param
- `createAgentApiSuccessResponse` / `createAgentApiErrorResponse` — standardised response shapes
- `handleAgentApiError` — maps service errors to HTTP status codes

### Agent Integration (`agent-integration.ts`)

Higher-level orchestration logic combining the REST primitives for multi-step agent workflows.

### Agent CLI (`agent-cli.ts`)

`scripts/bug-agent-cli.ts` (run via `npm run bugs:cli`) exposes agent operations as a command-line tool. Useful for scripted automation or testing agent flows without HTTP overhead.

## MCP Server (`mcp-server.ts`)

`src/lib/bugs/mcp-server.ts` implements a [Model Context Protocol](https://modelcontextprotocol.io) server using `@modelcontextprotocol/sdk`. It exposes bug tracker operations as MCP tools that LLM clients (Claude, Cursor, etc.) can call directly.

Start it with: `npm run mcp:server`

The MCP server allows an LLM agent to list, read, claim, and update bugs using natural language tool calls, making it suitable for the agentic workflows described in the project vision documents.

## API Key Management

API keys are managed through:
- UI: Settings → API Access (`src/components/settings/api-key-settings-panel.tsx`)
- Service: `src/lib/settings/api-key-service.ts`
- Store: `src/lib/settings/api-access-config-service.ts`

Keys are scoped to an organization and can be revoked from the settings page.

## JWT Auth for External Integrations

`src/lib/settings/auth-jwt-service.ts` supports JWT-based auth as an alternative to API keys for integrations that prefer stateless tokens. Configured via `src/components/settings/auth-jwt-settings-panel.tsx`.