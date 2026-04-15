# CarrotFin — UX Philosophy

> **Domain:** Design  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C4, C5, C6  
> **Related decisions:** —

---

## The Thesis: Hyperpersonalization as Architecture

CarrotFin's UX is not a better dashboard. It's not a chatbot with charts. It's a new category of financial interface — one where the AI is the architect of the experience, assembling a unique interface for each user based on who they are, what they need, and what they should do next.

**Hyperpersonalization is not a feature.** It's not a settings page where users pick a theme. It's not segment-based targeting (millennials see green, boomers see blue). It's the governing architecture of every pixel on every screen.

The closest analogy: a skilled human financial advisor. When you sit down with a good advisor, they don't hand you a standardized form. They ask you questions, read your body language, adjust their explanation depth, show you only the charts that matter for YOUR situation, and give you a recommendation calibrated to YOUR risk tolerance and life stage. CarrotFin does this — digitally, at scale, with an AI that gets better with every interaction.

---

## What "Adaptive" Means, Concretely

Adaptive is the most overused word in product design. Here's what it means in CarrotFin — and what it doesn't.

### What It IS

**Content selection:** The AI decides what information to surface. A user who just received a salary sees "Here's how your spending looks this month." A user approaching tax-saving season sees "You have ₹42,000 left in your 80C limit — here are 3 ways to use it."

**Complexity calibration:** The same concept renders at different levels of depth. For a financially literate user: "Your equity allocation is 72% — consider rebalancing to 60/40 given your 5-year horizon." For a novice: "You have most of your money in stocks. Since you'll need this in 5 years, let's move some to safer options."

**Component composition:** The home surface assembles from a palette of components — spending insight card, goal progress tracker, recommendation card, alert banner, SIP nudge. The AI selects which components appear, in what order, at what density, based on the user's current context. No two home screens are identical.

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
| **Home screen** | Fixed layout: net worth widget, recent transactions, portfolio chart. Same for everyone. | AI-composed surface: different components for different users. A first-timer sees "Let's set up your emergency fund." A power user sees "Your SIP returns are underperforming — here's a rebalancing option." |
| **Navigation** | Bottom tab bar: Home, Invest, Budget, Profile, More | Emergent. The AI surfaces what matters. Users CAN deep-link to specific areas, but the default experience is guided, not menu-driven. |
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

## The Data → Intelligence → Interface Pipeline

```
User Data (inputs, behavior, context)
    ↓
User State Model (literacy, risk, stage, mode, emotion, trust)
    ↓
AI Reasoning Layer (what does this user need right now?)
  → Governed by: behavioral-framework.md
  → Behavioral principles, adaptive rigor, content framing, verification anchors
    ↓
Component Selection + Composition (assemble the interface)
    ↓
Rendered Experience (what the user sees and interacts with)
```

Each layer in this pipeline is adaptive. Data informs the model. The model informs the AI's reasoning. The reasoning informs component selection. The components themselves adapt their rendering based on user context.

**The AI Reasoning Layer** is operationalized by the [Behavioral Intelligence Framework](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/behavioral-framework.md) — the decision intelligence layer that translates user state into behaviorally informed decisions about what to show, when, and how to frame it. It defines the cognitive biases the AI leverages, the financial guardrails it cannot violate, and the trust-building mechanisms that earn the right to advise.

**This is not a template system.** Template systems have pre-defined layouts that get populated with data. CarrotFin's system has a component palette and composition rules — the AI writes the layout at render time.

---

## What We Don't Know Yet

Honesty about gaps is a design principle, not a weakness.

- **Does adaptive UI actually outperform static?** (C4 — untested, highest-risk assumption)
- **How complex can compositions get before users feel lost?** We need constraint guardrails — max components per surface, minimum consistency, anchor elements.
- **How quickly can the AI build an accurate user state model?** If it takes 20 sessions to personalize meaningfully, the first 19 sessions might feel generic.
- **Can users override the AI's composition?** Should they be able to pin components, hide recommendations, or switch to a fixed layout? Flexibility vs. coherence is an open tension.

> See [tension-log.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/tension-log.md) for the full list of unresolved design tensions.

---

*This file defines what CarrotFin's experience IS at the deepest level. It should evolve rarely and only with a corresponding design-decision record.*
