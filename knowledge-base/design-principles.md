# CarrotFin — Design Principles

> **Domain:** Design  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** 90 days (foundational, low volatility)  
> **Related assumptions:** C4, C5  
> **Related decisions:** DD-001, DD-002

---

## Purpose

Three governing axioms that define what CarrotFin is architecturally — not aspirationally. These are not feature requests or nice-to-haves. They are structural constraints that every screen, component, and interaction must satisfy. When two principles conflict, the resolution framework below applies.

---

## The Three Axioms

### 1. Adaptive Composition

Every touchpoint asks: **"What should this user see right now, and how should it be assembled?"**

CarrotFin's interface is composed at render time by the AI from a palette of adaptive components and structural composition rules. The AI selects, orders, and configures components based on user context — financial literacy, risk tolerance, life stage, emotional state, trust level, and interaction history. This is not responsive design (adapting to screen sizes). This is behavioral adaptation — the interface restructures itself based on who the user is and what they need.

**What this means for design work:**
- Design artifacts are not static screen mockups. They are component specs with adaptive behaviors, composition grammars, and context triggers.
- The question is never "What does the home screen look like?" It's "What are the components the AI can place on the home surface, what rules govern their composition, and what user context triggers each one?"
- Mockups are illustrative examples of possible compositions, not canonical layouts.

**What it means concretely:**
- A 25-year-old first-time investor sees a single savings goal with encouragement copy and a celebratory animation when they set their first SIP.
- A 45-year-old portfolio optimizer sees asset allocation gaps highlighted with delta metrics, rebalancing suggestions, and tax-loss harvesting opportunities.
- Same app. Same "home screen." Fundamentally different rendering.

**When adaptive composition applies — and when it doesn't:**

Not every screen benefits from runtime composition. When AI-driven assembly doesn't add meaningful user value — for example, legal/regulatory screens, account settings, security flows — fixed or hybrid approaches are appropriate. The screen taxonomy ([screen-taxonomy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/screen-taxonomy.md)) defines when each type applies:
- **Generative screens:** AI composes the interface from components. This is where CarrotFin's differentiation lives.
- **Static screens:** Fixed layout, every user sees the same structure. For legal, regulatory, security, and settings contexts.
- **Hybrid screens:** Fixed structural skeleton with AI-generated content. For screens needing structural predictability with personalized content.

The default bias is generative — adaptive composition is the product thesis. Static and hybrid are concessions to practical necessity, not aspirations. But they are legitimate design choices when runtime composition adds complexity without proportional user value.

**The test:** If a screen looks identical for two users with different financial contexts — *and* context-dependent composition would add value — it's a design failure. Every non-generative screen needs a justification for why it's static or hybrid (regulatory, security, structural predictability).

**The mental model:** Think of the AI as a compositor with a palette of components and a rule book. The designer creates the palette and writes the rules. Users never see the palette — they see the composition.

**Scope note:** This axiom governs **interface assembly** — what users see and how it's arranged. Adaptive *advice* (what the AI recommends and how it frames it) is a separate concern, governed by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md).

> **Depends on:** C4 (adaptive UI outperforms static dashboard) — untested, highest-conviction bet.

---

### 2. Conversational + Visual Integration

The AI doesn't live in a chat window. It speaks through the interface itself.

There is no "chat tab" and "dashboard tab." The conversation IS the navigation. A chart appears because the AI decided this user needs to see their spending trend right now. A suggestion card appears because the AI identified a savings opportunity. A form appears inline because the AI needs one more data point to refine its advice.

**What it means concretely:**
- The AI says "Your spending jumped 40% this month" and a spending breakdown chart materializes below the text — not in a separate dashboard.
- The user asks "Should I increase my SIP?" and the AI responds with a projection visualization inline, comparing current vs. increased SIP outcomes.
- Navigation is emergent, not menu-driven. The AI guides the user to what matters, not to a list of features.

**The anti-pattern:** A "Chat" icon in the bottom nav that opens a separate conversation screen. That's two products glued together, not one integrated experience.

> **Depends on:** C5 (integrated conversation + visual > separate surfaces) — untested.

---

### 3. Hyperpersonalization Is the Architecture

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
| 2 | Conversational + Visual Integration | The interaction paradigm. Without it, adaptive composition becomes just "different dashboards per segment." |
| 3 | Hyperpersonalization Is Architecture | The long-term moat. Requires data depth that won't exist on day one. |

**Example conflict:** Hyperpersonalization wants to render a 6-component information-dense surface for an advanced user. Adaptive Composition's composition rules limit a generative surface to 4 components maximum to maintain coherence. Resolution: Adaptive Composition wins — the AI selects the 4 highest-priority components, dropping the lowest-value two. Composition coherence outranks information density.

> **Note on trust:** Progressive trust — when the AI earns the right to ask for data, how confidence is built, and how trust level governs the AI's behavior — is a product-level concern governed by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) (Part 6: Trust Architecture). Trust level is one of the context dimensions that Adaptive Composition composes against, but the trust architecture itself is not a design axiom — it's AI decision logic.

---

*These axioms are foundational. They should rarely change. If one is invalidated, write a decision-log entry explaining why.*
