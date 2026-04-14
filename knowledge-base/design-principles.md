# CarrotFin — Design Principles

> **Domain:** Design  
> **Last updated:** 2025-04-15  
> **Staleness threshold:** 90 days (foundational, low volatility)  
> **Related assumptions:** C4, C5, C6  
> **Related decisions:** —

---

## Purpose

Five governing axioms that define what CarrotFin is architecturally — not aspirationally. These are not feature requests or nice-to-haves. They are structural constraints that every screen, component, and interaction must satisfy. When two principles conflict, the resolution framework below applies.

---

## The Five Axioms

### 1. Adaptive-First

Every touchpoint asks: **"How does this change for a different user?"**

This is not responsive design (adapting to screen sizes). This is behavioral adaptation — the interface restructures itself based on financial literacy, risk tolerance, life stage, goals, emotional state, and interaction history.

**What it means concretely:**
- A 25-year-old first-time investor sees a single savings goal with encouragement copy and a celebratory animation when they set their first SIP.
- A 45-year-old portfolio optimizer sees asset allocation gaps highlighted with delta metrics, rebalancing suggestions, and tax-loss harvesting opportunities.
- Same app. Same "home screen." Fundamentally different rendering.

**The test:** If a screen looks identical for two users with different financial contexts, it's a design failure. Every static screen needs a justification for why it's static (regulatory, legal, settings).

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

### 4. Generative UI Thinking

Don't design screens. Design systems of constraints.

Traditional app design: a designer creates a screen layout in Figma, an engineer implements it pixel-perfect, every user sees the same layout. CarrotFin inverts this — the designer creates component libraries, composition rules, and context triggers. The AI uses these to assemble the right interface for the right user at the right moment.

**What this means for design work:**
- Design artifacts are not static screen mockups. They are component specs with adaptive behaviors, composition grammars, and context triggers.
- The question is never "What does the home screen look like?" It's "What are the components the AI can place on the home surface, what rules govern their composition, and what user context triggers each one?"
- Mockups are illustrative examples of possible compositions, not canonical layouts.

**The mental model:** Think of the AI as a compositor with a palette of components and a rule book. The designer creates the palette and writes the rules. Users never see the palette — they see the composition.

---

### 5. Progressive Trust Architecture

Trust is earned in micro-increments, not demanded upfront.

Financial products require an unusual amount of trust. CarrotFin asks users to share sensitive data (income, spending, debts) and act on AI-generated financial advice. Both require trust that must be built progressively — never assumed.

**What it means concretely:**
- First interaction: zero data required. The AI provides value with public information ("Here are 3 things every 28-year-old in Bangalore should know about tax-saving").
- Second interaction: one data point requested ("What's your approximate monthly income?"). AI immediately shows value from that single input.
- Over time: the AI earns the right to ask for more, because each data point visibly improves the advice quality.
- Confidence indicators: the AI shows its reasoning, not just its conclusions. "I'm recommending this because [X], [Y], and [Z]. Here's what I'm less sure about."

**The anti-pattern:** A 15-field onboarding form before the user sees any value. Every field without preceding value is a trust violation.

> **Critical dependency:** C6 (users trust AI enough to act) — existential risk. This axiom is the mitigation strategy.

---

## Conflict Resolution

When axioms conflict — and they will — resolve using this hierarchy:

| Priority | Axiom | Rationale |
|---|---|---|
| 1 | Progressive Trust | Without trust, nothing else matters. Users leave before experiencing personalization. |
| 2 | Adaptive-First | The core product thesis. If this fails, we're building another dashboard. |
| 3 | Conversational + Visual Integration | The interaction paradigm. Without it, adaptive-first becomes just "different dashboards per segment." |
| 4 | Generative UI Thinking | The design methodology. Critical for scalability but a means to axioms 1-3, not an end. |
| 5 | Hyperpersonalization Is Architecture | The long-term moat. Requires data depth that won't exist on day one. |

**Example conflict:** Hyperpersonalization wants deep data access. Progressive Trust says don't ask until you've earned the right. Resolution: progressive trust wins — personalization quality increases as trust deepens. Day-one personalization uses inferred signals (device, time, location, browsing behavior), not demanded data.

---

*These axioms are foundational. They should rarely change. If one is invalidated, write a decision-log entry explaining why.*
