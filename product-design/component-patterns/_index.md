# Component Patterns — Index

> **Domain:** Design  
> **Last updated:** 2025-04-15  
> **Staleness threshold:** 30 days

---

## What This Directory Contains

Adaptive component specifications for CarrotFin's generative UI system. These are not static UI widgets. They are context-aware building blocks that the AI uses to compose interfaces.

Every component in this directory must define:
- **Purpose** — what information or action the component serves
- **User context inputs** — what dimensions of the user state model affect rendering
- **Adaptive behavior spec** — concrete rules for how the component changes per context
- **Composition rules** — where this component can appear, what it can be adjacent to, sizing constraints
- **Interaction affordances** — what the user can do with this component (tap, expand, dismiss, act)

---

## Component Philosophy

Components are **not designed and then personalized.** They are designed with personalization as the primary axis.

**The question is never:** "What does this component look like?"  
**The question is always:** "What does this component look like *for this user in this context*?"

Example: A "spending insight card" is not one design. It's a constraint system:
- **Literacy 1-2:** Headline + emoji + single metric ("You spent ₹14K on dining 🍕")
- **Literacy 3-4:** Headline + sparkline chart + category breakdown + comparison to average
- **Literacy 5:** Headline + detailed chart + trend analysis + budget impact projection

Same component. Three renderings. The AI selects the rendering based on user context.

---

## Component Inventory

| ID | Component | Adaptive? | Status |
|---|---|---|---|
| — | *No components defined yet* | — | — |

---

## Component Template

```markdown
# CP[N]: [Component Name]

> **Type:** [Card | Chart | Form | Alert | Nudge | Action]
> **Used on:** [Screen types: Generative / Hybrid]

## Purpose
[What this component communicates or enables]

## User Context Inputs
| Dimension | How It Affects Rendering |
|---|---|
| Financial literacy | [Complexity calibration] |
| Risk tolerance | [Framing and tone] |
| Emotional state | [Density and urgency] |

## Adaptive Behavior Spec
[Concrete rendering rules per context level]

## Composition Rules
[Where it can appear, adjacency constraints, max instances per surface]

## Interaction Affordances
[Tap → expand, swipe → dismiss, hold → detail, etc.]
```

---

*Add components as they're designed via `/ux-designer` sessions. Update this index when new patterns are added.*
