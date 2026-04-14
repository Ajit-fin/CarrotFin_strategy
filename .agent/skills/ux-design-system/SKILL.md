# UX Design System — Skill Router

> **Skill type:** Pattern library + methodology  
> **Invoked by:** `/ux-designer`, `/product`, `/mockup`  
> **Last updated:** 2026-04-15

---

## Purpose

This skill is the reference library for CarrotFin's adaptive, generative UI design system. It contains reusable component patterns, composition grammars, and design methodology checklists. Use this skill to avoid reinventing patterns and to maintain consistency across the adaptive UI system.

## When to Use This Skill

| Scenario | Action |
|---|---|
| Designing a new component | Check `patterns/` first — the pattern may already exist or have a close analogue. Adapt before creating new. |
| Assembling a screen composition | Check `patterns/` for eligible components and `methodology/` for composition checklists. |
| Creating a mockup | Check `examples/` for reference compositions. Use as inspiration, not templates. |
| Defining adaptive behavior | Check `methodology/adaptive-behavior-checklist.md` for required dimensions. |
| Freeform creative exploration | Skip this skill. It's for pattern reuse, not creative constraint. Return here to formalize after exploration. |

## Directory Navigation

```
ux-design-system/
├── SKILL.md              ← You are here — routing guide
├── patterns/             ← Component pattern definitions
│   ├── _index.md         ← Pattern inventory
│   ├── genui/            ← Generative UI patterns (AI-composed components)
│   ├── adaptive-layout/  ← Layout adaptation rules
│   ├── confidence/       ← AI confidence indicators
│   ├── streaming-ui/     ← Streaming/progressive rendering patterns
│   └── disposable/       ← Disposable interface patterns (temporary, contextual)
├── examples/             ← Annotated composition examples
│   └── _index.md         ← Example inventory
├── methodology/          ← Design process checklists
│   └── _index.md         ← Methodology inventory
└── resources/            ← Templates and reusable assets
    └── _index.md         ← Resource inventory
```

## Pattern Categories

| Category | What It Contains | When to Look Here |
|---|---|---|
| **GenUI** | Patterns for AI-composed interfaces: dynamic card stacks, contextual action surfaces, intent-driven layouts | Designing any generative screen from screen-taxonomy.md |
| **Adaptive Layout** | Rules for layout adaptation: density scaling by literacy level, component reordering by priority, responsive composition grammars | Defining how a surface changes per user context |
| **Confidence Indicators** | Patterns for showing AI certainty: reasoning traces, source attribution, uncertainty callouts | Designing any recommendation or advice surface |
| **Streaming UI** | Patterns for progressive rendering: skeleton compositions, content streaming, live calculation displays | Designing surfaces where AI computation takes noticeable time |
| **Disposable Interfaces** | Patterns for temporary, contextual UI elements: one-time alerts, contextual tooltips, time-bound nudges | Designing ambient layer elements from interaction-model.md |

## Methodology Checklists

| Checklist | Purpose |
|---|---|
| **Adaptive Behavior Matrix** | Ensure every component defines behavior across all 6 user state dimensions (literacy, risk, stage, mode, emotion, trust) |
| **Composition Audit** | Verify a generative screen composition follows the rules from screen-taxonomy.md (max components, priority ordering, anchor consistency) |
| **Conversational Integration** | Ensure visual components have defined conversational entry/exit points per interaction-model.md |

## Rules

- **Patterns are living documents** — update when usage reveals gaps or improvements. Git preserves history.
- **No orphan patterns** — every pattern must be referenced by at least one journey or composition example. Unused patterns get archived.
- **Adaptive behavior is mandatory** — no pattern may be added without an adaptive behavior spec. Static-only patterns belong in a different system.
- **Examples are illustrative** — annotated examples show possible compositions, not canonical designs. Always label with the user context that produced them.
