# CarrotFin — User Insights

> **Domain:** Users  
> **Last updated:** 2026-04-16  
> **Staleness threshold:** 30 days  
> **Related assumptions:** C1, C2, C3, C6  
> **Related decisions:** —

---

## Target Segments

### Primary Hypothesis: Indian Millennials/GenZ (25-35)

CarrotFin's initial target is digitally native Indians aged 25-35 — salaried professionals in metros/Tier-1 cities who are:
- **Earning enough to need financial planning** but not wealthy enough to access human financial advisors (₹5L-25L annual income band)
- **Comfortable with AI interfaces** — already using ChatGPT, voice assistants, AI-powered apps
- **Underserved by existing options** — too sophisticated for generic dashboard apps, too cost-conscious for human advisors
- **At a life-stage inflection** — early career decisions (emergency fund, first investments, insurance, tax-saving) compound massively

| Segment | Description | Priority | Hypothesis Status |
|---|---|---|---|
| **S1: Young professionals (25-30)** | First-time earners/investors, single, salaried, metro, high AI comfort | 🟢 Primary | 🟡 Partially validated (InsurEasy traction, FIRE calculator usage) |
| **S2: Young families (28-35)** | Recently married or new parents, dual income, need protection + planning | 🟢 Primary | 🟡 Partially validated (FIRE calculator users ask about goals beyond retirement) |
| **S3: Aspiring FIRE pursuers (28-40)** | Financially literate, pursuing financial independence, active in r/FIREIndia | 🟡 Secondary | 🟡 Partially validated (FIRE calculator organic adoption) |
| **S4: Mid-career optimizers (35-45)** | Portfolio complexity growing, need consolidation + advice, higher income | 🔴 Deferred | ⬜ Untested |
| **S5: Gig workers/freelancers** | No employer benefits, need insurance + retirement planning from scratch | 🔴 Deferred | ⬜ Untested |

> **Why S1+S2 first:** Highest AI comfort, most forgiving of early product imperfections, and their financial needs (emergency fund, first SIP, term insurance) are well-understood enough to build strong AI advisory around.

### Archetype Gaps Identified — Emergency Fund Research (Phase 0)

> **Added:** 2026-04-16 | Source: `research/market/emergency-fund-philosophy.md`
> The EF use case research surfaced archetypes with high emergency fund urgency that are either absent or insufficiently differentiated in the current segment model. Not primary V1 targets, but relevant for advisory accuracy.

| New / Refined Archetype | Why It Matters for EF | Recommended Disposition |
|---|---|---|
| **S2 refinement: Split into S2a (pre-kids) and S2b (post-first-child)** | EF sizing jumps meaningfully at the transition to parenthood (childcare costs, parental medical exposure, non-deferrable school fees). Treating them as one segment blunts advisory precision. | Refine S2 when EF journey is in Phase 2 design. The 8-dimension assessment handles this adaptively — but personas for journey documentation need this split. |
| **Small Business Owner** (30–50, variable business income, may have staff obligations) | EF sizing: 9–12 months. Business and personal finances are often conflated — emergency fund design must address cash-flow cycles, not just personal income. | Lower priority for V1 (complex to serve well). Flag as potential S4-adjacent segment post-PMF. |
| **Pre-Retiree** (50–60, approaching retirement, corpus protection critical) | EF target: 12–18 months, framed as *runway* not income replacement. Re-employment difficulty at this age makes income disruption disproportionately severe. | Defer from V1 — different mental model, different product framing entirely. Note for future EF v2. |
| **Tier 2/3 Joint Family** (25–40, lower cost base, family support partially available) | EF target: 4–8 months. Lower absolute costs reduce fund size, but lower income and weaker formal insurance coverage offset this. Informal family safety net is real but unreliable with urbanization. | V1 advisory engine must not assume metro cost structures — city-tier input is critical to accuracy. The 8-dimension model handles this; no separate segment journey needed for V1. |

---

## Existing User Signals

> ⚠️ CarrotFin has zero direct user data. All signals are from InsurEasy (~300 users) and the FIRE calculator (~500 users). These are adjacent but not identical products.

### Behavioral Insights (from InsurEasy era)

| # | Insight | Source | Implication for CarrotFin |
|---|---|---|---|
| B1 | **80%+ of digitally savvy Indians consult a human before financial decisions.** Even users who engage deeply with AI guidance still want someone — a friend, spouse, parent — to say "yes, do this." | Policybazaar survey [as of 2025-04]; InsurEasy session data | AI alone won't close the confidence gap. CarrotFin must either earn human-level trust or facilitate human validation within the flow. This is existential — see assumption C6. |
| B2 | **Users want confidence, not information.** The bottleneck isn't access to financial data — it's the courage to act on it. Users come in saying "help me understand" but actually mean "tell me I'm making the right choice." | InsurEasy conversation patterns [as of 2025-03] | The AI's job isn't to educate — it's to advise with conviction. Copy, tone, and interaction design must project confidence without being reckless. "Based on your situation, here's what I'd do" > "Here are 5 options, you decide." |
| B3 | **Personal network engagement ≠ acquisition.** Friends will like/share/comment out of support, not because they need the product. Vanity metrics from personal networks are misleading. | InsurEasy LinkedIn launch [as of 2026-02]: 55 likes, 45 comments, <10 new users | Acquisition must come from intent-driven channels (people actively seeking financial help), not broadcast to personal network. Content marketing on LinkedIn is social proof, not acquisition. |

### Signals from FIRE Calculator Users

| Signal | Detail | Implication |
|---|---|---|
| **Users want holistic planning** | FIRE calculator users ask about goals beyond retirement — travel, education, housing, emergency fund | Validates C2: single-vertical tools (insurance only, investment only) leave demand on the table |
| **Progressive disclosure works** | Step-by-step flow with increasing complexity had strong completion rates | Validates the progressive commitment model for CarrotFin onboarding |
| **Variant analysis resonates** | Users engage with "what-if" scenarios (changing return rates, adding goals) | Users want to explore, not just be told. The AI should invite simulation and exploration. |
| **Shareability is latent** | Shareable PDF reports were built but sharing behavior not yet measured | C3 (viral sharing) remains untested but the mechanism is ready to validate |

---

## Unmet Needs in Indian Personal Finance

Based on InsurEasy user patterns, FIRE calculator engagement, and competitive analysis:

| Need | Current State | CarrotFin Response |
|---|---|---|
| **"What should I do with my money?"** | No app answers this. They show data and let users figure it out. | AI proactively advises based on the user's complete financial picture |
| **"Am I on track?"** | Goal tracking exists (Kuvera, ET Money) but it's passive — user must check | AI monitors and surfaces alerts: "You're ₹8K behind your emergency fund goal this month" |
| **"Can I afford this?"** | No existing tool does real-time affordability checks against financial goals | AI can model the impact of a purchase on goal timelines: "Buying this car delays your home down payment by 14 months" |
| **"Explain this to me like I'm 5"** | Financial jargon everywhere. Even "simple" apps assume literacy. | AI adapts explanation depth and vocabulary to the user's demonstrated literacy level |
| **"I want to start but I'm overwhelmed"** | Apps dump you into a dashboard with 15 features visible at once | CarrotFin starts with ONE thing: "Let's figure out your emergency fund first." AI controls complexity exposure. |

---

## User Data We Don't Have (Honest Gaps)

- **Zero CarrotFin-specific user data** — all insights are inferred from adjacent products
- **No A/B test data** on adaptive vs. static UI (assumption C4 is untested)
- **No data on AI trust for financial decisions** (vs. just information consumption) — C6 is the biggest risk
- **No demographic data** on FIRE calculator users — we don't know if they match our target segment
- **No retention/engagement data** beyond first session for either product

---

*This file grows over time. Append user feedback, interview notes, and behavioral data as collected.*
