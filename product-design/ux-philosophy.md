# CarrotFin — UX Philosophy

> **Domain:** Design  
> **Last updated:** 2026-07-30  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C4, C6, GP-01  
> **Related decisions:** Data-Model

---

## The Thesis: Hyperpersonalization as Architecture

CarrotFin's UX is not a better dashboard. It's not a chatbot with charts bolted on. It's a different category of financial relationship — one where the AI understands who you are and what you need, and delivers advice calibrated to your actual situation.

**Hyperpersonalization is not a feature.** It's not a settings page where users pick a theme. It's not segment-based targeting (millennials see green, boomers see blue). It's the governing intent behind every product decision: the experience should respond to who this specific user is, not to who the average user is.

The closest analogy: a skilled human financial advisor. When you sit down with a good advisor, they don't hand you a standardized form. They ask you questions, adjust their explanation depth, show you only the charts that matter for YOUR situation, and give you a recommendation calibrated to YOUR risk tolerance and life stage. CarrotFin does this — through conversation, at scale, with an AI that gets better with every interaction.

---

## What "Adaptive" Means, Concretely

Adaptive is the most overused word in product design. Here's what it means in CarrotFin — and what it doesn't.

### What It IS

**Content selection:** The AI decides what information to surface. A user who just received a salary sees "Here's how your spending looks this month." A user approaching tax-saving season sees "You have ₹42,000 left in your 80C limit — here are 3 ways to use it."

**Complexity calibration:** The same concept renders at different levels of depth. For a financially literate user: "Your equity allocation is 72% — consider rebalancing to 60/40 given your 5-year horizon." For a novice: "You have most of your money in stocks. Since you'll need this in 5 years, let's move some to safer options."

**Contextual relevance:** What the user sees is calibrated to their current situation — not pulled from a fixed list of things to show everyone. A user who just received their salary sees something different from a user approaching tax-saving season. A user stressed about an uninsured parent sees something different from one in a fully-insured household. The AI reasons about what matters for this person right now before deciding what to surface.

**Interaction mode adaptation:** Some users want to type. Some want to tap. Some want to scroll. The AI learns and adjusts. Heavy typists get richer conversational flows. Tap-oriented users get more card-based interactions with quick-action buttons.

**Emotional calibration:** Financial stress is real. If the AI detects anxiety signals (rapid tapping, spending spike, repeated checking of investment value during a market downturn), it adjusts tone — reassuring, not alarmist. "Markets dropped 8% this week. Your long-term plan is fine. Here's why."

### What It Is NOT

- **Responsive design** — adapting to screen sizes is table stakes, not personalization.
- **A/B testing** — showing different variants to find a global optimum is optimization, not personalization. CarrotFin isn't finding the best layout for everyone — it's finding the right layout for each person.
- **Theming** — dark mode vs. light mode, or choosing accent colors, is customization, not adaptation.
- **Segment-based UX** — building 3 persona-specific flows is segment UX, not individual personalization. CarrotFin has no personas. It has a continuous, multidimensional user context space.

---

## How This Differs from Every Existing Finance App

| Dimension | Category Norm (CRED, ET Money, INDmoney, Groww) | CarrotFin |
|---|---|---|
| **Entry experience** | Fixed layout: net worth widget, recent transactions, portfolio chart. Same for everyone. | Conversational: the AI greets the user based on context and surfaces the most relevant thing — not a fixed set of widgets. A first-timer gets "Let's set up your emergency fund." A power user gets "Your SIP returns are underperforming — here's a rebalancing option." |
| **Navigation** | Bottom tab bar: Home, Invest, Budget, Profile, More | Driven by the conversation — the AI surfaces what matters next rather than asking the user to navigate to it. Navigation structure evolves with the product. |
| **Onboarding** | 8-12 screen wizard: name, email, PAN, bank link, goals | Conversational. "Hi. How old are you?" → one answer → immediate value → "Want to know how much you'd need to retire?" → progressive profiling over time, not upfront interrogation. |
| **Recommendations** | Generic: "Top SIPs this month" (same for every user) | Contextual: "You spent ₹14K on dining last month — that's 2× your usual. Want me to show how this affects your travel fund timeline?" |
| **Insights** | Passive: "Your portfolio is up 12% YTD" | Active + prescriptive: "Your portfolio is up 12%, but you're over-allocated to large-cap. I'd move ₹50K into mid-cap to improve diversification for your 15-year horizon." |

---

## The User State Model

Hyperpersonalization requires a rich model of who the user is. Not just demographics — behavioral, emotional, and contextual dimensions.

| Dimension | What It Captures | How It's Inferred | How It Affects UX |
|---|---|---|---|
| **Financial literacy** | Scale 1-5. Does the user understand SIPs, asset allocation, tax harvesting? | Interaction patterns: Do they skip explanations? Do they ask "what does this mean?" | Governs explanation depth, jargon usage, component density |
| **Risk tolerance** | Conservative → Aggressive | Stated preference + revealed preference (investment choices, reaction to volatility) | Governs recommendation aggressiveness, chart framing |
| **Life stage** | Single earner → Young family → Mid-career → Pre-retirement | Stated (age, dependents) + inferred (spending patterns, goal types) | Governs which financial topics are prioritized, default goal suggestions |
| **Engagement mode** | Conversational → Visual → Hybrid | Interaction history: typing frequency, card taps, time spent on charts | Governs interaction modality balance |
| **Emotional state** | Calm → Anxious → Excited → Overwhelmed | Session signals: browsing speed, portfolio check frequency, time of day | Governs tone, pacing, information density |
| **Trust level** | New → Warming → Trusting → Dependent | Longitudinal: data shared, recommendations acted on, return frequency | Governs what data the AI asks for, recommendation confidence display |

> **Day-one constraint:** Most of these dimensions start unknown. The AI must provide value with minimal context and deepen personalization as it learns. See [Trust Architecture](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) (Part 6) in the Behavioral Intelligence Framework.

---

## The Data → Intelligence → Experience Pipeline

```
User Data (inputs, behavior, context)
    ↓
User State Model (literacy, risk, stage, mode, emotion, trust)
    ↓
AI Reasoning Layer (what does this user need right now?)
  → Governed by: behavioral-framework.md
  → Behavioral principles, adaptive rigor, content framing, verification anchors
    ↓
AI Output (what to surface, what to ask, how to frame it)
    ↓
Rendered Experience (what the user sees and interacts with)
```

Each layer in this pipeline is adaptive. Data informs the model. The model informs the AI's reasoning. The reasoning informs what the AI outputs — what to surface, how to explain it, what question to ask next. The rendering layer takes that output and presents it appropriately.

**The AI Reasoning Layer** is operationalized by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) — the decision intelligence layer that translates user state into behaviorally informed decisions about what to show, when, and how to frame it. It defines the cognitive biases the AI leverages, the financial guardrails it cannot violate, and the trust-building mechanisms that earn the right to advise.

**Surface architecture** — how many surfaces, their roles, and how users navigate — sits above this pipeline. The pipeline outputs to whatever surface architecture is chosen; the personalization principles apply regardless.

---

## Household-Level Personalization (Data-Model)

> **Added:** 2026-07-29 | Source: Data-Model (Group Profile Model), GP-01

The hyperpersonalization thesis extends beyond the individual to the **household/family unit**. With the group profile model (Data-Model), the User State Model operates at two levels:

1. **Individual dimensions** — literacy, risk tolerance, emotional state, trust level remain per-person.
2. **Household dimensions** — income structure (dual vs. single income), dependency load (granular dependent categories), housing status, decision-making style (solo vs. joint vs. family consensus).

The AI must personalize differently based on household structure:
- A **single earner** household gets income disruption scenarios centered on the sole earner.
- A **dual-income salaried** household gets natural hedging explanations and joint contribution planning.
- A household with **uninsured aging parents** triggers a medical buffer advisory that doesn't appear for fully-insured families.

The User State Model table above captures individual dimensions. Household-level context comes from the GroupProfile entity model (Person, Household, Relationship) and is injected alongside individual context.

---

## What We Don't Know Yet

Honesty about gaps is a design principle, not a weakness.

- **Does contextual, advisory-led UX outperform generic dashboards?** (C4 — untested, highest-risk assumption). We believe it does; we don't have data yet.
- **How quickly can the AI build an accurate user state model?** If meaningful personalization requires 10+ sessions, the early experience may feel generic. Managing this gap is a design challenge.
- **What monitoring/tracking patterns work in a chat-primary product?** Users who want to "check in" on portfolio state or spending need a fast path — one that doesn't require a full conversational exchange. How to serve this without a dashboard is an open design question.
- **Can users override or influence AI surfacing?** Should users be able to pin topics, mute suggestions, or request a different focus? Flexibility vs. AI coherence is an open tension.

> See [tension-log.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/tension-log.md) for the full list of unresolved design tensions.

---

*This file defines what CarrotFin's experience IS at the intent level. It should evolve rarely, and only when the fundamental thesis changes. Implementation details live in screen-taxonomy.md and interaction-model.md.*

*Last revised 2026-08-28: Removed AI-as-layout-compositor and composed-dashboard framing. Reanchored to intent-level language — what the user sees should depend on who they are, delivered through conversation and contextually appropriate inline elements. Pipeline updated to remove hardcoded "Component Selection + Composition" step.*
