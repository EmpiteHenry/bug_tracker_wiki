---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 2
tags: [agent, automation, multi-agent, mcp, ai]
status: active
---

# Agent Architecture Concept

The product roadmap (from `shared/agent_bugtracker.md`) describes an agent layer that goes well beyond a simple API wrapper.

## Specialised Agent Roles

Rather than one generic agent, the design calls for a set of focused agents:

| Agent | Responsibility |
|---|---|
| **Intake** | Structures messy bug reports, rewrites vague titles, extracts reproduction steps, classifies severity |
| **Triage** | Ranks business impact, routes to team, flags blockers, recommends escalation |
| **Investigation** | Correlates logs, recent commits, releases, feature flags, and past similar bugs |
| **QA** | Suggests test scenarios, verification steps, regression scope |
| **Release** | Checks whether a fix is safe to ship |
| **Support** | Generates customer-safe summaries and workaround notes |
| **Insights** | Finds recurring patterns, modules with regressions, systemic quality issues |

## Agent Coordination

Agents should coordinate with each other on shared bugs. The `agent-integration.ts` and claim model (via `/api/agent/bugs/[id]/claim`) provide the primitives for preventing duplicate work.

## MCP Integration

`mcp-server.ts` exposes bug tracker operations as MCP tools. This allows Claude and other LLM agents to interact with the bug tracker natively within their tool-calling loop.

## Confidence-Based Automation

Rather than silent automation, the design recommends surfacing confidence scores:
- `likely duplicate: 91%`
- `likely module: billing service, 84%`
- `likely caused by latest release, 73%`

This builds user trust and allows human override before high-confidence actions are taken autonomously.

## Triage Modes

- **Conservative** — agent suggests only, human approves
- **Assisted** — agent auto-fills fields, human approves
- **Autonomous** — low-risk bugs triaged without human involvement

## Evidence Correlation

Investigation agents should pull in: logs, API failures, session recordings, deployment history, recent releases, feature flags, analytics anomalies, test failures. Current infrastructure (observability logs, operational alerts, bug history) provides the data substrate for this.