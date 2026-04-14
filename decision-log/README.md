# Decision Log — Directory Guide

> **Purpose:** Immutable records of significant strategic and product decisions.

---

## What Goes Here

Every significant decision — product direction, feature prioritization, market positioning, architectural choices — gets an immutable entry. Once written, entries are never edited (git preserves history).

## What Does NOT Go Here

- Working documents and drafts → `strategy/`
- Research and analysis → `research/`
- Product design specifications → `product-design/design-decisions/`

## Naming Convention

`YYYY-MM-DD-[decision-slug].md` — e.g., `2025-05-01-target-segment-s1.md`

## Record Template

```markdown
# YYYY-MM-DD — [Decision Title]

## Context
[What prompted this decision — the situation, trigger, or question]

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| Option A | ... | ... | ... |
| Option B | ... | ... | ... |

## Decision
[What was chosen and WHY — be specific about the reasoning]

## Assumptions Made
[Cross-reference assumptions-tracker.md — which assumptions does this decision depend on?]

## Reversal Trigger
[What would cause us to revisit this decision? Be specific: a metric, a user signal, a market event.]
```

## Rules

1. **Immutable** — never edit a decision record after creation. If a decision is reversed, create a NEW entry referencing the original.
2. **Cross-reference** — always link to relevant assumptions (from `assumptions-tracker.md`).
3. **Reversal trigger** — every decision must include a specific, measurable condition that would cause reconsideration.
4. **Velocity alert** — healthy rate is ~2-5 decisions/month. 0 = stagnation. 10+ = thrashing.

---

*Decision records are created via the `/founder` workflow or during any strategic session where a significant choice is made.*
