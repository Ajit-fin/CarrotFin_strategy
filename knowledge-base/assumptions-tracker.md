# CarrotFin — Assumptions Tracker

> **Domain:** Risk  
> **Last updated:** 2025-04-14  
> **Staleness threshold:** 30 days  
> **Related assumptions:** all  
> **Related decisions:** —

---

## How to Use

- **Status key:** `⬜ Untested` → `🟡 Partially validated` → `✅ Validated` / `❌ Invalidated`
- After each session, run `/feedback` to review and update assumptions
- Invalidated assumptions should trigger a **decision-log entry** explaining the pivot
- New assumptions added during any session must be logged here with date and evidence

---

## Product & Market Assumptions

| ID | Assumption | Status | Evidence | Date Added |
|---|---|---|---|---|
| C1 | Indian millennials/GenZ (25-35) will engage with an AI-powered personal finance advisor | 🟡 Partially validated | InsurEasy traction (~300 users, organic). 11% already use ChatGPT for insurance info [source: Policybazaar survey, as of 2025-04]. GenZ at 14%. Adjacent, not direct validation — InsurEasy is insurance-specific, not holistic finance. | 2025-04-14 |
| C2 | Holistic financial advice (not just one vertical like insurance or MFs) is what users actually want | 🟡 Partially validated | FIRE calculator users ask about goals beyond retirement — travel, education, housing. A tool designed for insurance led to broader personal finance demand. But: self-selected sample, not representative. | 2025-04-14 |
| C3 | Users will share financial planning outputs with family/friends (viral coefficient potential) | 🟡 Partially validated | InsurEasy shareable PDF reports built. Sharing behavior not yet measured. The mechanism exists but the behavior is unproven. Cultural sensitivity around sharing financial details is a risk — see C6. | 2025-04-14 |
| C4 | An adaptive/personalized UI meaningfully outperforms a static dashboard for financial planning | ⬜ Untested | **Core product thesis. Zero A/B data.** This is the highest-conviction, highest-risk assumption. The entire product architecture depends on this being true. Need to validate with first prototype. | 2025-04-14 |
| C5 | Conversational AI integrated with visuals is better than chat + dashboard as separate surfaces | ⬜ Untested | Design thesis only. No user data. International reference: Cleo's conversational model works for spending, but Cleo doesn't do visual integration at the level CarrotFin envisions. | 2025-04-14 |
| C6 | Users trust AI financial advice enough to act on it (beyond just information consumption) | ⬜ Untested | **Critical risk.** InsurEasy insight B2 says users want confidence, not just information. AI may not provide enough confidence alone — 80%+ still consult humans before financial decisions [as of 2025-04]. If this is wrong, the entire AI-native thesis fails. | 2025-04-14 |

---

## Growth & Distribution Assumptions

| ID | Assumption | Status | Evidence | Date Added |
|---|---|---|---|---|
| C7 | Intent-driven channels (Reddit, search, communities) outperform broadcast (LinkedIn, social media) for acquisition | 🟡 Partially validated | InsurEasy LinkedIn launch: 55 likes, 45 comments, <10 new users. Engagement was from personal network, not intent-driven strangers. Reddit/Quora untested for CarrotFin but promising based on active communities (r/IndiaInvestments, r/FIREIndia). | 2025-04-14 |
| C8 | SEO/content-as-acquisition is no longer a reliable primary channel | 🟡 Partially validated | Google AI Overviews reduce organic CTR by up to 70% for informational finance queries [as of 2025]. InsurEasy blog drove <10 new users. The era of "write a blog, get traffic" is ending. | 2025-04-14 |

---

## Competitive Assumptions

| ID | Assumption | Status | Evidence | Date Added |
|---|---|---|---|---|
| C9 | No existing Indian personal finance app has meaningful AI advisory intelligence | 🟡 Partially validated | Competitive analysis [as of 2025-04]: CRED, ET Money, INDmoney, Groww, Kuvera — none have proactive AI advisory. INDmoney has data aggregation but no intelligence layer. Window is open but narrowing — large players (Jio, banks) could enter. | 2025-04-14 |
| C10 | AI-first positioning differentiates from all incumbents | 🟡 Partially validated | Extended from InsurEasy assumption A12. 11% ChatGPT usage for insurance validates consumer appetite. Domain-specific AI should outperform generic LLMs for finance. But: "AI-first" is becoming table stakes in marketing — need to demonstrate it in product, not just positioning. | 2025-04-14 |

---

## Dependency Chain

```
C4 (adaptive UI works) + C5 (integrated conversation + visual works)
  → enables building the product
    C1 (target users will engage) + C6 (users trust AI enough to act)
      → enables achieving product-market fit
        C7 (intent channels work) + C3 (viral sharing works)
          → enables growth
```

> **Sequencing implication:** C4 and C5 must be validated first (via prototype). If they fail, the entire product thesis needs rethinking. C6 is the existential risk that runs parallel to everything.

---

*Run `/feedback` after any strategic session to review and update this tracker.*
