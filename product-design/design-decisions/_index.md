# Design Decisions — Index

> **Domain:** Design  
> **Last updated:** 2026-04-16  
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
| [DD01](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD01-ef-as-onboarding.md) | EF Setup as First-Launch Onboarding | 2026-04-16 | — |
| [DD02](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD02-advisory-only-model.md) | Advisory-Only Execution Model for V1 | 2026-04-16 | — |
| [DD03](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD03-v1-scope-boundary.md) | V1 Scope Boundary — Discovery Through Contribution Plan | 2026-04-16 | — |
| [DD04](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD04-voice-screen-hybrid-modality.md) | Voice + Screen Hybrid Modality | 2026-04-16 | — |
| [DD05](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD05-multi-stage-re-entry.md) | Multi-Stage Re-Entry — AI-Routed Journey Entry | 2026-04-16 | — |
| [DD06](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD06-assessment-stream-primary.md) | Assessment Stream-Primary — No Composed Surface in Assessment | 2026-04-16 | — |
| [DD07](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD07-attribution-strip-design.md) | Attribution Strip — Tiered Linear Arrow Diagram with Literacy Fallback | 2026-04-16 | — |
| [DD08](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD08-allocation-visual-design.md) | Three-Layer Allocation Visual — Horizontal Liquidity Gradient Strip | 2026-04-16 | — |
| [DD09](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD09-contribution-plan-deliverable.md) | Contribution Plan Deliverable — Action Card + Salary-Day Reminder | 2026-04-16 | — |

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
