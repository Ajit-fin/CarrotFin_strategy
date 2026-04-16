# J01: Emergency Fund Setup

> **Workflow:** `/product` — Phase 1: Product Scoping & Flow Definition
> **Last updated:** 2026-04-16
> **Status:** Phase 1 complete — scope locked, journey phases defined. Phase 2 (interaction design) is next.
> **Trigger:** First-launch onboarding. New user opens CarrotFin for the first time.
> **User segments:** All — EF setup IS onboarding. Primary design anchors: New Parent (30–38) and Sandwich Generation (35–45). The Adaptive Engine (see §0 below) handles all 9 archetypes at runtime.
> **Assumptions depended on:** EF-11 (8 core sizing dimensions + 1 open/contextual dimension — see §2), C5 (conversational+visual integration feasible), C6 (adaptive composition viable in Flutter)
> **Design decisions:** DD01 (EF as onboarding), DD02 (advisory-only model), DD03 (V1 scope boundary)
> **Source philosophy:** `research/market/emergency-fund-philosophy.md`

---

## 0. What Is the Adaptive Engine — and Where Does It Sit?

> **For Phase 2 onwards:** This section clarifies a term used throughout J01 so downstream design and engineering phases are aligned.

The **Adaptive Engine** is CarrotFin's runtime decision layer — the logic that determines what the AI says, what components are rendered, and how the experience adapts based on what it knows about the user at any given moment. It is **not a separate product or service to be built independently** — it is the product intelligence that lives inside the app.

In practical terms, it is:
- The **LLM inference layer** that drives conversational turns (what the AI asks next, how it frames answers, which beat to enter)
- The **composition rules engine** that decides which screen type (Generative / Hybrid / Static — per `screen-taxonomy.md`) and which components to render
- The **user state model** that tracks what we know about the user (literacy signal, trust level, engagement mode, archetype signals, life stage) and updates it in real time

**Where it sits in the product build journey:**

| Phase | What is built | Adaptive Engine involvement |
|:---|:---|:---|
| **Phase 1 (this doc)** | Product scope + flow definition | Defined in terms of what decisions the engine must make |
| **Phase 2 (UX Journey Design)** | Step-by-step interaction spec | Specifies adaptive variations per archetype — the engine's behavioral contract |
| **Phase 3 (Component Specs)** | Adaptive component specs | Defines the component palette the engine selects from and the composition rules |
| **Phase 4 (Stitch Briefs)** | Mockup generation | Visualises 2–3 archetype paths the engine would produce |
| **Phase 5 (BuildSpec)** | Engineering handoff | Translates the engine's decision logic into concrete implementation guidance for the Flutter dev |

> **Key point:** The adaptive engine is not a separate micro-service to spec and build in isolation. It is the sum of the LLM's conversational capability + the component palette + the composition rules. Phase 2 and Phase 3 together define its behavioral contract. Phase 5 translates it to implementation.

---

## 1. Product Boundaries

### Entry Points

| Entry Point | Trigger | User State at Entry | Notes |
|:---|:---|:---|:---|
| **First-launch onboarding** | App installed, first open | New, zero context, zero trust | Primary V1 entry. The app presents a multi-path starting point (see §0A below) — but only the EF path is active in V1. All other paths are visible placeholders. |
| **Home surface prompt** | Returning user, EF not set up | Low trust, some context exists | For users who skipped or abandoned onboarding mid-flow. |
| **Ambient nudge** | App detects no emergency fund data and user has been active for 7+ days | Warm trust, some context | Re-entry via notification: "You haven't set up your safety net yet." |

> The conversational stream (per `interaction-model.md`) is the entry modality for all paths — not a form, not a dashboard. The AI starts the conversation.

#### §0A — First-Run Experience: Multi-Path Shell, EF as the Only Active Path

When a user opens CarrotFin for the first time, they should not land directly into an EF-specific flow. Instead:

1. The app presents a **goal discovery surface** — the AI asks: *"What's on your mind financially?"* or *"What would you like to work on first?"* — with visible options like:
   - 🛡️ **Build my safety net** (Emergency Fund) ← *Active in V1*
   - 🎯 **Plan for a goal** (Retirement / Education / Buy a home) ← *Placeholder — visible, not interactive*
   - 📈 **Grow my wealth** ← *Placeholder*
   - 🧾 **Understand my finances** ← *Placeholder*

2. Only the **Emergency Fund path is live**. All other paths show a "Coming soon — we're building this" state when tapped — but their presence sets the product vision from day one.

3. If the user selects Emergency Fund (or if the AI recommends it as the most relevant starting point based on any contextual signals), the J01 EF setup flow begins.

4. This shell means onboarding is **conceptually goal-agnostic** — it is not permanently EF-only. Future goal types slot in without restructuring the first-run experience.

> **Engineering note (from DD01):** The app shell must support multi-goal navigation from day one. The EF path being the only active one is a V1 content constraint, not an architectural constraint.

### Exit Points

| Exit Point | User State at Exit | What "Done" Means |
|:---|:---|:---|
| **Contribution plan confirmed** | Target set, allocation advised, SIP-equivalent plan agreed | User knows their target, knows where to put the money, knows how much to set aside per month. Advisory complete. |
| **Monitoring state activated** | After contribution plan confirmation | App shifts to ambient monitoring — nudges are **contextual, personalised, and evolving** (not periodic check-ins). See §1A below. |
| **Partial exit (deferred)** | User completes assessment but defers contribution planning | Journey saves state. User is re-engaged via ambient layer. A partial EF profile is better than none. |
| **Full abandonment** | User exits before assessment | Minimal data saved. Re-entry prompt on next session. |

### Adjacent Flows (Connected, Not Yet Built in V1)

| Adjacent Flow | Connection Point | V1 Status |
|:---|:---|:---|
| **Insurance Gap Check** | After 8-dimension assessment: if user has no health insurance, flag as a companion action | Advisory-only flag in V1. Full flow in a later phase. |
| **Salary Credit Nudge** | After contribution plan: "Your salary hit. Want to move ₹X to your EF?" | Future ambient trigger. Not in V1 scope. |
| **Goal Tracker** | EF appears as Goal #1 in the goal list on the home surface | Navigation scaffold exists in V1 (D2). |
| **Recalibration Flow** | Life event detected → EF target adjusted | Recalibration trigger logic in V1 data model. Active UX in V2. |

#### §1A — Nudge Philosophy: Contextual, Personalised, Evolving

Nudges triggered after the monitoring state activates are **not** a scheduled ping cadence. They are event-driven, phase-aware, and must evolve as the user and their fund evolve.

| Nudge type | Trigger condition | Journey phase | Personalisation dimension |
|:---|:---|:---|:---|
| **Milestone celebration** | Fund hits 25%, 1 month, 3 months, etc. | Active build phase | Milestone label + what they can now survive |
| **Momentum nudge** | Auto-transfer scheduled but no growth detected for 30+ days | Active build phase | Specific month amount, next milestone gap |
| **Recalibration signal** | Life event detected (salary change, new dependent, job change) | Any phase | Dimension that changed + new target estimate |
| **Inactivity re-engagement** | User hasn't opened app in 14+ days | Any phase | Last milestone reached + personalised hook |
| **Overshoot alert** | Fund ≥ 100% of target | Funded state | Redirect to wealth creation — this money is now over-working |
| **Risk environment signal** | Macro signal (e.g., sector layoffs in user's industry) | Any phase | Framed to user's employment type and current fund coverage |

**Key principle:** Nudges should feel like they come from a thoughtful advisor who knows the user's situation — not a notification system. Tone, framing, and timing must adapt to the user's current emotional state (per `behavioral-framework.md`) and journey phase. A user who just lost their job gets a different signal than one who just got a raise.

---

## 2. High-Level Journey Phases

The EF user journey is structured into 8 phases. **V1 covers Phases 1–5.** Phases 6–8 are deferred to V2.

### Phase Map

```
V1 ────────────────────────────────────────────────┐
                                                    │
  [1] Discovery  →  [2] Assessment  →  [3] Target   │
                                          Setting   │
                                             ↓      │
  [5] Contribution  ←─────────────  [4] Fund        │
      Planning                          Architecture│
                                             ↓      │
                                     ✅ V1 EXIT:    │
                              Plan confirmed,        │
                              monitoring begins      │
────────────────────────────────────────────────────┘

V2 ────────────────────────────────────────────────┐
  [6] Monitoring & Nudging                          │
  [7] Crisis Mode (Withdraw + Rebuild)              │
  [8] Recalibration (Life event → target update)   │
────────────────────────────────────────────────────┘
```

---

### Phase 1: Discovery / Education

> [!IMPORTANT]
> **All journey phases described below are one possible scenario — not a rigid script.** The GenAI layer must adapt in real time to what the user actually says and does. The phase descriptions capture the design intent and a representative happy path, not a decision tree the AI must execute literally. Phase 2 (UX Journey Design) defines the adaptive variation matrix; Phase 5 (BuildSpec) defines the engineering contract for how real-time adaptation is implemented in Flutter.
>
> Specific implications:
> - The AI may collapse or skip phases if the user is clearly already past that point (e.g., a user who says "I already have some savings but I'm not sure if it's enough" enters Phase 2 directly).
> - The AI may revisit a phase if the user contradicts an earlier answer or asks to adjust.
> - The sequence within each beat is a guide; the actual conversational path is driven by the user's responses, not a fixed script.

**User question:** "Do I actually need an emergency fund? I thought my savings/investments cover me."

**AI's job:** Establish relevance and urgency without fear-mongering. Use loss framing (cost of NOT having a fund), not abstract definitions.

**Modality:** Conversational stream leads. AI opens the conversation — no form, no dashboard.

**Screen type:** Generative (conversational stream — per `screen-taxonomy.md`)

**What happens:**
1. AI opens with a direct, opinionated question — not a welcome screen. (Scenario A from `interaction-model.md` — "Let's figure out one thing: do you have an emergency fund?")
2. User selects from inline structured options: "Yes, I have one" / "Sort of" / "Not really" / "Not sure"
3. AI responds adaptively to each:
   - "Yes": "Great. Let me check if it's sized right for your situation — takes 2 minutes."
   - "Sort of" / "Not really" / "Not sure": Surfaces the cost of the gap — personalizable once income is known.
4. **Emergency framing** is surfaced here: the What Qualifies / What Doesn't table (§1 of philosophy) is rendered as an inline quick-sort card — NOT a lecture. "Quick check: which of these have happened to you or someone close in the last 2 years?" → surfaces relevance personally.
5. Myth busting surfaces contextually: if user says "I'll use my credit card," the 36–42% APR cost is shown immediately.

**Trust level at entry:** Zero/New. The AI earns the right to ask for data by delivering value first.

**V1 Adaptive variation note:** Literacy calibration starts here. User's language in their first message (if any free text) + response speed + choices made calibrate initial literacy signal.

---

### Phase 2: Assessment

**User question:** "Okay, I need one. How much do I actually need?"

**AI's job:** Walk through the 8 sizing dimensions in a conversational, progressive way — NOT a form. Each question delivers value immediately after. The AI reveals the picture as it builds.

**Modality:** Conversational stream with inline structured inputs. A live "Building Your Picture" progress indicator sits above the stream — shows dimensions covered without numericizing them.

**Screen type:** Hybrid (fixed progress indicator + generative conversation stream below)

**The 8-dimension conversation design:**

The AI does NOT ask 8 sequential questions. It groups them into 3 smart conversation beats. Each beat also leaves space for a **9th open dimension** — factors the user volunteers that don't fit neatly into the 8 (see §D9 below).

> **Beat 1 — Income Profile** (covers Dimensions 1, 7)
> "What's your primary income source — salaried job, freelance/contract, or running your own business?"
> → Adaptive follow-up: If salaried → "Government, large private company, or startup?" | If freelance → "Fairly regular projects, or months where income is lumpy?"
> → Value after: "Got it. Based on [employment type], most people in similar situations need [X–Y] months as a base. Let's see how your other factors adjust this."

> **Beat 2 — Responsibility & Protection** (covers Dimensions 2, 3, 4)
> "Who depends on your income? Just yourself, or do you have a family / parents relying on you?"
> → Adaptive follow-up on dependents: Age of parents (insured or not?), children (school-age?), single earner or dual income household?
> → "And do you have health insurance? Roughly what cover — under ₹5L, ₹5–10L, or ₹10L+?"
> → "Ballpark: how much of your monthly income goes to non-negotiables — rent/EMI, school fees, insurance premiums, loan EMIs?"
> → Value after: Shows a preliminary "Your Safety Net So Far" number that adjusts visually as each answer comes in.

> **Beat 3 — Context & Risk** (covers Dimensions 5, 6, 8)
> "Which city are you in?" (or tier selection: Metro / Tier 2 / Tier 3)
> → "And your age, roughly? (20s / 30s / 40s / 50s+)"
> → Health profile: "Any ongoing health conditions in the family that could mean higher medical costs?" (Yes/No/Prefer not to say — respects privacy)
> → Value after: Final dimension set resolved. "Here's what all of this adds up to…" → bridges to Phase 3.

> **Beat 4 — Open Dimension (D9: User-Surfaced Context)** *(triggered by user behaviour, not a fixed beat)*
> If at any point in Beats 1–3 the user volunteers information that doesn't map to the 8 dimensions — for example: *"I also support a sibling in another city"* / *"We have a legal matter pending"* / *"I run a small side business"* — the AI acknowledges it, asks a clarifying follow-up, and incorporates it into the target as a contextual adjustment. The attribution strip label reads: **"+ [N] months (your specific situation)"** — honest about what it is without forcing it into an ill-fitting dimension.
> If the user proactively asks *"What about X?"* where X isn't covered, the AI explains whether it's already captured in another dimension or adds it as a contextual factor.

#### §D9 — The Ninth Dimension: Open / Contextual

The 8-dimension model is the baseline. Real users will surface factors outside it. The AI must:
1. **Not ignore them.** Dismissing user-surfaced context breaks trust.
2. **Not over-engineer a response.** Use a conservative adjustment and flag it — no new form field needed.
3. **Label it honestly** in the attribution: *"+ 1 month (based on your specific situation)"* — not buried in the base calculation.
4. **Track it.** User-surfaced D9 factors in the first cohort inform whether any should be promoted to a permanent dimension.

> **Assumption EF-11B:** The 8-dimension model covers the majority of Indian users' sizing factors. D9 is the safety valve for the remainder. Track D9 invocation rate — if >20% of users surface a D9 factor of the same type, promote it to a permanent dimension in the next iteration.

**Trust gate:** Beat 2 asks for income-adjacent data (fixed obligations). Per behavioral framework trust architecture — this is asked only after Beat 1 delivers value. The AI earns permission to go deeper.

**Data sparsity handling:**
- If user declines to answer any dimension: AI uses the conservative side of the range for that dimension and flags it. ("I'll use a cautious estimate here — you can refine this anytime.")
- If user says "I'm not sure" on health insurance: AI defaults to "no adequate coverage" and adds the buffer — explains the logic.

**Insurance flag:** Per philosophy §3 — if user has no health insurance, the AI flags this explicitly: "Before we finalize your EF target, one thing: having no health insurance significantly increases your emergency risk. I'll flag this separately — but I'd strongly suggest looking into at least a ₹5L cover in parallel." This is an advisory flag, not a blocker.

---

### Phase 3: Target Setting ("Why This Number")

**User question:** "What's my number? And why?"

**AI's job:** Reveal the personalized target with full dimension attribution. Make the number feel inevitable and personal — not arbitrary.

**Modality:** Conversation → Visual pivot ( per `interaction-model.md`, "Decide" modality). Stream sets up the reveal. Visual component materializes inline with the number.

**Screen type:** Hybrid (conversational reveal + inline target card with dimension attribution)

**The reveal sequence:**
1. AI delivers conversational setup: "Here's what all of that adds up to."
2. **Personalized Target Card** materializes inline:
   - Headline: "Your emergency fund target: ₹[X]L ([N] months)"
   - Attribution strip below the number: dimension-by-dimension arrows showing what increased/decreased the base estimate:
     > "Started at 6 months (salaried baseline) → +2 months (single earner with dependents) → +1 month (parent without health insurance) → −1 month (stable large employer) → **Final: 8 months**"
   - "Was this calculation right?" / "Adjust an answer" — escape hatch.
3. **Myth busting surfaces here** if the number differs materially from "the usual advice" (§7 of philosophy): "This is higher than the '3-6 months' you may have heard — here's why that rule doesn't fit your situation."
4. AI delivers the plain-English "Why This Number" explanation with one key insight highlighted.

**Social Obligation Buffer (D3 — V1 optional step):**
After the primary target is revealed:
> "One more thing — many Indian families also set aside a small separate buffer for family obligations: a wedding in the family, a ceremony, community support. This keeps your EF intact for true emergencies. Want to add one?"
> → **Yes:** Simple input: "How many months of obligations typically hit in a year?" → adds a clearly labeled "Family Buffer" sub-fund target.
> → **Skip:** Core EF target unchanged.

**"Starter Shield" framing for large targets:**
If the revealed target is ≥ ₹5L AND the user appears to be early-career or low-income (signal from Beat 1):
> AI offers: "That's a big goal. The real win is getting to your first month — your 'Starter Shield' of ₹[monthly_expense]. Once you hit that, your most acute risk drops significantly. Let's start there."
→ Milestone 1 = 1 month. Full target is the horizon, not the first step.

---

### Phase 4: Fund Architecture ("Where Should the Money Sit?")

**User question:** "Okay, I know the target. But where do I actually put this money?"

**AI's job:** Recommend the 3-layer allocation split, adapted to income pattern. Explain why this structure — without naming specific products.

**Modality:** Conversation → Visual pivot to composed surface. The 3-layer allocation chart is the dominant visual. Stream provides conversational explanatory layer.

**Screen type:** Hybrid (fixed 3-column/layer structure + AI-generated amounts and explanations per layer)

**What the AI recommends (per philosophy §5):**
- Layer 1: Instant Access — "sweep-in FD or savings account with instant UPI/ATM access" → amount = 1–2 months essential spend
- Layer 2: Quick Access — "liquid mutual fund (many allow instant redemption up to ₹50K, rest T+1)" → amount = 2–4 months
- Layer 3: Stability Reserve — "short-term FD or ultra-short debt fund" → remaining months

**Adaptive income-pattern rule (from philosophy):**
- Variable income (freelancer/gig): Weight toward Layer 1 — income is lumpy, need more instant access.
- Dual-income salaried: Weight toward Layer 2/3 — income regularity = natural hedge.

**DICGC note:** If total target exceeds ₹5L, AI surfaces: "If you're putting a significant amount into FDs, keep it spread across 2+ banks — deposit insurance only covers ₹5L per depositor per bank."

**Advisory ceiling (D4):** No specific AMC, fund name, or bank product named. Instrument type only.

---

### Phase 5: Contribution Planning ("How Do I Get There?")

**User question:** "How do I actually build this over time?"

**AI's job:** Translate the target into a monthly contribution plan. Frame it as a "self-EMI" — automatic, non-negotiable. Set up the milestone architecture.

**Modality:** Conversational stream → Contribution Plan Card (inline visual). Then a clear CTA to externally set up the auto-debit.

**Screen type:** Hybrid (conversational setup + fixed Contribution Plan Card + single CTA)

**Contribution calculation logic:**
- Ask: "What's a realistic amount you can set aside each month right now?"
- AI computes: At ₹[user_input]/month, you'd reach your full target in [Y] months. At ₹[20% more], you'd get there in [Y - 2] months.
- AI recommends: The faster path — with data showing the "gap months" cost (what your risk exposure is if an emergency hits before you're funded).
- Frame: "Think of this like an EMI you pay yourself. Set up an auto-transfer on the day your salary arrives — before you can spend it."

**Milestone architecture (per §6 behavioral principles):**
| Milestone | Label | What It Means |
|:---|:---|:---|
| 25% fund | "Starter Shield" | "A 2-week income gap won't derail you" |
| 1 Month | "First Month Protected" | "A full month of expenses is safe" |
| 3 Months | "3 Months Secure" | "Most job losses are covered" |
| 6 Months | "Half-Year Shield" | "You can weather most household crises" |
| 100% | "Fully Funded" | "Redirect to wealth creation — this job is done" |

**Advisory output (D4):** The plan tells the user: "Set up an auto-transfer of ₹X on your salary date to a [sweep-in FD / savings account]. Do the same for ₹Y to a liquid mutual fund." No specific platform or product named.

**Exit confirmation:** "Your plan is set. I'll check in occasionally to see how it's growing. Whenever you hit a milestone, we'll celebrate." → Monitoring state begins.

---

## 3. V1 Scope Boundary

### In V1

| Feature | Phase | Notes |
|:---|:---|:---|
| Conversational onboarding (EF as first-run) | Phase 1–5 | D2: EF is CarrotFin's onboarding |
| 8-dimension assessment | Phase 2 | Adaptive, not a form |
| Personalized target with attribution | Phase 3 | "Why This Number" reveal |
| Social obligation buffer (optional) | Phase 3 | D3: Post-target, skippable |
| 3-layer allocation recommendation | Phase 4 | Instrument type only (D4) |
| Contribution plan (month, milestone) | Phase 5 | Advisory only. External execution. |
| Insurance gap flag | Phase 2 | Advisory flag, no dedicated flow |
| App shell with EF + placeholder goals | Navigation | D2: Home + 2–3 greyed goal cards |

### Deferred to V2

| Feature | Reason |
|:---|:---|
| Monitoring & nudging surface | Requires contribution data over time — can't exist at first launch |
| Crisis withdrawal flow + rebuild mode | High complexity; better designed after real usage is observed |
| Recalibration on life event (active UX) | Trigger logic in V1 data model. Active UX flow in V2. |
| Insurance gap dedicated journey | Separate product feature — significant scope. |
| Transaction execution (open account, SIP setup) | Advisory-only V1. No licensing required. |
| Archetype shortcut ("You seem like a Metro Professional") | Risk of oversimplification at first launch. Full assessment builds better initial profile. |

---

## 4. Success Metrics

### Primary

| Metric | Target (indicative) | What it signals |
|:---|:---|:---|
| Assessment completion rate | >60% of users who start Beat 1 complete all 3 beats | The conversational assessment doesn't feel like a form |
| Target reveal engagement | >70% of users who see the target card tap "Why this number?" | Attribution transparency builds curiosity, not anxiety |
| Contribution plan confirmation | >40% of users who see the plan confirm a monthly amount | The plan is realistic and actionable, not overwhelming |

### Secondary

| Metric | What it signals |
|:---|:---|
| Data sparsity rate per dimension | Which dimensions users most resist sharing — signals trust gaps |
| Social obligation buffer add rate | How India-resonant this optional step is |
| Milestone 1 hit rate (Starter Shield) | Whether users actually start building after setting the plan |
| Assessment restart rate | How often users go back to "adjust an answer" — signals dissatisfaction or desire for precision |

### Behavioral signals that the design is correct

- Users spontaneously reference their EF target in later sessions ("Is my fund enough if X happens?")
- Users return to the monitoring surface without being nudged
- Low myth-bust skip rate (users actually engage with the myth busting content)
- Assessment abandonment happens late in the flow (Beat 3), not early (Beat 1) — signals value is delivered early enough to keep users engaged

---

## 5. Trust Level & Data at Entry

Per the Behavioral Framework trust architecture, the EF onboarding starts at **Trust Level: New**.

| Data point | Beat | Sensitivity | Handling if withheld |
|:---|:---|:---|:---|
| Employment type | Beat 1 | Low | Ask — essential for base estimate |
| Employer stability tier | Beat 1 | Low | Offer a dropdown (no typing needed) |
| Number of dependents | Beat 2 | Medium | Use conservative range if skipped |
| Dependent health insurance status | Beat 2 | Medium | Default to "uninsured" — cautious |
| Monthly fixed obligations (rough) | Beat 2 | Medium-High | Offer a slider or range picker, not a text field |
| City / tier | Beat 3 | Low | Default to Tier 2 if skipped |
| Age bracket | Beat 3 | Low | Offer 4 brackets — not an exact year |
| Health conditions | Beat 3 | High | Fully optional. "Prefer not to say" is a valid answer. |
| **Monthly income (rough range)** | Beat 2 | Medium | Ask with a range picker — not a blank text field. If withheld, infer from city tier + employment type. |
| **D9 — Open / contextual factor** | Any beat | Variable | Accept and incorporate if user volunteers it. |

**Design rule — Data collection philosophy (applies to all fields, not just income):**

Users are generally willing to share information when asked sensitively and when they can see why it matters. The default posture is **ask, with care** — not avoid-and-infer.

- **Ask directly, but make it low-friction.** Use range pickers, bracket selectors, and sliders — not blank text fields for sensitive data. *"Roughly what's your monthly income? (Pick a range)"* is fine. *"Enter your exact annual CTC"* is not.
- **Explain the why, immediately.** Before asking for sensitive data, the AI states the purpose: *"To figure out how much you'd need, I need to know roughly what your monthly expenses are — your income level helps me estimate that."* Justification earns permission.
- **Graceful degradation if withheld.** If a data point is skipped, the AI uses a conservative estimate and tells the user: *"I'll use a cautious estimate here — you can refine this anytime."* The flow never stalls.
- **No permanent locks.** Any data point can be revised later. The assessment is a live profile, not a one-time form.
- **Universally applicable.** This sensitivity-first, ask-directly approach is the CarrotFin-wide data collection philosophy — not specific to income or any single field.

---

## 6. App Shell Requirement (from D2)

The app shell (navigation scaffold) must be in place before this journey ships:

| Element | V1 Requirement |
|:---|:---|
| Bottom navigation | Home / Goals / Chat / Profile (4 tabs minimum) |
| Home surface | EF as primary active feature card (progress bar, status) + 2–3 goal cards greyed out ("Coming soon" or goal type labels visible) |
| EF goal card | Shows fund health status, next milestone, quick-access shortcut back into conversation |
| Placeholder goal cards | Visible but non-interactive. Examples: "Retirement Corpus," "Child's Education," "Wealth Builder." Labels set the product vision. |

---

## 7. Open Questions for Phase 2 (UX Journey Design)

These are scoped questions — scope is locked, but the UX design of each phase will need to resolve these:

1. **Assessment modality:** Does the AI conduct the assessment purely as a stream, or does one of the beats use a dedicated composed surface (e.g., a dependency/protection map visual) before returning to the stream? (T2: Conversational vs. Visual Primacy)
2. **"Building Your Picture" progress indicator:** How exactly does the live number update? Animated number change? Progress bar filling? Meter-style? This is a key delight moment.
3. **Attribution visualization:** For the "Why This Number" reveal card — is the attribution a text list (simple), an arrow diagram (moderate), or a force graph / weighted bubble chart (rich)? Per literacy level: at least 2 designs needed. (T7: Verification Depth vs. Cognitive Load)
4. **3-layer allocation visual:** Bar chart (familiar), layered area chart, or a conceptual illustration (like stacked safe-deposit boxes)? The visual must communicate liquidity as a spectrum, not three separate buckets. (T1: Simplicity vs. Financial Rigor)
5. **Social obligation buffer UX:** The optional step after target reveal — how does it animate in? How does it maintain the "this is separate from your core EF" mental model visually?
6. **Contribution plan CTA:** What happens when the user "confirms the plan"? Do they get a step-by-step external instruction card? A shareable PDF? A calendar reminder? This is the highest-stakes CTA in the flow.

---

*This document is the Phase 1 artifact for J01. Phase 2 (UX Journey Design, `/ux-designer`) will expand each phase into detailed step-by-step interaction specifications, adding screen-level component inventories, conversational scenarios, and adaptive variations for New Parent and Sandwich Generation archetypes.*
