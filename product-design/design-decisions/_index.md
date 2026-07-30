# Design Decisions — Index

> **Domain:** Design  
> **Last updated:** 2026-07-29  
> **Staleness threshold:** N/A (immutable records)

---

## What This Directory Contains

Consolidated records of significant **product-level** design decisions. Decisions are grouped thematically — not chronologically — for readability. Each consolidated document contains multiple related decisions with compressed rationale and a "Considered & Rejected" appendix.

**Scope:** Only 1st and 2nd-order decisions that govern the product system regardless of which journey is active. Journey-specific decisions (e.g., EF component visual specs) live in their respective journey artifacts and component patterns.

---

## When to Create a Design Decision Record

- A design tension from `tension-log.md` is resolved
- A significant architectural choice is made over alternatives
- A user test invalidates a design assumption, forcing a change
- Two design principles conflict and a resolution precedent is set

**When NOT to create one:** Journey-specific component decisions (those go in CP specs), minor styling choices, copy tweaks, or 3rd-order implementation details. The bar is "would reversing this decision require rethinking the product architecture?"

---

## Decision Inventory

| Document | Decisions | Focus | Date Range |
|---|---|---|---|
| [V1-Product-Scope.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/V1-Product-Scope.md) | DD01, DD02, DD03, DD05 | V1 boundaries — first journey as onboarding, advisory-only model, scope ceiling, AI-routed entry | 2026-04-16 |
| [Interaction-Modality.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Interaction-Modality.md) | DD04, DD16 | Text-only MVP (active) + voice+screen hybrid architecture (deferred) | 2026-04-16 – 2026-07-11 |
| [Data-Model.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Data-Model.md) | DD10, DD11, DD12 | Data nomenclature, 4-entity schema, group/family profile model | 2026-07-06 – 2026-07-21 |
| [Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md) | DD13, DD14 | Split planner, flash extraction v2 (group-aware) | 2026-07-21 – 2026-07-28 |
| [Visual-Identity.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Visual-Identity.md) | DD15 | Digital Private Bank aesthetic, Teal Cyan seed | 2026-06-19 |

### Archived Decisions

The following decisions were removed from standalone DD records during the July 2026 consolidation. Their rationale has been absorbed into the target files listed:

| Former ID | Decision | Absorbed Into |
|---|---|---|
| DD-001 | Adaptive Composition axiom consolidation | [design-principles.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/design-principles.md) (outcome is the current 3-axiom structure) |
| DD-002 | Progressive Trust relocation to behavioral framework | [behavioral-framework.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) Part 6 |
| DD06 | Assessment stream-primary constraint | [interaction-model.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/interaction-model.md) |
| DD07 | Attribution strip visual design | [CP03](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/component-patterns/CP03-target-card.md) (design rationale note) |
| DD08 | Allocation gradient strip visual design | [CP04](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/component-patterns/CP04-allocation-card.md) (design rationale note) |
| DD09 | Contribution plan Action Card design | [CP05](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/component-patterns/CP05-contribution-plan.md) (design rationale note) |

---

*Log design decisions during `/ux-designer` and `/product` sessions. Link back to the tension-log entry if a tension was resolved.*
