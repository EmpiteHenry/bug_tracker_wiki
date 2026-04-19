---
type: concept
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [bug-lifecycle, status, workflow, history]
status: active
---

# Bug Lifecycle

## Status Transitions

```
New → Triaged → In Progress → Ready for QA → Closed
                                    ↑              ↓
                             (reopened)  →  In Progress
```

Bugs can be archived at any status. Archiving does not change status — it hides the bug from default list views. Pass `showArchived=true` to include archived bugs in queries.

## Status Descriptions

| Status | Meaning |
|---|---|
| New | Just submitted, not yet reviewed |
| Triaged | Reviewed, severity/priority confirmed, ready to assign |
| In Progress | Actively being worked on |
| Ready for QA | Fix complete, needs verification |
| Closed | Verified fixed or intentionally closed |

## History Tracking

Every state change, field edit, comment, and attachment action is recorded as a history event in `bug_history`. The timeline is available at `GET /api/bugs/[bugId]/history` (requires org admin).

History event types follow the pattern `bug_<action>`, e.g.:
- `bug_created`
- `bug_status_changed`
- `bug_assigned`
- `bug_comment_added`
- `bug_import_created` — bug was created via CSV import

The source of each event is determined by context:
- `bug_import_*` events → source is "Import"
- `bug_*` events → source is "User" (with actor ID)
- Other events → source is "System"

## Notification Triggers

Notifications are sent on:
- Bug created (reporter gets confirmation; project members notified)
- Bug assigned (assignee notified)
- Bug status changed
- Bug closed / reopened
- Comment added

## Agent Workflow

Agents can participate in the lifecycle:
1. `GET /api/agent/bugs` — find claimable bugs
2. `POST /api/agent/bugs/[id]/claim` — claim a bug (sets `claimedBy`)
3. `PATCH /api/agent/bugs/[id]/status` — update work state with a status note
4. Agent closes/updates bug via the regular `PATCH /api/bugs/[id]` endpoint

Agent actions appear in the history timeline with the agent identifier as actor.

## Bulk Operations

Multiple bugs can have their status updated simultaneously via `PATCH /api/bugs/bulk`. Requires an array of bug IDs and a target status. Useful for triage sessions closing multiple resolved issues at once.

## Import Lifecycle

Bugs imported via CSV follow the same status model. The import service:
1. Validates all rows
2. Applies duplicate strategy (`skip_existing` or `update_existing`)
3. Creates history events with `bug_import_*` types
4. Returns a summary: created / updated / skipped / failed counts