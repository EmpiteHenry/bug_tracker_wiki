---
type: open-question
owner: engineering
last_updated: 2026-04-20
source_count: 1
tags: [agent, coordination, multi-agent, concurrency]
status: active
---

# Open Question: Multi-Agent Coordination

## Question

How should multiple agents coordinate to avoid duplicate work, handle failures mid-claim, and signal progress to each other?

## Current State

The claim mechanism (`POST /api/agent/bugs/[bugId]/claim` with `claimedBy`) provides basic mutual exclusion — an agent names itself as the claimer before working on a bug.

However:
- There is no timeout/lease on claims — a crashed agent keeps a bug claimed indefinitely
- There is no heartbeat mechanism to signal liveness
- There is no queue abstraction — agents poll `GET /api/agent/bugs` and race to claim
- No coordination protocol is documented

## From Source Notes

> "Agent should be automated and if multiple agents working on this it should coordinate with others how to perform its task."

This indicates multi-agent operation is intended but the coordination design is unresolved.

## Options to Consider

1. **Lease-based claims**: add `claimedUntil` timestamp; expired claims become available again
2. **Agent heartbeat**: agents ping a keepalive endpoint; stale agents are evicted
3. **Distributed queue**: move claimable bugs into a proper work queue (e.g. Redis, DB queue table)
4. **Coordinator agent**: one agent acts as dispatcher, others are workers

## Priority

Medium — single-agent operation works today. Multi-agent concurrency becomes a problem only when multiple agents run simultaneously against the same bug pool.
