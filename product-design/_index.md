# Product Design — Reading Guide

> **Domain:** Design  
> **Last updated:** 2025-04-15  
> **Staleness threshold:** 30 days

---

## What This Layer Is

The product design layer defines **what CarrotFin's experience is and why** — the UX philosophy, interaction paradigm, screen architecture, and component system that make CarrotFin a fundamentally different personal finance product.

These are not wireframes or pixel specs. They are the design decisions, constraint systems, and architectural principles that govern how the AI assembles the user experience. Think of this layer as the "constitution" that every screen, flow, and interaction must comply with.

---

## File Inventory

| File | What It Contains | Read When |
|---|---|---|
| [ux-philosophy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/ux-philosophy.md) | The hyperpersonalization thesis — why CarrotFin's UX is architecturally different, not incrementally better | Starting any UX/product design work |
| [interaction-model.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/interaction-model.md) | How conversational AI and visual surfaces integrate — surface types, flow modes, modality handoffs | Designing any screen or interaction flow |
| [screen-taxonomy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/screen-taxonomy.md) | Generative, Static, and Hybrid screen types — when each applies, constraints, examples | Deciding how to implement a specific screen |
| [tension-log.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/tension-log.md) | Unresolved design tensions — forces in opposition, why they can't be resolved yet | Before making design tradeoffs |

## Subdirectories

| Directory | What It Contains | Status |
|---|---|---|
| [journey-catalog/](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/) | End-to-end user journey definitions with adaptive behaviors | Scaffold only — journeys to be designed |
| [component-patterns/](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/component-patterns/) | Adaptive component specifications with context-dependent rendering | Scaffold only — patterns to be defined |
| [design-decisions/](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/) | Immutable design decision records | Scaffold only — decisions logged as made |

---

## Reading Order for New Contributors

1. **Start with** [design-principles.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/design-principles.md) in `knowledge-base/` — the five governing axioms
2. **Then** `ux-philosophy.md` — the thesis that drives everything
3. **Then** `interaction-model.md` — how the thesis manifests in the interaction paradigm
4. **Then** `screen-taxonomy.md` — the practical framework for implementing screens
5. **Check** `tension-log.md` before making any design tradeoff — the hard problems we haven't solved yet

---

## Relationship to Other Layers

- **Knowledge Base** (`knowledge-base/`) provides the user insights, market context, and assumptions that inform design decisions
- **Workflows** (`.agent/workflows/`) invoke these files — `/ux-designer` and `/product` both read this layer
- **Skills** (`.agent/skills/ux-design-system/`) will contain reusable component templates and methodology checklists (Phase 4)

---

## Design Artifact Conventions

- **No static mockups as source of truth.** Mockups in `mockups/` are illustrative compositions, not canonical layouts.
- **Every component spec includes adaptive behavior.** A component without a "how does this change per user context" section is incomplete.
- **Design decisions are immutable.** Once logged in `design-decisions/`, they don't change. New decisions can supersede old ones, but the record persists.
- **Use inline date annotations** `[as of YYYY-MM-DD]` for any claim about user behavior, market data, or competitive positioning.

---

*This file is the entry point for the product design layer. Update it when new files are added.*
