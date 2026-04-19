---
type: query
last_updated: 2026-04-20
status: active
tags: [query]
---

# Query: what is the agent 

**Date**: 2026-04-20

## Question

what is the agent 

## Answer

Based on the wiki, the **Agent** in Bug Tracker refers to automated systems or AI agents that interact with bugs programmatically.

## Agent Overview

According to `wiki/systems/bug_tracker/api/agent.md` and `wiki/concepts/bug_tracker-domain-model.md`:

### What it is
An agent is an automated system (or AI assistant) that can claim and update bugs via a dedicated API, separate from the normal user-facing interface.

### Authentication
Agents authenticate using **API keys or JWT tokens** (not session cookies) via `requireAgentApiUser()`. This is part of the dual-auth strategy described in `wiki/decisions/bug_tracker-dual-auth-strategy.md`.

### What agents can do
- **List claimable bugs** — `GET /api/agent/bugs`
- **Fetch bug details** — `GET /api/agent/bugs/[bugId]`
- **Claim a bug** — `POST /api/agent/bugs/[bugId]/claim` with `{ claimedBy: string }`
- **Update agent work state** — `PATCH /api/agent/bugs/[bugId]/status` with `{ claimedBy, state, statusNote? }`

### Agent state on bugs
Each bug has an **agent state** field — recording `claimedBy`, `state`, and `statusNote` — that agents update independently of the main bug status field.

### Access methods
There are three ways to interact as an agent:
1. **REST API** — `/api/agent/` endpoints directly
2. **CLI** — `npm run bugs:cli` (wraps `scripts/bug-agent-cli.ts`)
3. **MCP Server** — `npm run mcp:server` exposes bug operations as structured tools for AI assistants like Claude via `@modelcontextprotocol/sdk`

The MCP integration is an intentional design choice to support **AI-assisted bug triage workflows**, as noted in `wiki/decisions/bug_tracker-tech-stack.md`.
