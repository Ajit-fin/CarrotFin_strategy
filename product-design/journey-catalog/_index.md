# Journey Catalog — Index

> **Domain:** Design  
> **Last updated:** 2026-04-16  
> **Staleness threshold:** 30 days

---

## What This Directory Contains

End-to-end user journey definitions for CarrotFin. Each journey maps how a user moves through a specific financial task — from initial trigger to completion — with adaptive behaviors defined at each step.

Journeys are NOT wireframes. They define:
- **User intent** — what the user wants to accomplish
- **AI behavior** — what the AI does at each step (questions asked, decisions made, components rendered)
- **Adaptive variations** — how the journey changes for different user contexts
- **Modality flow** — when conversation leads vs. visualization leads (per `interaction-model.md`)
- **Trust gates** — what data the AI needs and when it earns the right to ask (per [behavioral-framework.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) Part 6: Trust Architecture)

---

## Journey Inventory

| ID | Journey | Priority | Status |
|---|---|---|---|
| [J01](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md) | Emergency Fund Setup | P0 — First use case | Phase 2 in progress — Steps 2A-2B complete |
| ↳ [J01 Specs](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-interaction-specs.md) | Emergency Fund — Phase Interaction Specs | (Part of J01) | Phases 1–2 fully specced; Phases 3–5 skeleton |

---

## Journey Template

```markdown
# J[N]: [Journey Name]

> **Trigger:** [What initiates this journey]
> **User segments:** [Which segments from user-insights.md]
> **Assumptions depended on:** [IDs from assumptions-tracker.md]

## Happy Path
[Step-by-step: user action → AI response → surface type]

## Adaptive Variations
[How this journey changes for different user contexts]

## Data Requirements
[What user data the AI needs at each step, and whether it's available or must be requested]

## Design Decisions
[Tradeoffs made, with links to design-decisions/ records]
```

---

*Add journeys as they're designed via `/ux-designer` sessions. Update this index when new journeys are added.*
