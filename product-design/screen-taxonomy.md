# CarrotFin — Screen Taxonomy

> **Domain:** Design  
> **Last updated:** 2026-08-28  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C4  
> **Related decisions:** interaction-model (absorbed DD06)

---

## Purpose

Not every interaction in CarrotFin is the same. Some moments call for open-ended conversation. Some require a predictable, fixed flow (legal, security). Some sit in between — structured but contextually informed. This taxonomy describes the three types of interaction contexts and when each applies.

This is a living vocabulary. As the product adds new surfaces or patterns, this file should be updated to reflect them — not treated as a sealed specification.

---

## The Three Types

### 1. Conversational Contexts

**What they are:** The primary interaction mode. The AI engages the user as an advisor — asking, explaining, recommending — with inline structured elements (inputs, summaries, comparisons) woven in when they serve the user better than text alone.

**When to use:** Discovery, exploration, decision-making, onboarding, advisory. Any context where the AI needs to understand user context before responding, or where reasoning through the situation with the user adds value.

**Characteristics:**
- The AI drives the pacing — it decides when to ask, when to explain, when to show data
- Inline structured elements appear within the flow (not as separate pages)
- Progressive: each exchange builds on the last
- The user's responses (typed or tapped) keep the context alive

**Examples:**
- Emergency Fund assessment and contribution planning
- "What should I do with my bonus?"
- New user onboarding
- Any journey that requires profiling the user before advising

---

### 2. Fixed/Utility Contexts

**What they are:** Predictable, pre-designed flows. Every user sees the same structure. The AI is not composing here — the layout and sequence are fixed.

**When to use:** Legal, regulatory, security, account management. Where consistency is a requirement, not a tradeoff.

**Why some contexts must be fixed:**
- **Legal obligation:** Terms of service, privacy policy, KYC. Standardized disclosure is the requirement.
- **User expectation:** Profile settings, notification preferences, linked accounts. Predictability is the UX here.
- **Security context:** Password changes, 2FA, payment authorization. Personalization in security flows creates confusion and social engineering vectors.

**Design rule:** These screens are infrastructure. Minimal, clean, and invisible. Get the job done and return the user to the conversational experience.

**Examples:**
- Terms of Service / Privacy Policy
- KYC verification flow
- Profile and account settings
- Notification preferences
- Linked bank accounts management

---

### 3. Structured/Hybrid Contexts

**What they are:** Fixed structural skeleton with AI-informed content within it. The layout is predictable; the content or emphasis adapts to user context.

**When to use:** Contexts where users need structural consistency to orient themselves (frequent-return monitoring, goal review, report-style summaries), but where the content inside that structure should be calibrated to their situation.

**The structure/content split:**
- **Fixed:** Section layout, navigation within the screen, form field placement, action button position
- **AI-informed:** Content within sections, emphasis, suggestions, explanatory framing, chart data

**Examples:**
- Goal setup form (fixed fields, AI-suggested target amounts and projections)
- Monthly spending summary (fixed sections, AI-selected emphasis based on where the user's attention matters most)
- Investment detail view (fixed layout, AI commentary calibrated to the user's allocation and horizon)

**Note:** As the product matures and monitoring/tracking use cases grow, this category will expand. These aren't concessions — they're the right choice when structural predictability serves the user's behavioral mode better than an open conversational flow.

---

## Decision Framework: Which Type?

```
Is this context regulated, legal, or security-critical?
  → YES: Fixed/Utility
  → NO: What is the primary behavioral mode here?
    → Frequent monitoring / returning to check state: lean Structured/Hybrid
    → Decision-making / exploration / advisory: lean Conversational
    → Transactional (user knows what they want to do): inline structured action within Conversational
```

The guiding question: does the user need to think through this with the AI, or do they need to act on something they already know they want? The former is Conversational; the latter may be best served by a more structured pattern.

---

## Inline Component Vocabulary (Conversational Contexts)

Within the Conversational Stream, the AI may trigger the app to render structured elements inline. These are not separate screens — they appear within the flow.

| Component Type | Purpose | Examples |
|---|---|---|
| **Input components** | Structured data capture within the stream | Option chips, range pickers, number sliders, date selectors |
| **Summary cards** | Render a data result or insight compactly | EF target card, spending breakdown, goal progress |
| **Comparison displays** | Show options or projections side-by-side | Current vs. rebalanced trajectory, SIP scenario comparison |
| **Confirmation elements** | Action confirmation without leaving the stream | "Start SIP of ₹5,000/month?" with confirm/modify |
| **Inline charts** | Data visualization embedded in a conversational turn | Spending breakdown, portfolio allocation, goal trajectory |

This vocabulary grows as new patterns are built and validated. Components are not enumerated upfront as an exhaustive list — they're added when a new interaction need is identified and solved.

---

*This file pairs with `interaction-model.md` (which defines HOW users interact) and `ux-philosophy.md` (which defines WHY these patterns exist).*
