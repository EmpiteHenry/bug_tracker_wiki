---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [agent, mcp, automation, api]
status: active
---

# Agent & MCP Integration

The bug tracker exposes two surfaces for automated agent operations.

## Agent REST API (`/api/agent/*`)

A lightweight REST API authenticated via agent API key (not session cookies). Used by automated agents to work bugs in the tracker.

### Workflow

1. **Discover** claimable bugs: `GET /api/agent/bugs`
2. **Claim** a bug: `POST /api/agent/bugs/[bugId]/claim` with `{ claimedBy: "agent-name" }`
3. **Work** the bug: agent performs investigation
4. **Update status**: `PATCH /api/agent/bugs/[bugId]/status` with `{ claimedBy, state, statusNote }`

### Agent States

The `agent_state` field on bugs tracks the agent workflow state (e.g. `claimed`, `investigating`, `done`).

### Auth

Agent requests authenticate using the API key configured in organization settings (`/settings/integrations`). The key is passed in the `Authorization: Bearer` header.

## MCP Server

`src/lib/bugs/mcp-server.ts` implements a [Model Context Protocol](https://modelcontextprotocol.io) server exposing bug tracker operations as MCP tools for LLM-based agents (e.g. Claude).

Start with:
```bash
npm run mcp:server
# or directly: tsx scripts/mcp-server.ts
```

The MCP server lets an LLM agent:
- List and search bugs
- Read bug details
- Update bug status and fields
- Add comments

## CLI Agent Tool

`npm run bugs:cli` (`scripts/bug-agent-cli.ts`) is a command-line interface that drives the agent REST API. Used for testing and scripted workflows.

Backed by `src/lib/bugs/agent-cli.ts` and `src/lib/bugs/agent-api.ts`.

## Multi-Agent Coordination

Per product notes, agents should coordinate when multiple agents work on the same bug set. The `claimed_by` field on bugs provides basic mutual exclusion — an agent should only work bugs it has claimed.

## Testing Sessions

`TestingSessionService` tracks QA agent sessions. During a session, evidence (screenshots, recordings) can be attached to bugs via `POST /api/extension/bugs/[bugId]/evidence`. Session logs are stored in `testing_session_logs`.