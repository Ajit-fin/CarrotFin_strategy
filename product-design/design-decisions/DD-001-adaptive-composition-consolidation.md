# DD-001: Adaptive Composition Consolidation

> **Date:** 2026-04-15  
> **Status:** Accepted  
> **Participants:** Founders  
> **Supersedes:** Original 5-axiom design principles (design-principles.md pre-2026-04-15)

---

## Decision

Consolidated design axioms #1 (Adaptive-First) and #4 (Generative UI Thinking) into a single axiom: **Adaptive Composition**. This reduced the design principles from 5 axioms to 4.

---

## Context

During a review of the Behavioral Intelligence Framework integration, the founders identified that Adaptive-First and Generative UI Thinking were describing the same architectural bet from two angles:

- **Adaptive-First** was the *why* — the interface must be different for each user based on context.
- **Generative UI Thinking** was the *how* — the mechanism is AI-composed component palettes with structural rules, not pre-designed screens.

Every downstream document (`ux-philosophy.md`, `screen-taxonomy.md`, `interaction-model.md`) already treated them as one concept. Only `design-principles.md` separated them.

---

## What Changed

1. **Merged two axioms into one:** "Adaptive Composition" combines the thesis (adaptive > static) with the mechanism (AI-composed interfaces from component palettes and composition rules).

2. **Added "not 100% generative" nuance:** The original Adaptive-First axiom was absolutist — "if a screen looks the same for two users, it's a design failure." The merged axiom acknowledges that some screens and use cases don't benefit from runtime composition. Static and hybrid approaches are legitimate when AI-driven assembly doesn't add meaningful user value. The screen taxonomy already handled this; now the axiom reflects it philosophically.

3. **Scoped to "Composition":** The name "Adaptive Composition" makes explicit that this axiom governs **interface assembly**, not adaptive advice. Advice adaptation is a separate concern handled by `behavioral-framework.md`. This prevents conflation of two distinct systems.

4. **Updated conflict resolution:** 4 rows instead of 5. Progressive Trust remains #1. Adaptive Composition is #2 (combines the product thesis + mechanism). Conversational + Visual Integration is #3. Hyperpersonalization is #4.

5. **Renumbered Progressive Trust:** From #5 → #4. All cross-references updated across 6 downstream files.

---

## Alternatives Considered

- **Keep as separate axioms:** Rejected. They're inseparable — you can't be adaptive-first without generative UI, and generative UI has no purpose without adaptive-first. Keeping them separate created a misleading impression of independent principles.
- **Fold GenUI into Adaptive-First (keep original name):** Rejected. "Adaptive-First" is ambiguous — it could refer to adaptive advice, adaptive content, or adaptive interface assembly. "Adaptive Composition" scopes cleanly to interface assembly.

---

## Impact

| File | Change |
|---|---|
| `design-principles.md` | Primary refactor — merged axioms, renumbered, updated conflict table |
| `ux-philosophy.md` | principle #5 → #4 |
| `behavioral-framework.md` | principle #5 → #4 |
| `tension-log.md` | principle #5 → #4 |
| `journey-catalog/_index.md` | principle #5 → #4 |
| `knowledge-base/_index.md` | "Five" → "Four", updated axiom list |
| `product-design/_index.md` | "five" → "four" |

---

*This decision is immutable. If the axiom needs further refactoring, create a new decision record that supersedes this one.*
