# Design Decisions — Index

> **Domain:** Design  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** N/A (immutable records)

---

## What This Directory Contains

Immutable records of significant design decisions. Once logged, a design decision is never modified — only superseded by a newer decision that references the original.

Design decisions differ from strategic decisions (`decision-log/`): they concern UX/UI architecture, interaction design, and component system choices — not business strategy, market positioning, or growth tactics.

---

## When to Create a Design Decision Record

- A design tension from `tension-log.md` is resolved
- A significant component pattern or interaction paradigm is chosen over alternatives
- A user test invalidates a design assumption, forcing a change
- Two design principles conflict and a resolution precedent is set

**When NOT to create one:** Minor styling choices, copy tweaks, or implementation details. The bar is "would reversing this decision require rethinking other design choices?"

---

## Decision Inventory

| ID | Decision | Date | Superseded By |
|---|---|---|---|
| — | *No decisions logged yet* | — | — |

---

## Decision Record Template

```markdown
# DD[N]: [Decision Title]

> **Date:** YYYY-MM-DD  
> **Tension resolved:** [T-ID from tension-log.md, if applicable]
> **Assumptions depended on:** [IDs from assumptions-tracker.md]

## Context
[What prompted this decision — the specific design question or conflict]

## Options Evaluated
| Option | Pros | Cons | Risk |
|---|---|---|---|

## Decision
[What was chosen and why — be specific, not vague]

## Consequences
[What this decision enables and constrains going forward]

## Reversal Trigger
[What evidence or event would cause us to revisit this decision]
```

---

*Log design decisions as they're made during `/ux-designer` and `/product` sessions. Link back to the tension-log entry if a tension was resolved.*
