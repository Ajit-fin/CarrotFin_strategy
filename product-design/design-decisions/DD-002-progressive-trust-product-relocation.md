# DD-002: Progressive Trust — Product-Level Relocation

> **Date:** 2026-04-15  
> **Status:** Accepted  
> **Participants:** Founders  
> **Supersedes:** Progressive Trust Architecture as design axiom #4 (design-principles.md pre-2026-04-15, post-DD-001)

---

## Decision

Removed Progressive Trust Architecture as a design axiom entirely. Relocated all trust mechanics to `behavioral-framework.md` as Part 6 (Trust Architecture). This reduced the design axioms from 4 to 3.

---

## Context

During the DD-001 refactoring session (Adaptive-First + GenUI consolidation), the founders identified that Progressive Trust had the same "dual nature" as behavioral aspects — it bundled a UX concern with product-level decision logic:

- **The product-level concern (trust ramp):** When to ask for data, what value must precede each ask, how trust level governs the AI's behavioral repertoire. This is AI reasoning logic.
- **The UX concern (interface resilience):** The interface must work with sparse data and degrade gracefully. 

On closer examination, the UX concern is not a *separate* axiom — it's already covered by **Adaptive Composition** (trust level is just another dimension in the user context space the AI composes against) and **Hyperpersonalization** (the User State Model already includes trust level as a dimension with defined UX effects).

The pattern follows DD-001's scope clarification principle: design axioms govern **interface structure**, not AI decision logic. Trust ramp mechanics are isomorphic to the nudge taxonomy already in `behavioral-framework.md` §2.2 — the same escalation principle applied to data requests instead of nudges.

---

## What Changed

1. **Removed axiom #4** (Progressive Trust Architecture) from `design-principles.md`. Design axioms reduced from 4 to 3.

2. **Added Part 6 (Trust Architecture)** to `behavioral-framework.md`, containing:
   - Trust ramp with 4 levels (New → Warming → Trusting → Dependent)
   - Value-before-ask principle (hard constraint)
   - Trust level → AI behavior gating matrix
   - Trust regression mechanics
   - Trust-personalization conflict resolution (trust always wins — formerly the #1 conflict resolution priority in design axioms)
   - Confidence indicators

3. **Updated conflict resolution** in `design-principles.md`: 3 axioms, ranked Adaptive Composition > Conversational + Visual Integration > Hyperpersonalization. The former "trust trumps personalization" safeguard is now a hard constraint in the behavioral framework (§6.5).

4. **Updated cross-references** across 5 files to point to `behavioral-framework.md` Part 6 instead of the removed design principle #4.

---

## Alternatives Considered

- **Keep Progressive Trust as a narrowed UX axiom (interface resilience only):** Rejected. Once the trust ramp is extracted, the remaining UX constraint is redundant with Adaptive Composition — "compose differently for users with different trust/data levels" is just adaptive composition doing its job with trust as a context input.

- **Keep as a design axiom but move trust ramp details elsewhere:** Rejected. This would leave a thin axiom whose only content is "trust level is a context dimension" — which Adaptive Composition already states.

---

## Impact

| File | Change |
|---|---|
| `design-principles.md` | Removed axiom #4, updated to 3 axioms, new conflict resolution table |
| `behavioral-framework.md` | Added Part 6 (Trust Architecture), updated §5.4 cross-reference, updated changelog |
| `ux-philosophy.md` | principle #4 reference → behavioral-framework.md Part 6 |
| `tension-log.md` | principle #4 reference → behavioral-framework.md Part 6 |
| `journey-catalog/_index.md` | principle #4 reference → behavioral-framework.md Part 6 |
| `knowledge-base/_index.md` | "Four" → "Three", removed "progressive trust" from axiom list |
| `product-design/_index.md` | "four" → "three" |

---

*This decision is immutable. If the trust architecture needs further refactoring, create a new decision record that supersedes this one.*
