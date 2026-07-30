# CarrotFin — Design Principles

> **Domain:** Design  
> **Last updated:** 2026-07-30  
> **Staleness threshold:** 90 days (foundational, low volatility)  
> **Related assumptions:** C4, GP-01  
> **Related decisions:** V1-Product-Scope, Data-Model

---

## Purpose

Two governing axioms that define what CarrotFin is architecturally — not aspirationally. These are not feature requests or nice-to-haves. They are structural constraints that every screen, component, and interaction must satisfy. When the two principles conflict, the resolution framework below applies.

---

## The Two Axioms

### 1. Adaptive Composition

Every touchpoint asks: **"What should this user see right now, and how should it be assembled?"**

CarrotFin's interface is composed at render time by the AI from a palette of adaptive components and structural composition rules. The AI selects, orders, and configures components based on user context — financial literacy, risk tolerance, life stage, emotional state, trust level, and interaction history. This is not responsive design (adapting to screen sizes). This is behavioral adaptation — the interface restructures itself based on who the user is and what they need.

**What this means for design work:**
- Design artifacts are not static screen mockups. They are component specs with adaptive behaviors, composition grammars, and context triggers.
- The question is never "What does the home screen look like?" It's "What are the components the AI can place on a given surface, what rules govern their composition, and what user context triggers each one?"
- Mockups are illustrative examples of possible compositions, not canonical layouts.

**What it means concretely:**
- A 26-year-old single professional (S1) sees an emergency fund progress bar with encouragement copy and a milestone animation when they hit their Starter Shield target.
- A 32-year-old parent in a dual-income household (S2) sees family coverage gaps — an uninsured aging parent flagged as a medical buffer risk, a household income risk assessment, and a contribution plan calibrated to their joint cash flow.
- Same app. Same surface. Fundamentally different rendering — driven by household context, not just individual demographics.

**When adaptive composition applies — and when it doesn't:**

Not every screen benefits from runtime composition. When AI-driven assembly doesn't add meaningful user value — for example, legal/regulatory screens, account settings, security flows — fixed or hybrid approaches are appropriate. The screen taxonomy ([screen-taxonomy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/screen-taxonomy.md)) defines when each type applies:
- **Generative screens:** AI composes the interface from components. This is where CarrotFin's differentiation lives.
- **Static screens:** Fixed layout, every user sees the same structure. For legal, regulatory, security, and settings contexts.
- **Hybrid screens:** Fixed structural skeleton with AI-generated content. For screens needing structural predictability with personalized content.

Composition strategy should match the user's behavioral mode and context. Contexts where users need to monitor or glance at state frequently may benefit from structural stability (hybrid composition). Contexts where users are exploring, deciding, or being advised benefit from fully generative composition. Static and hybrid are legitimate design choices — not just concessions — when the behavioral mode warrants them.

**The test:** If a screen looks identical for two users with different financial contexts — *and* context-dependent composition would add value — it's a design failure. Every non-generative screen needs a justification for why it's static or hybrid (regulatory, security, structural predictability, monitoring-mode behavioral fit).

**The mental model:** Think of the AI as a compositor with a palette of components and a rule book. The designer creates the palette and writes the rules. Users never see the palette — they see the composition.

**Scope note:** This axiom governs **interface assembly** — what users see and how it's arranged. Adaptive *advice* (what the AI recommends and how it frames it) is a separate concern, governed by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md).

> **Depends on:** C4 (AI-driven composition outperforms static, one-size-fits-all layouts) — untested, highest-conviction bet.

---

### 2. Hyperpersonalization Is the Architecture

Personalization is not a feature layer bolted on top of a generic app. The entire UI stack is built to reason about the user.

**What this requires technically:**
- Data models include user state (financial literacy score, risk tolerance, life stage, emotional indicators, interaction history).
- Every component accepts user context as input — not just "data" but "who is looking at this data."
- The AI decides what to show, when, and how — it composes the interface, it doesn't fill templates.

**What this means for design:**
- There is no "default" home screen to design in Figma. There are constraint systems that define what CAN appear and HOW the AI selects.
- Component specs include adaptive behavior definitions: "At literacy level 1, show headline only. At level 3, show headline + sparkline + breakdown."
- Onboarding is not a fixed flow — it's a conversation that progressively profiles the user, revealing interface complexity as the AI learns who they are.

**The trap to avoid:** Designing 3 "personas" and building 3 versions of each screen. That's segment-level personalization. CarrotFin does individual-level, contextual, real-time personalization.

---

## Conflict Resolution

When axioms conflict — and they will — resolve using this hierarchy:

| Priority | Axiom | Rationale |
|---|---|---|
| 1 | Adaptive Composition | The core product thesis — both the bet (adaptive > static) and the mechanism (AI-composed interfaces). If this fails, we're building another dashboard. |
| 2 | Hyperpersonalization Is Architecture | The long-term moat. Requires data depth that won't exist on day one. |

**Example conflict:** Hyperpersonalization wants to render a 6-component information-dense surface for an advanced user. Adaptive Composition's composition rules limit a generative surface to 4 components maximum to maintain coherence. Resolution: Adaptive Composition wins — the AI selects the 4 highest-priority components, dropping the lowest-value two. Composition coherence outranks information density.

> **Note on trust:** Progressive trust — when the AI earns the right to ask for data, how confidence is built, and how trust level governs the AI's behavior — is a product-level concern governed by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) (Part 6: Trust Architecture). Trust level is one of the context dimensions that Adaptive Composition composes against, but the trust architecture itself is not a design axiom — it's AI decision logic.

---

> **Note on surface architecture:** The specific arrangement of surfaces (how many, their roles, and how users navigate between them) is a design decision that evolves with user data and product maturity — not a foundational axiom. Surface architecture decisions are recorded in the design-decisions layer as they are made and validated.

---

*These axioms are foundational. They should rarely change. If one is invalidated, write a decision-log entry explaining why.*

*Last revised 2026-07-30: Axiom 2 (Conversational + Visual Integration) deleted — prescribing surface architecture is an implementation decision, not a foundational constraint. Former Axiom 3 renumbered to Axiom 2. Axiom 1 scoped to acknowledge that composition strategy varies by behavioral mode.*
