---
type: decision
owner: engineering
last_updated: 2026-04-20
source: raw/agent_bugtracker.md
tags: [agent, architecture, multi-agent, ai, design]
status: proposed
---

# Decision: Multi-Agent Architecture for Bug Tracker

## Context

A single generic agent doing everything (intake, triage, investigation, coordination, QA) is not scalable. As the system grows, a monolithic agent becomes slow, harder to tune, and harder to trust.

## Decision

Adopt a **multi-agent model** with specialised agents, each with a narrow scope and high confidence in its domain.

## Agent Roles

| Agent | Responsibility |
|-------|---------------|
| **Intake Agent** | Cleans and structures incoming bug reports |
| **Triage Agent** | Prioritises, classifies, and routes bugs |
| **Investigation Agent** | Correlates logs, releases, stack traces, and code changes |
| **QA Agent** | Suggests test scenarios and verification steps |
| **Release Agent** | Assesses whether bug fixes are safe for release |
| **Support Agent** | Generates customer-facing summaries and workaround notes |
| **Insights Agent** | Identifies recurring patterns and systemic quality issues |

## Confidence-Based Automation

Rather than pretending the agent is always right, every output includes a confidence score:
- `likely duplicate: 91%`
- `likely module: billing service, 84%`
- `likely caused by latest release: 73%`
- `likely severity: high, 68%`

This builds user trust and avoids over-automation.

## Autonomy Levels

| Level | Description |
|-------|-------------|
| **Conservative** | Agent suggests, humans decide |
| **Assisted** | Agent auto-fills fields, humans approve |
| **Autonomous** | Low-risk, high-confidence decisions executed automatically |

High-risk actions (rollback, pause rollout, notify on-call) always require human approval regardless of mode.

## Innovation Opportunities

### Bug Impact Graph
Visualise relationships between bugs, modules, customers, releases, and support complaints — showing the system, not just individual tickets.

### Self-Healing Workflow Triggers
For well-understood bug patterns, the agent can:
- Restart a stuck process
- Rollback a bad flag
- Clear a failed queue
- Trigger smoke tests automatically

### Voice and Screenshot Intake
Users report bugs by talking or uploading a screenshot. The agent extracts page, issue description, feature, error text, and reproduction flow.

### Bug Prevention Mode
Proactively predict risk by spotting:
- Modules with repeated regressions
- Teams with rising reopen rates
- Features with poor test coverage
- Release patterns that correlate with incidents

## Positioning

> *"We are not building another bug tracker. We are building an agentic quality operations platform that helps teams detect, understand, prioritise, and resolve bugs faster."*

## Related

- [Agent Capabilities](../concepts/bug_tracker-agent-capabilities.md)
- [Agent MVP Roadmap](../runbooks/bug_tracker-agent-mvp-roadmap.md)
