# Emergency Fund: Product Philosophy for Adaptive Personalization

> **Domain:** Market Research — Goal-Based Investing  
> **Topic:** Emergency Fund — Guiding philosophy for adaptive, hyperpersonalized advisory  
> **Last updated:** 2026-04-16 (scoping decisions resolved)  
> **Staleness threshold:** 90 days (update when macro conditions shift or new India-specific data becomes available)  
> **Source:** Distilled from raw research session [Conversation 3e343af4] with date annotations verified  
> **Provenance:** `emergency_fund_research.md` → `/Users/kshekhaw/.gemini/antigravity/brain/3e343af4-b1c6-4ded-873b-d48d77041029/`  
> **Related KB:** `user-insights.md` (archetype mapping), `assumptions-tracker.md` (EF-11 through EF-16)

---

## 1. First-Principles Definition

An emergency fund is **financial insurance, not an investment**. Its core job: absorb income shocks so the user never has to:
- Liquidate long-term investments at a loss
- Take on high-interest debt (personal loans at 18–24% APR, credit cards at 36–42% APR)
- Compromise family needs under duress
- Make panic-driven financial decisions

> **Core product axiom:** The right fund size is the amount that lets someone navigate their *worst plausible scenario* without financial ruin — not a rigid "X months" formula. The app must make this concretely personal.

### What Qualifies as an Emergency (In-App Framing)

This distinction must be surfaced proactively — users conflate "unexpected" with "unplanned":

| ✅ Emergency | ❌ Not an Emergency |
|:---|:---|
| Job loss / income disruption | Planned vacation |
| Medical emergency (self/dependent) | New phone or gadget |
| Urgent home/vehicle repair | Wedding expenses (planned) |
| Family crisis requiring travel | Festival shopping |
| Sudden legal obligation | Investment opportunity |

---

## 2. The Eight Sizing Dimensions

The fund target is derived from **8 adaptive dimensions** — not a static formula. This is the product's core advisory logic for the EF use case.

> **Assumption EF-11:** These 8 dimensions are the right and sufficient set for sizing. Tracked in assumptions-tracker.md — validate with first user cohort.

| # | Dimension | Product-Level Implication |
|:---|:---|:---|
| 1 | **Income Stability** | Government/PSU: 3–4 months. Large private: 4–6. Startup: 6–8. Freelancer: 8–12. Gig: 6–10. SMB owner: 9–12. Commission-based: 9–12. Seasonal: 10–12+. |
| 2 | **Dependency Load** | Ask about *nature* of dependents, not just count. Aging parents with no health insurance is a materially different risk than a working adult sibling. |
| 3 | **Insurance Coverage Quality** | Adequate health (₹10L+ cover) + term: can reduce target by 1–2 months. No health insurance: add ₹2–5L buffer. No term (with dependents): critical flag — EF cannot substitute. |
| 4 | **Fixed Obligations** | Rent/EMI, school fees, insurance premiums, SIP commitments. High fixed-to-variable ratio = larger fund required. |
| 5 | **City Tier & Cost of Living** | Metro (₹60K–1.2L/month family essential spend) → Tier 2 (₹25–50K) → Tier 3/semi-urban (₹15–35K). |
| 6 | **Age & Career Stage** | 22–27: 3–4 months (high re-employability). 28–35: 6–8 months. 35–45 (peak responsibility): 8–12 months. 45–55: 6–12 months (re-employment risk rises). 55+: 12–24 months runway framing. |
| 7 | **Household Income Sources** | Dual-income salaried = lowest risk. Single income + variable = highest risk. |
| 8 | **Health Profile & Pre-Existing Conditions** | Chronic conditions (diabetes, hypertension), family history of critical illness, physically demanding occupation → increase buffer. |

**Illustrative output of dimension interplay:**
> "Your target is ₹4.2L (7 months) because: you're a single earner (↑), have 2 dependents including an uninsured parent (↑), but have stable government employment (↓) and good personal health insurance (↓)."

---

## 3. India-Specific Structural Realities

These factors fundamentally differentiate Indian EF advisory from Western frameworks — they are non-negotiable inputs to the advisory engine.

| Reality | Key Data | Product Implication |
|:---|:---|:---|
| **Medical bankruptcy risk** | 39.4% of health expenditure is out-of-pocket [NHA 2021–22]. ~17% of households face catastrophic health spending annually [as of 2024]. Medical inflation: ~14%/year [as of April 2026]. | EF assessment *must* ask about health insurance status. Medical emergency is the #1 threat to EF adequacy. Flag EF-12. |
| **Joint family dynamics** | ~60–67% of income may go to "needs" in joint households vs. standard 50% assumption. Intergenerational financial obligations are cultural norm. | Ask about parents' health, insurance, whether they are financial dependents — not just "number of dependents." Consider a separate, labeled "Parent Care Fund" sub-account. |
| **Informal economy** | ~87% of India's 634M workers are informally employed [NITI Aayog, 2025]. 10M+ gig workers [as of 2025], growing to 23.5M by 2029–30. No employer benefits, no severance. | Freelancers and gig workers should default to 8–12 month targets. Income gaps between projects/gigs can be multi-week. EF-13 tracks whether our gig-economy sizing is calibrated correctly. |
| **Inflation erosion** | CPI: ~3.4% [March 2026]; medical: ~14%; education: 8–12%; rent (metros): 5–8%. | EF target must be recalibrated annually. App should auto-suggest inflation-adjusted upward revisions. |
| **Social & cultural obligations** | Weddings, ceremonies, community expectations can consume 1–3 months of income. Unpredictable timing. | **✅ Decided:** Include as an optional "Social Obligation Buffer" sub-fund in V1. Surfaced post-target-reveal as an add-on step — not part of the core EF assessment flow. Does not inflate the core EF target. |

---

## 4. User Archetype Mapping

### Research Archetypes vs. CarrotFin User Segments

The research identifies 9 archetypes. Mapped against existing CarrotFin user segments (from `user-insights.md`):

| Research Archetype | Recommended Range | Maps To CarrotFin Segment | Gap? |
|:---|:---|:---|:---|
| **Metro Single Professional** (25–30, salaried ₹8–15L, no dependents, employer health cover) | 3–5 months | S1: Young professionals ✅ | None — strong match |
| **Young Married (Dual Income)** (28–35, home loan EMI, no kids) | 4–6 months | S2: Young families ✅ | Partial — S2 also covers new parents |
| **New Parent** (30–38, infant/toddler, parents partially dependent) | 6–9 months | S2: Young families ✅ | Partial — worth splitting S2 into pre-kids / post-kids |
| **Sandwich Generation** (35–45, children in school, aging parents, single earner) | 9–12 months | S4: Mid-career optimizers 🔴 (deferred) | **Gap:** This archetype has highest EF urgency but is in a deferred segment — reconsider |
| **Freelancer / Creator** (25–40, variable income) | 8–12 months | S5: Gig workers/freelancers 🔴 (deferred) | **Gap:** High EF urgency, but segment is deferred. EF may be the forcing function to serve them. |
| **Small Business Owner** (30–50, variable business income) | 9–12 months | — | **New gap:** Not in existing segment model, but represents significant EF urgency |
| **Gig Worker** (20–35, app-based income) | 6–10 months | S5: Gig workers/freelancers 🔴 (deferred) | Same gap as Freelancer |
| **Pre-Retiree** (50–60, corpus protection) | 12–18 months | — | **New gap:** Not in segment model; different EF framing (runway, not income replacement) |
| **Tier 2/3 Joint Family** (25–40, lower costs, family support) | 4–8 months | — | **New gap:** Not in segment model; lower income, different risk calculus |

### Archetype Gaps Identified

Three new user segments emerge from the EF research that are absent from the current KB:
1. **Small Business Owner** — variable income, business + personal risk conflation
2. **Pre-Retiree** — EF as corpus protection buffer, not income replacement
3. **Tier 2/3 Joint Family** — lower absolute costs, informal family safety net as partial buffer

> These gaps are flagged to `user-insights.md` for update. See the archetype additions section below.

---

## 5. Three-Layer Liquidity Architecture

**Product recommendation:** Allocate across three liquidity layers, not a single instrument.

| Layer | Coverage | Vehicle | Access Time | Returns (indicative) |
|:---|:---|:---|:---|:---|
| **Layer 1: Instant Access** | 1–2 months | High-interest savings / sweep-in FD | Instant (ATM/UPI/24×7) | 3–4% (savings) / 6–7% (sweep-in) |
| **Layer 2: Quick Access** | 2–4 months | Liquid mutual funds | T+1 (some: instant up to ₹50K) | ~6–7% [as of April 2026] |
| **Layer 3: Stability Reserve** | Remaining months | Short-term FD / ultra-short debt / arbitrage funds | 1–3 business days | 6–8% [as of April 2026] |

**Adaptive allocation rule:** App should recommend layer split based on income pattern. Freelancers: weight toward Layer 1 (income is lumpy). Dual-income salaried: weight toward Layer 2/3 (natural income hedge).

**DICGC caveat:** FD insurance covers only ₹5L per depositor per bank — advise diversification if total corpus exceeds this threshold.

> All specific rates are indicative and dated [April 2026]. Do not hardcode; pull from live data layer.

### Illustrative Allocation (9-Month Fund, ₹4.5L)

| Layer | Amount | Vehicle | Access |
|:---|:---|:---|:---|
| Layer 1 | ₹1.0L (2 months) | Sweep-in FD | Instant |
| Layer 2 | ₹2.0L (4 months) | Liquid MF | T+1 |
| Layer 3 | ₹1.5L (3 months) | Short-term FD | 1–3 days |

---

## 6. Behavioral Design Principles for Product

These govern how the EF experience should be designed — not just what it calculates.

| Principle | Description | Product Manifestation |
|:---|:---|:---|
| **Separate & Sacred** | EF must be in a separate account/instrument from daily spending | Friction-by-design for withdrawals; "Are you sure this is an emergency?" confirmation |
| **Start Small, Build Momentum** | ₹10L target feels impossible. Show a "Starter Shield" (₹25K–50K or 1 month) first. Celebrate each tier hit. | Progressive milestone model. Don't show full target upfront to low-income users. |
| **Mental Accounting Works** | Labeled money is 2–3× less likely to be spent impulsively than fungible savings | Named sub-funds: "Medical Shield," "Job Buffer," "Parent Care Fund" |
| **Automate Like an EMI** | Recurring, automatic, non-negotiable. "You pay yourself first." | SIP-like auto-debit setup. Frame as a "self-EMI" not as savings. |
| **Reframe as Protection** | Don't say "money you can't touch." Say "money that protects everything else." | Show the cost of NOT having a fund: credit-card spiral illustration, SIP-stoppage impact |
| **Replenishment Imperative** | After withdrawal, rebuild is #1 priority | Auto-trigger "Rebuild Mode" immediately after any withdrawal |
| **Celebrate Interim Milestones** | 25%, 50%, "1 Month Protected," "3 Months Secure" | Each milestone shows what the user can now survive: "A 2-week income gap won't derail you." |

---

## 7. Common Myths — In-App Busting

App should surface these contextually, not as a lecture dump:

| Myth | Reality | When to Surface |
|:---|:---|:---|
| "3 months is enough" | Depends on 8 dimensions. Dangerously low for freelancers/single-income with dependents. | At target-reveal, show why their number differs from "the usual advice" |
| "I'll use my credit card" | 36–42% APR. ₹2L emergency → ₹3L+ within a year. | During assessment if no fund exists |
| "My investments can be liquidated" | Selling equity in a crash = crystallizing losses. SIP stoppage destroys compounding. | When user mentions MF/stocks as substitute |
| "My family will help" | Informal safety nets declining with urbanization. Should be a bonus, not a plan. | Contextual, if user indicates joint family |
| "I'll start when I earn more" | Lifestyle inflation. Start with ₹500/month — the habit matters more than the amount. | For low-income/early-career users daunted by the target |
| "More is always better" | Over-saving in low-yield instruments has opportunity cost. Once funded, redirect to wealth creation. | When fund is ≥100% of target |

---

## 8. Dynamic Recalibration Triggers

The EF target is not static. App-level events should prompt re-assessment:

**Life events:** Marriage, birth of child, divorce, parent becoming dependent, child starting school/college.

**Financial events:** Salary hike (>15%), job change, new EMI, loss of employer health insurance, going freelance, windfall (bonus, inheritance).

**External events:** Significant inflation shift (>2% annual change), industry-wide layoffs in user's sector, pandemic/natural disaster.

> **Product implication:** Maintain a "Life Events Tracker" in the user profile. Passive recalibration (annual inflation adjustment) should happen automatically without user action.

---

## 9. What This Research Does NOT Determine

Intentionally left out — requires separate, dynamic data layers or legal review:
- Specific product names or AMC recommendations (change frequently)
- Tax computation details (subject to Finance Act updates)
- Exact interest/return rates (volatile; pull from live data)
- Regulatory compliance for fund distribution (SEBI, RBI, AMFI — requires legal review)
- UI/UX wireframing (covered in Phase 2: Journey Design)

---

## 10. Resolved Scoping Decisions

> **Decided:** 2026-04-16 | These are locked founder decisions. Phase 1 journey design must treat these as fixed constraints, not open questions.

| # | Decision | Resolution | Engineering Implication |
|:---|:---|:---|:---|
| **D1** | **V1 archetype target** | The assessment conversation is *designed* around two primary personas: **New Parent** (30–38, infant/toddler, partially dependent parents) and **Sandwich Generation** (35–45, children in school, aging parents, single earner or variable income). These two represent the highest EF urgency and the widest spread of complexity. The adaptive engine handles all 9 archetypes — these two anchor the UX design work. | Journey design (Phase 2) must walk through both archetypes end-to-end. Component specs (Phase 3) must cover information density for both. |
| **D2** | **Onboarding integration** | EF setup **is** CarrotFin's first-launch onboarding. The 8-dimension assessment IS the user profiling flow. The app should be built as if it has many features — other goal types (retirement, education, wealth building) exist as visible but dummy/placeholder entries in the navigation. Users see where the product is going; they can only interact with EF for now. | App shell must include bottom nav or home surface with EF as primary active feature + 2–3 placeholder goal cards (greyed out, "Coming soon"). No dead ends — users see the full product vision from day one. |
| **D3** | **Social obligation buffer** | **Include in V1** as an optional post-assessment step — not part of the core EF sizing flow. After the primary target is set and revealed, the app asks: "Many Indian families also set aside a small buffer for family obligations like weddings or ceremonies. Want to add one?" User can skip. This keeps the core flow clean while acknowledging Indian financial reality. | Implement as a distinct, skippable module after "Why This Number" reveal. Sub-fund is labeled separately (e.g., "Family Buffer"). Does not affect the core EF target calculation. |
| **D4** | **Advisory depth / fund execution model** | **Advisory-only, instrument-type level.** CarrotFin recommends *how to allocate* across instrument types (savings/sweep-in FD, liquid MF, short-term FD/debt fund) but does **not** recommend specific AMCs, fund names, or bank products. User executes externally. This is the advisory ceiling for V1 — no embedded transactions, no product-level recommendations, no SEBI/RBI distribution licensing required. | Advisory output is: "Put ₹1L in a sweep-in FD for instant access, ₹2L in liquid mutual funds, ₹1.5L in a short-term FD or ultra-short debt fund." No fund name, no AMC, no bank named. User directs execution themselves or via their existing platforms. |

---

## Sources & References

| Source | Data Point | Year | Tier |
|:---|:---|:---|:---|
| NITI Aayog | Gig economy projections (23.5M by 2029–30), informal sector (87%) | 2022, 2025 | Tier 1 |
| National Health Accounts (NHA) | Out-of-pocket expenditure (39.4%) | 2021–22 | Tier 1 |
| MOSPI / CPI Data | Headline CPI 3.4%, food 3.87% | March 2026 | Tier 1 |
| Tribune India / Survey Data | Households spending 12.2% of income on medical costs | 2025 | Tier 2 |
| NIH / ORF Research | Catastrophic health expenditure (~17% of households) | 2024 | Tier 1 |
| DICGC | Deposit insurance limit ₹5L per depositor per bank | Current | Tier 1 |
| Economic Times, LiveMint | Emergency fund best practices, instrument comparison | 2024–2026 | Tier 3 |
| ClearTax, Groww | Tax efficiency: liquid funds vs FDs | 2025 | Tier 3 |

---

*This is the workspace-canonical version of the emergency fund research. The raw source document is preserved at its original path. Update this file if macro conditions shift, new data points emerge, or Phase 1 scoping decisions invalidate any sections. Provenance: Conversation 3e343af4 → `emergency_fund_research.md`.*
