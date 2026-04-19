---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, automation, mcp, api]
status: active
---

# Agent API

The agent API (`/api/agent/*`) exposes a stable interface for AI agents and automated tooling to interact with bugs without a browser session.

## Authentication

Agent requests use an API key rather than a session cookie. `requireAgentApiUser` in `agent-api.ts` validates the key and resolves the acting user identity.

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/agent/bugs` | List claimable bugs (`listClaimableBugs`) |
| `GET` | `/api/agent/bugs/:bugId` | Get full bug detail |
| `POST` | `/api/agent/bugs/:bugId/claim` | Claim a bug for an agent (`claimedBy` string) |
| `PATCH` | `/api/agent/bugs/:bugId/status` | Update agent work state, optionally set `statusNote` |

## Response Helpers

`agent-api.ts` exports:
- `createAgentApiSuccessResponse(data)` — standardised success envelope
- `createAgentApiErrorResponse(message, status)` — standardised error envelope
- `handleAgentApiError(error)` — maps known error types to HTTP responses
- `parseAgentBugId(value)` — validates bug ID from path params (returns `null` on invalid)

## CLI & Integration

- `src/lib/bugs/agent-cli.ts` — command-line interface over the agent API, invoked via `npm run bugs:cli`
- `src/lib/bugs/agent-integration.ts` — higher-level orchestration helpers for agent workflows
- `src/lib/bugs/mcp-server.ts` — MCP server exposing bug operations as LLM tools, invoked via `npm run mcp:server`

## Scripts

| Script | Entry |
|---|---|
| `npm run bugs:cli` | `scripts/bug-agent-cli.ts` |
| `npm run mcp:server` | `scripts/mcp-server.ts` |
| `npm run alerts:cli` | `scripts/alerts-cli.ts` |

## Ralph Agent

`scripts/ralph/` contains a self-contained sub-agent with its own `prompt.md`, `project-context.md`, and shell runner `ralph.sh`. Ralph appears to be an internal QA/bug-intake agent.