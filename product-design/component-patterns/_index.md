# Component Patterns — Index

> **Domain:** Design  
> **Last updated:** 2026-04-16  
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

**LLM autonomy boundary:** Component specs define *available states and visual constraints*. They do NOT mandate which state the agent selects or the exact sequence of composition. See CG01 §0.1 for the full prescription boundary.

**Flutter/M3 native-first:** Every component spec must map to the nearest M3 widget primitive before specifying any custom behaviour. See CG01 §0.2 for the priority ladder.

---

## Component Inventory

| ID | Component | Adaptive? | Status |
|---|---|---|---|
| DS01 | Design System (tokens, palette, motion) | N/A — foundation | Complete |
| CG01 | Composition Grammar (selection rules, density, conflicts) | N/A — foundation | Complete |
| CP01 | Confirmation Patterns (§C2 Confirm Chip, §C3 Summary Card, §C4 Edit-in-Place) | Yes | Complete |
| CP02 | BYP Progress Indicator (pinned assessment tracker, 8+1 dimension pills, running estimate) | Yes | Complete |
| CP03 | Target Card + Attribution Strip (phase-gate reveal, DD07 attribution, D3 Prompt, Starter Shield) | Yes | Complete |
| CP04 | Allocation Card + Liquidity Gradient Strip (DD08 3-zone proportional strip, layer detail rows, DICGC footnote, light phase gate) | Yes | Complete |
| CP05 | Contribution Plan Card + Action Card (DD09 milestone timeline, §C4 contribution edit, Action Card with per-layer steps, salary-day reminder, §1A exit copy, D3 Confirmed Card appendix) | Yes | Complete |

---

## Component Template

```markdown
# CP[N]: [Component Name]

> **Type:** [Card | Chart | Form | Alert | Nudge | Action | Indicator]
> **Used on:** [Screen types: Generative / Hybrid]
> **M3 base:** [Primary Flutter/M3 widget this component is built on, e.g., `FilterChip`, `Card`, `ListTile`]

## Purpose
[What this component communicates or enables]

## Flutter Widget Mapping
| Sub-element | M3 Widget | Custom work needed? |
|---|---|---|
| [element] | [widget] | [None / Minimal — reason / Yes — reason] |

## User Context Inputs
| Dimension | How It Affects Rendering |
|---|---|
| Financial literacy | [Complexity calibration] |
| Risk tolerance | [Framing and tone] |
| Emotional state | [Density and urgency] |

## Adaptive Behavior Spec
[Concrete rendering rules per context level. States listed as options for the composition agent — not mandated sequences.]

## Composition Rules
[Where it can appear, adjacency constraints, max instances per surface]

## Interaction Affordances
[Tap → expand, swipe → dismiss, hold → detail, etc.]
```

---

*Add components as they're designed via `/ux-designer` sessions. Update this index when new patterns are added.*
