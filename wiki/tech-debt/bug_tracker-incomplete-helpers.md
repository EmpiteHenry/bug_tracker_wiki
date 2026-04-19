---
type: tech-debt
owner: engineering
last_updated: 2026-04-20
source_count: 4
tags: [helpers, incomplete, filter-logic]
status: active
---

# Incomplete Helper Implementations

## Summary

Several helper/utility files have structural scaffolding in place but contain empty or stub implementations inside switch/case blocks and return statements. These were identified in the source snapshot as of 2026-04-20.

## Affected Files

### `bug-data-management-panel.helpers.ts`

- `summarizeBugDataJobs`: `reduce` accumulator body is empty; `return {}` at the end returns an empty object instead of counts-by-status map.
- `formatBugDataJobProgress` (import branch): empty return.
- `formatBugDataJobIssueSummary` (error/warning branches): empty returns — always falls through to `"No issues"` even when issues exist.
- `formatBugDataJobSummary` (import branch): empty return.

### `bug-history.helpers.ts`

- `formatBugHistoryEventLabel`: entire `switch` body empty — returns `undefined` for all event types.
- `getBugHistoryEntrySource` (import branch, bug branch): empty returns.
- `formatBugHistoryChange` (set/cleared/unchanged branches): empty returns.

### `all-bugs-filters.ts`

- Multiple normalizer functions (`normalizeOptionalPositiveInteger`, `normalizeOptionalDate`, `normalizeSortField`, `normalizeSortDirection`) have empty early-return and error branches.
- `parseAllBugFilters`: entire function body is empty (returns `{}` cast to `AllBugsFilterState`).

### `alert-list-filters.ts` / `notification-center-filters.ts`

- Filter builders have empty `if` branches — search params may not be appended correctly.

## Risk

- Bug list page may not filter or sort correctly.
- Bug history timeline may show blank labels and no source attribution.
- Export/import job summaries in the admin panel may display "No issues" when errors exist.

## Recommended Action

Audit each empty branch against the corresponding type definitions and fill in the correct logic. The types are complete and accurate — only the runtime bodies are missing.