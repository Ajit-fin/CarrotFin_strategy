# CarrotFin — Screen Taxonomy

> **Domain:** Design  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C4, C5  
> **Related decisions:** —

---

## Purpose

Not every screen in CarrotFin is AI-generated. Some must be fixed (legal, regulatory). Some blend fixed structure with AI-generated content. This taxonomy defines three screen types, when each applies, what constraints govern them, and how the AI decides which to render.

---

## The Three Types

### Generative Screens

**Definition:** The AI assembles the screen from a component palette based on user context. No two users see the same layout. There is no canonical "design" for this screen — there is a composition grammar and a set of eligible components.

**When to use:** Primary experience surfaces where the user's context should drive what they see. These are the screens where CarrotFin's differentiation lives.

**How the AI decides what to render:**
1. Evaluate user state model (literacy, risk, life stage, engagement mode, emotional state, trust level)
2. Identify the user's most urgent financial priority (unpaid bills > goal at risk > optimization opportunity > education)
3. Select components from the eligible set, ordered by relevance to the current priority
4. Apply composition rules: max 5-7 components per surface, highest-priority at top, progressive disclosure for secondary items

**Constraints:**
- Must include at least one actionable element (not just informational)
- Must not exceed cognitive load thresholds (component density limits per literacy level)
- Must maintain spatial consistency for anchor elements (e.g., primary action button location doesn't jump between sessions)

**Examples:**
| Screen | What User A (novice, 25, single) sees | What User B (experienced, 40, family) sees |
|---|---|---|
| **Home** | Emergency fund progress bar, "Did you know?" financial tip card, single goal tracker | Spending vs. budget summary, 3 goal trackers with alerts, portfolio rebalancing nudge, upcoming bill reminder |
| **Financial Health** | Simple health score with one improvement suggestion and encouraging copy | Multi-axis health breakdown (liquidity, protection, growth, tax efficiency) with ranked action items |
| **Recommendations** | One focused suggestion: "Start a ₹500 SIP" with step-by-step | Three suggestions ranked by impact: rebalance, tax-harvest, increase insurance cover |

---

### Static Screens

**Definition:** Pre-designed, fixed layout. Every user sees the same structure (though content may vary — e.g., their own profile data). No AI composition logic. Traditional app screens.

**When to use:** Where consistency is a requirement — legal, regulatory, settings, account management. Where user trust depends on predictability, not personalization.

**Why some screens must be static:**
- **Legal obligation:** Terms of service, privacy policy, KYC flows. The regulator cares about standardized disclosure, not personalized presentation.
- **User expectation:** Profile settings, notification preferences, linked accounts. Users expect these to be in predictable locations and behave predictably.
- **Security context:** Password changes, 2FA setup, payment authorization. Personalization in security flows creates confusion and potential social engineering vectors.

**Examples:**
- Terms of Service / Privacy Policy
- KYC verification flow
- Profile and account settings
- Notification preferences
- Linked bank accounts management
- Support / help center

**Design rule:** Static screens should be minimal, clean, and invisible. They're infrastructure, not experience. The user shouldn't spend time here — these screens should get the job done and return the user to the generative experience.

---

### Hybrid Screens

**Definition:** Fixed structural skeleton with AI-generated content filling key sections. The layout is predictable, but the content within it adapts to user context.

**When to use:** Screens where users need structural consistency to orient themselves, but the content within that structure should be personalized. Transitional screens that bridge static and generative paradigms.

**The structure/content split:**
- **Fixed:** Section headers, navigation within the screen, form layout, action button placement
- **AI-generated:** Section content, suggestions, projections, explanatory text, chart data, inline recommendations

**Examples:**
| Screen | Fixed Structure | AI-Generated Content |
|---|---|---|
| **Goal Setup** | Form fields: goal name, target amount, timeline, priority | Suggested target amount ("People in your income bracket typically aim for ₹X"), projected savings trajectory chart, AI suggestion for optimal SIP amount |
| **Investment Detail** | Asset name, current value, returns chart, transactions list | AI commentary: "This fund underperformed its benchmark by 3% over 12 months. Consider switching to [alternative]." Performance context relative to user's overall allocation. |
| **Monthly Report** | Section layout: spending summary, savings progress, goal updates, next steps | Content within each section adapts: a user who over-spent gets spending-focused insights; a user on track gets goal-acceleration suggestions |

**Hybrid progression:** As user trust increases, hybrid screens can become more generative. Early in the relationship, a goal setup screen is mostly form (fixed) with light AI suggestions. Later, the AI takes more initiative — pre-filling fields, suggesting goals the user hasn't thought of, reordering sections by relevance.

---

## Decision Framework: Which Type?

```
Is this screen regulated, legal, or security-critical?
  → YES: Static
  → NO: Does the user need structural predictability to orient?
    → YES: Hybrid (fixed structure, AI content)
    → NO: Can the AI meaningfully compose this differently for different users?
      → YES: Generative
      → NO: Hybrid (consider: should this screen exist?)
```

**Default bias:** Generative. If you're debating between hybrid and generative, lean generative. The whole point of CarrotFin is that the AI composes the experience. Static and hybrid are concessions to practical necessity, not aspirations.

---

## Composition Rules for Generative Screens

These rules govern how the AI assembles generative screens. They're guardrails, not templates.

| Rule | Why |
|---|---|
| **Max 5-7 components per surface** | Cognitive load. More than 7 items overwhelm. Research: Miller's law applies to financial decision-making even more strictly than general UI. |
| **Highest-priority item at top** | Attention decays with scroll depth. The most actionable item must be immediately visible. |
| **One primary CTA per surface** | Multiple equal-weight actions create paralysis. The AI picks the ONE thing the user should do. |
| **Anchor elements stay consistent** | The primary action button, navigation affordance, and AI "persona" element should maintain spatial position across sessions. Consistency where it matters, adaptation where it helps. |
| **Progressive disclosure for depth** | Show headline insight first. Expandable detail on tap. Full analysis on request. Never dump everything. |

---

*This file pairs with `interaction-model.md` (which defines HOW users interact) and `ux-philosophy.md` (which defines WHY the taxonomy exists).*
