# Knowledge Base — Reading Guide

> **Purpose:** Index of all knowledge-base files. Start here when entering any workflow.  
> **Last updated:** 2026-07-30  
> **Staleness threshold:** 30 days

---

## File Inventory

| File | Domain | Description | Last Updated |
|---|---|---|---|
| [company-context.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/company-context.md) | Company | CarrotFin product definition, team, stage, constraints, immediate goals | 2026-04-14 |
| [market-intel.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/market-intel.md) | Market | Indian personal finance app landscape, competitors, market sizing | 2026-04-14 |
| [user-insights.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/user-insights.md) | Users | Target segments, behavioral signals from InsurEasy, unmet needs | 2026-04-14 |
| [assumptions-tracker.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/assumptions-tracker.md) | Risk | Tracked assumptions with status, evidence, and validation plan. Includes Group Profile assumptions (GP-01). | 2026-07-29 |
| [prior-learnings.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/prior-learnings.md) | Learnings | Distilled insights from InsurEasy era — what carries forward to CarrotFin | 2026-04-14 |
| [design-principles.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/design-principles.md) | Design | **Two** governing design axioms — adaptive composition, hyperpersonalization. (Axiom 2 ‘Conversational + Visual Integration’ deleted 2026-07-30; surface architecture is a design decision, not an axiom.) | 2026-07-30 |

---

## Recent Workspace Evolution (July 2026)

The product has pivoted from an individual-centric model to a **multi-entity household/family model** ([Data-Model.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Data-Model.md)). Key structural changes:

| Change | Design Decision | Impact |
|---|---|---|
| Group Profile Model | [Data-Model.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Data-Model.md) | Schema, extraction, and all buildspecs now operate on `GroupProfile`. Assumption GP-01. |
| Split Planner Architecture | [Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md) | Monolithic planner → Overview + Detail planners |
| Extraction v2 | [Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md) | Rebuilt for group-aware entity resolution |
| Digital Private Bank identity | [Visual-Identity.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Visual-Identity.md) | Dark-mode-first, Teal Cyan (#3CDDC7) |
| Voice → Persona | [Interaction-Modality.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Interaction-Modality.md) | Text-only MVP adaptation |
| Axiom 2 deleted; Axioms reduced from 3 to 2 | [design-principles.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/design-principles.md) | Surface architecture is a design decision, not a foundational axiom. Axiom 1 scoped to be behavioral-mode-aware. |
| C5 assumption deleted | [assumptions-tracker.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/assumptions-tracker.md) | Surface arrangement is validated by user testing per design decision, not tracked as an assumption. |

For the full agent architecture, see [buildspecs/_index.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/_index.md).

---

## How to Use

1. **Start of any session:** Read this file + `company-context.md` minimum
2. **Before making claims:** Check `assumptions-tracker.md` for validated vs. speculative
3. **Before recommending:** Check `prior-learnings.md` for lessons that may apply
4. **Market questions:** Read `market-intel.md` — note inline `[as of YYYY-MM-DD]` annotations
5. **User/segment questions:** Read `user-insights.md`

## Freshness Protocol

- Files with `Last Updated` older than 30 days should be flagged in `/feedback` sessions
- Quantitative claims must carry `[as of YYYY-MM-DD]` inline annotations
- When updating a file, update both the inline date and this index

---

*Updated by `/feedback` workflow after each session that modifies KB files.*
