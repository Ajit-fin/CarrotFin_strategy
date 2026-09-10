# CarrotFin — Design Principles

> **Domain:** Design  
> **Last updated:** 2026-08-28  
> **Staleness threshold:** 90 days (foundational, low volatility)  
> **Related assumptions:** C4, GP-01  
> **Related decisions:** V1-Product-Scope, Data-Model

---

## Purpose

Two governing axioms that define what CarrotFin is at the intent level — not implementation instructions. These are not feature specifications. They are constraints that every design decision should be held against. Implementations evolve; the intent behind them should be durable.

---

## The Two Axioms

### 1. What the User Sees Should Depend on Who They Are

Every touchpoint asks: **"What does this specific user need right now — and is that what we're showing them?"**

CarrotFin is not a one-interface-fits-all product. Different users at different life stages, with different financial situations and different levels of confidence, should receive information and prompts calibrated to their actual context. This is the core bet: contextual relevance outperforms breadth and completeness.

**What this means in practice:**
- A user being assessed for an emergency fund should not see the same prompts, depth, or framing as a returning user who already has one.
- Onboarding a 26-year-old single professional looks and feels different from onboarding a 34-year-old in a dual-income household — not just in content, but in pacing, vocabulary, and what gets asked when.
- The AI decides what to surface and how to frame it, based on its model of who the user is. What the user sees is an output of reasoning, not a template.

**How this is implemented today:**
The AI outputs structured data and contextual attributes (field labels, descriptions, shorthand). The app maps these to an appropriate component and renders it in context — inline in the conversation, or as part of a fixed flow. The AI focuses on advisory quality; rendering is a separate concern. This split was a deliberate choice for latency, reliability, and advisory focus — but the rendering mechanism may evolve.

**The test:** If two users with meaningfully different financial contexts see the same thing — and serving them differently would have helped — that's a design failure. The mechanism for achieving differentiation can change; this intent cannot.

> **Depends on:** C4 (contextual relevance outperforms one-size-fits-all) — untested, highest-conviction bet.

---

### 2. Hyperpersonalization Is Architecture, Not a Feature Layer

Personalization at CarrotFin is not a filter on top of a generic app. The product is designed from the ground up to reason about the user — their literacy, risk tolerance, life stage, emotional state, household structure, and trust level.

**What this requires:**
- The data model includes user state, not just user data. We track who the user is, not just what they've done.
- Every component and prompt accepts user context as a first-class input. The question is never just "what is this user's balance?" but "what does this number mean for this specific user right now, and how should we frame it?"
- The AI reasons about the user before it responds. Every advisory output is calibrated to that user's context — vocabulary, depth, tone, and recommendation confidence.

**What this does not mean:**
- It doesn't mean designing three persona variants and switching between them. That's segment UX, not personalization.
- It doesn't mean prioritizing UI novelty. Personalization lives in the quality and relevance of the advice, not in visual differentiation for its own sake.
- It doesn't mean the layout must be different for every user. Personalized content within a consistent structure is still hyperpersonalization; the AI framing a recommendation differently for a novice vs. an expert is hyperpersonalization.

**Household dimension:** The personalization model extends to the household unit, not just the individual. Income structure, dependency load, and household decision-making style are all first-class inputs. A dual-income household gets different framing from a single-earner household — not as a persona, but as a continuous contextual dimension.

> See [ux-philosophy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/ux-philosophy.md) for the full User State Model.

---

## Conflict Resolution

When axioms conflict, resolve using this hierarchy:

| Priority | Axiom | Rationale |
|---|---|---|
| 1 | What the User Sees Should Depend on Who They Are | The core product thesis and the primary competitive bet. If users get the same experience regardless of context, CarrotFin is just another dashboard. |
| 2 | Hyperpersonalization Is Architecture | The long-term moat. Data depth and a rich user state model take time to build. |

**Example conflict:** Hyperpersonalization wants to show an advanced user 6 distinct data points relevant to their context. But showing 6 items creates cognitive overload in the current interaction context. Resolution: Axiom 1 wins — show the most relevant item clearly, and progressively disclose the rest. Relevance and clarity outrank completeness.

---

> **Note on surface architecture and implementation:** How screens are structured, how many surfaces the app has, and how the AI's outputs are rendered are design decisions that belong in the product-design layer and evolve with user data and product maturity. These axioms govern *intent*, not implementation. A screen taxonomy, component palette, or interaction pattern that serves these axioms is correct — regardless of whether it matches what was built previously.

---

*These axioms are foundational. They should rarely change. If one is invalidated, write a decision-log entry explaining why.*

*Last revised 2026-08-28: Reframed both axioms at the intent level to avoid locking in specific rendering mechanisms. Removed Generative/Static/Hybrid screen taxonomy (moved to screen-taxonomy.md as a living product-design artifact, not a foundational axiom). Updated Axiom 1 to describe the current LLM-output + app-rendering split as an implementation approach, not the definition of the axiom itself.*
