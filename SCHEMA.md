# SCHEMA.md — Wiki Conventions

This wiki follows the software-dev-wiki pattern: a persistent, LLM-maintained knowledge base
where raw sources (ADRs, post-mortems, design docs, runbooks) flow in and structured wiki pages
flow out. The LLM writes and maintains all pages. Humans source the inputs and ask questions.

---

## Directory Structure

```
wiki/
  systems/          # one page per service, component, or subsystem
  systems/<name>/   # subdirectory for linked sub-repos / microservices
  decisions/        # Architecture Decision Records (ADRs)
  incidents/        # post-mortems and incident reports
  concepts/         # design patterns, domain terminology, abstractions
  runbooks/         # step-by-step operational procedures
  tech-debt/        # tracked debt items with context and remediation
  people/           # team ownership, expertise, handoff context (optional)
  open-questions/   # unresolved architectural and design questions
  queries/          # saved Q&A sessions from swiki query
raw/                # immutable source inputs — LLM reads, never writes
```

---

## Page Types

### systems/
One page per major service, component, or subsystem. Cover: what it does, who owns it,
key dependencies, known limitations, deployment model, recent changes.
Each linked sub-repo gets its own subdirectory: `wiki/systems/<service-name>/overview.md`

### decisions/
Architecture Decision Records. Each file captures: what was decided, why, what alternatives
were rejected, and what has changed since. Named: `ADR-001-use-cobra-for-cli.md`

### incidents/
One page per significant incident. What happened, root cause, timeline, fix, follow-up
actions, links to systems and decisions touched. Named: `YYYY-MM-DD-incident-title.md`

### concepts/
Important technical concepts: design patterns in your codebase, domain terminology,
architectural principles you follow, internal abstractions worth documenting.

### runbooks/
Step-by-step operational procedures, kept current from source runbooks and incident learnings.

### tech-debt/
A cross-referenced map of known debt: what it is, why it exists, what it blocks, severity.

### people/
Who owns what, areas of expertise, context for onboarding and handoffs. (optional)

### open-questions/
A living list of unresolved architectural and design questions the team is carrying.

### queries/
Saved Q&A sessions produced by `swiki query`. Good answers get filed back as pages.

---

## Frontmatter Schema

Every wiki page must include YAML frontmatter:

```yaml
---
type: system | decision | incident | concept | runbook | tech-debt | person | open-question | query
owner: team or person responsible
last_updated: YYYY-MM-DD
source_count: number of raw sources this page was built from
tags: [tag1, tag2]
status: active | draft | deprecated | archived
---
```

---

## Link Conventions

- Use relative markdown links: `[Page Title](../systems/overview.md)`
- No orphan pages — every page in `wiki/` must be listed in `index.md`
- Cross-reference related pages (e.g. an incident page links to the system page it touched)

---

## index.md Format

Content-oriented catalog. Format:

```markdown
# Wiki Index

## Systems
- [Overview](wiki/systems/overview.md) — one-line summary

## Concepts
- [Concept Name](wiki/concepts/concept.md) — one-line summary

## Decisions
- [ADR-001 Title](wiki/decisions/ADR-001-title.md) — one-line summary

## Incidents
- [YYYY-MM-DD Incident](wiki/incidents/YYYY-MM-DD-title.md) — one-line summary

## Runbooks
- [Runbook Title](wiki/runbooks/title.md) — one-line summary

## Tech Debt
- [Debt Item](wiki/tech-debt/item.md) — one-line summary

## Open Questions
- [Question Title](wiki/open-questions/title.md) — one-line summary
```

---

## log.md Format

Append-only chronological record. Format:

```markdown
## [YYYY-MM-DD] <command> | <detail>
```

Example: `## [2026-04-19] ingest | tunnel-cli (analyzed: 12 files, 2 raw sources)`

---

## Ingest Checklist

When a new raw source is added and ingest is run:
1. Read the raw source and identify what it is (ADR, post-mortem, design doc, runbook, etc.)
2. Create or update the relevant wiki page(s)
3. Cross-reference: update any system pages the source touches
4. Update index.md to include any new pages
5. Append to log.md

---

## Lint Checklist

On a health-check pass:
- Flag pages not updated in 90 days (staleness)
- Find orphan pages (in wiki/ but not in index.md)
- Find dead links (links pointing to non-existent pages)
- Look for decision pages that contradict each other
- Look for runbooks that diverge from how the system works now
- Look for tech debt items that have been resolved but not archived

---

## Staleness Rule

Any page with `last_updated` more than 90 days in the past should be flagged during lint.
