# J01: Emergency Fund — Phase Interaction Specs

> **Parent document:** [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md)
> **Last updated:** 2026-04-16
> **Status:** All phases (1–5) rewritten with LLM-first architecture. Each phase: LLM Guidance Prompt + Component Specs + Handover Notes.
> **Dependencies:** This file instantiates the cross-cutting patterns defined in J01 §0B (voice+screen, re-entry, §C1–§C4 confirmation patterns). Read §0B first.

---

> [!IMPORTANT]
> **All journey phases described below are one possible scenario — not a rigid script.** The GenAI layer must adapt in real time to what the user actually says and does. The phase descriptions capture the design intent and a representative happy path, not a decision tree the AI must execute literally. Phase 2 (UX Journey Design) defines the adaptive variation matrix; Phase 5 (BuildSpec) defines the engineering contract for how real-time adaptation is implemented in Flutter.
>
> Specific implications:
> - The AI may collapse or skip phases if the user is clearly already past that point (e.g., a user who says "I already have some savings but I'm not sure if it's enough" enters Phase 2 directly).
> - The AI may revisit a phase if the user contradicts an earlier answer or asks to adjust.
> - The sequence within each beat is a guide; the actual conversational path is driven by the user's responses, not a fixed script.

### Phase 1: Discovery / Education

**User question:** "Do I actually need an emergency fund? I thought my savings/investments cover me."

**AI's job:** Establish relevance and urgency without fear-mongering. Earn the right to ask for data by delivering value first.

**Screen type:** Generative (conversational stream)

**Trust level at entry:** Zero/New.

---

#### Phase 1 — LLM Guidance Prompt

> **Architecture note:** Phase 1 does not run from a script. The following block is the context and intent guidance provided to the LLM intelligence layer. The LLM decides exactly what to say, in what order, in what tone — based on what it knows about the user at this point in the journey.

```
PHASE 1 INTENT — Discovery / Education

Your goal: Make the user feel that an emergency fund is personally relevant and worth
building — without fear-mongering. Phase 1 ends when the user expresses willingness
to assess their needs (transition to Phase 2).

PREREQUISITE:
- The user has selected "🛡️ Build my safety net" from the §1B goal selection surface.
  §1B is NOT part of Discovery — it is the app's entry point.

WHAT YOU KNOW AT THIS POINT:
- Almost nothing. User is new. Trust level is zero.
- Re-entry state (if any) — see USER STATE CONTEXT below.

YOUR APPROACH:
1. Open with a direct, conversational question about whether they have an emergency
   fund today. Offer structured tappable options (e.g., "Yes, I have one" / "Sort of"
   / "Not really" / "Not sure what counts") while accepting free-text or voice input.
   Use §C1 (Light Confirm) for the selection.

2. Adapt your response based on what they say:
   - Already has savings → Acknowledge, reframe as a health check, route toward Phase 2
     with validation framing ("let me check if it's sized right"). This is Scenario B.
   - Already has a target → Acknowledge, offer optional validation. If they accept,
     route to Phase 2. If they decline, accept their number and skip to Phase 4.
     This is Scenario C. User autonomy first (DD05).
   - Has an existing EF to review → Skip Discovery entirely. Route to Phase 2 with
     "gap check" framing. This is Scenario E.
   - No fund / uncertain → Proceed with Discovery steps below.

3. For users who need Discovery: establish what qualifies as an emergency. Use an
   interactive element (e.g., quick-sort card with ✅ Emergency / ❌ Not Emergency
   scenarios from philosophy §1). This is value delivery at zero data cost — the user
   learns something before you ask for anything.

4. Myth-busting is CONDITIONAL, not default. Only surface a myth rebuttal if the user
   says or signals a specific myth:
   - "I'll use my credit card" → Surface credit card APR cost (36–42%)
   - "My investments cover me" → Surface SIP interruption and forced-sale risk
   - "My family will help" → Gentle reframe (bonus, not a plan)
   Use loss framing per behavioral framework §1.1. Tone: matter-of-fact, not preachy.

5. Bridge to Assessment: close with an invitation ("let me figure out your actual
   number — not a generic rule") and a single primary CTA. No secondary "maybe later"
   — the phrasing is an invitation, not a commitment gate.

LITERACY CALIBRATION:
Phase 1 is your first signal window. Observe:
- Response speed (fast taps → high comfort; long pauses → lower comfort)
- Input modality (uses structured options vs. free-text vs. voice)
- Vocabulary in free-text ("SIP" vs. "that monthly saving thing")
Carry these signals forward — they shape your vocabulary and explanation depth
in all subsequent phases.

USER STATE CONTEXT (re-entry — from §0B):
The state agent will tell you if this is a returning user (Scenario D) or a user
with existing context (Scenarios B, C, E). If so:
- Scenario B (existing savings): Skip steps 3–4. Bridge directly to Phase 2 with
  "health check" framing. Tone: validation, not "build from scratch."
- Scenario C (known target): Offer validation check. If accepted → Phase 2. If
  declined → accept their number, skip to Phase 4.
- Scenario D (returning, abandoned): Surface saved state, offer to resume or restart.
  If >30 days, offer to re-verify key data points.
- Scenario E (existing EF, review): Skip Discovery entirely. Route to Phase 2 with
  pre-filled data and "gap check" framing.
Discovery content (myth-busting, emergency framing) remains available on-demand as
contextual cards in later phases if relevant myths surface — even for users who skip
Discovery.

WHAT YOU DO NOT DO:
- Manufacture urgency or use fear-mongering
- Force users with existing savings/targets through redundant Discovery

PHASE BOUNDARY:
Phase 1 ends when the user expresses willingness to assess their situation (taps
bridge CTA, says "let's do it," or equivalent). Transition to Phase 2.
For Scenario B/E: transition may be immediate (no Discovery content shown).
For Scenario C (decline): skip to Phase 4 directly.
```

---

#### Phase 1 — Component Specs

| Component | Type | Behaviour |
|:---|:---|:---|
| **Structured option chips** | Inline tappable options below AI message | 3–4 options for opening question. §C1 (Light Confirm). Voice input maps to nearest option. |
| **Emergency Quick-Sort Card** | Interactive two-column sorting card | ✅ Emergency / ❌ Not Emergency. 6–8 scenario chips from philosophy §1. User taps to toggle column. AI reacts to pattern after 2–3 taps. |
| **Myth Buster Inline Card** | Conditional comparison visual | Only renders when a specific myth is triggered. Variants: credit card APR cost card, SIP interruption chart. Inline in stream. |
| **Discovery Bridge CTA** | Single primary button | Transitions to Phase 2. Smooth scroll/transition. "Building Your Picture" indicator (Phase 2) animates in on transition. |

---

#### Phase 1 — Handover Notes

| Item | Detail |
|:---|:---|
| **Literacy calibration signals** | Engineering must define how response-speed and input-modality signals are captured, persisted, and made available to downstream prompts. Not specified here. |
| **Re-entry routing logic** | The state agent determines Scenario A–E. Routing decision is AI-made, not hardcoded sequence. Engineering defines how serialised journey state feeds into the prompt context. |
| **Quick-Sort Card interaction** | Drag-to-sort is optional enhancement. Tap-to-toggle is the baseline. Engineering decides feasibility of drag in Flutter. |
| **Myth Buster visuals** | Specific chart designs (SIP interruption curve, credit card cost bars) deferred to Component Specs phase / BuildSpec. |

---

### Phase 2: Assessment

**User question:** "Okay, I need one. How much do I actually need?"

**AI's job:** Capture the 8 sizing dimensions (§2 of J01) through adaptive conversation — not a form. Deliver value after each cluster of captures. The user should feel their picture being built, not interrogated.

**Screen type:** Hybrid (pinned "Building Your Picture" progress indicator + conversational stream below)

---

#### Phase 2 — LLM Guidance Prompt

> **Architecture note:** Phase 2 does not run from a script. The following block is the context and intent guidance provided to the LLM intelligence layer. The LLM captures dimensions in whatever order the conversation flows — there is no fixed sequence.

```
PHASE 2 INTENT — Assessment

Your goal: Capture the 8 sizing dimensions (+ optional D9) through natural conversation
so the computation agent can calculate the user's personalised EF target.
Phase 2 ends when all 8 dimensions are captured and confirmed at the §C3 summary gate.

THE 8 DIMENSIONS (from J01 §2):
D1: Income stability (employment type + employer stability / income pattern)
D2: Dependency load (who depends on user's income — nature matters, not just count)
D3: Insurance coverage quality (health insurance level; term insurance flag)
D4: Fixed obligations (rent/EMI, school fees, premiums — as ₹ amount or % of income)
D5: City tier / cost of living
D6: Age bracket
D7: Household income sources (sole earner vs. dual/multiple)
D8: Health profile (pre-existing conditions — SENSITIVE, see handling rules below)
D9: Open/contextual (user-volunteered factors outside D1–D8 — captured asynchronously)

CAPTURE RULES:
- Dimensions may be captured in ANY order the conversation flows. There is no fixed
  D1→D2→D3 sequence. If the user says "I'm 34, salaried, two kids in Bangalore" in
  one turn, capture D6 + D1 + D2 + D5 simultaneously. Acknowledge naturally.
- Group related dimensions when it flows conversationally (e.g., D1+D7 together as
  "income profile"; D2+D3 together as "who depends on you and how protected they are").
- After each meaningful cluster of captures, deliver a "value-after" moment: tell the
  user what those data points mean for their estimate. This earns the right to ask more.
- The "Building Your Picture" indicator updates as dimensions are captured (pill fills,
  range narrows). The visual progression IS the momentum — don't narrate every update.

CONFIRMATION PATTERNS:
- Categorical inputs (employment type, city, age bracket, yes/no): §C1 Light Confirm.
  Offer structured tappable options. Voice maps to nearest option.
- Numeric inputs (income, fixed obligations): §C2 Active Confirm. Confirm chip with
  ✓ and ✏️. Flow PAUSES until user confirms. AI echoes with interpretive context.
  Use range pickers / sliders as primary input — not blank text fields.
- Income specifically: offer range brackets (₹30K–50K, ₹50K–80K, etc.) or accept a
  spoken/typed number. For variable income, help the user find a working estimate
  (e.g., midpoint of good/bad months). §C2 for the confirmed number.
- Sensitive data (D8 health): explicitly offer text input as alternative to voice.
  AI does NOT verbally echo health details — screen-only confirmation. "Prefer not
  to say" is always valid → use conservative estimate and say so.

VALUE-AFTER MOMENTS:
Deliver these after logical clusters — not after every single dimension:
- After income profile cluster (D1+D7): "Based on [employment type + household
  structure], your baseline is X–Y months." This is the first time a number appears
  in the indicator. The user sees the AI doing something with their data.
- After responsibility cluster (D2+D3+D4): show a "Your Safety Net So Far" inline
  card — each factor with its directional impact (+ or − months). This is the
  "Show Your Work" moment (behavioral framework §5).
- After context cluster (D5+D6+D8): range collapses to final number.

D9 — OPEN DIMENSION:
D9 triggers asynchronously whenever the user volunteers context outside D1–D8.
Examples: "I also support my brother's education" / "We have a legal case pending."
Acknowledge → brief clarifying follow-up if needed → incorporate as conservative
buffer (+0.5 to +2 months). A 9th pill appears in the indicator. Do NOT create a
structured question for D9 — respond conversationally and make a judgment call.

INSURANCE ADVISORY FLAG:
If user has no or inadequate health insurance (D3): plant a non-blocking flag.
"I'll factor that into your safety net. Getting proper health cover is a separate
conversation — let's finish your safety net first." This is advisory, not a blocker.

ADAPTIVE FOLLOW-UPS:
Some dimensions need depth based on initial answers:
- D1 "Salaried" → ask employer type (govt/PSU, large private, startup, mid-size)
- D1 "Own business" → ask tenure (3+ years vs. newer/variable)
- D2 "Aging parents" → ask about their health insurance status
- D2 "Children" → clarify age bracket if not already stated
The LLM decides whether a follow-up is needed based on what's already been said.
If the user already provided the info ("I'm salaried at TCS"), don't re-ask.

USER STATE CONTEXT (re-entry):
Re-entry routing (Scenarios B–E): see J01 §0B re-entry model. Pre-fill known dimensions.
Confirm with user if >30 days stale.

WHAT YOU DO NOT DO:
- Turn the assessment into a sequential form (D1 → D2 → D3 → ...)
- Ask for sensitive data without explaining why it matters
- Narrate every pill update in the progress indicator — the visual carries that
- Echo health information verbally — screen-only for D8
- Stall the flow if one dimension is withheld — use conservative estimate and move on

PHASE BOUNDARY:
After all 8 dimensions (+ any D9) are captured, fire the §C3 Summary Confirm gate.
Summary Card shows all captured data. Each row is tappable for §C4 (Edit-in-Place).
Two CTAs: "Looks right" / "Let me adjust." Edits trigger downstream recalculation.
Flow WAITS for explicit confirmation before proceeding to Phase 3.
```

---

#### §BYP — "Building Your Picture" Progress Indicator

Pinned horizontal strip between top nav and conversational stream. Always visible during Assessment.

```
┌─────────────────────────────────────────────────────────────┐
│  Building Your Picture                                       │
│  [Income ✓] [Dependents ○] [Protection ○] [Obligations ○]   │
│  [City ○] [Age ○] [Health ○] [Household ○]                  │
│                                    Your range: — months      │
└─────────────────────────────────────────────────────────────┘
```

| State | Indicator behaviour |
|:---|:---|
| **Start** | 8 empty pills (greyed labels). "Your range: — months" (placeholder). |
| **Dimension captured** | Pill fills with animated pulse. ○ → ✓. |
| **After first value-after** | "Your range" shows first estimate (e.g., "6–8 months"). Smooth count-up from —. |
| **After each subsequent cluster** | Range narrows or shifts. Smooth number morph. Warm amber pulse if range goes up; cool blue-green if down. |
| **All dimensions captured** | Range collapses to single number: "Your target: 8 months (₹4.2L)". All pills ✓. Subtle completion micro-animation. |
| **D9 triggered** | 9th pill appears (labelled to context). Fills with ✓. Number updates. |

---

#### §C3 — Assessment Summary Card (Phase Gate)

```
┌──────────────────────────────────────────────────────┐
│  ✏️  Your Profile Summary                             │
│                                                       │
│  Employment      Salaried — large private company     │
│  Household       Dual income                          │
│  Income          ₹1,20,000/mo                         │
│  Dependents      Infant, mother-in-law (no insurance) │
│  Health cover    ₹5L–10L                              │
│  Fixed costs     ₹60,000/mo (50%)                     │
│  City            Tier 2                               │
│  Age             30s                                  │
│  Health          Factored in                          │
│  [+ D9 row if triggered]                              │
│                                                       │
│  ┌─────────────┐  ┌──────────────┐                    │
│  │  Looks right │  │ Let me adjust │                   │
│  └─────────────┘  └──────────────┘                    │
└──────────────────────────────────────────────────────┘
```

- Each row tappable → §C4 Edit-in-Place. Downstream recalculation on edit.
- Health row shows "Factored in" — never displays specific conditions.
- Flow WAITS for explicit CTA. Deliberate friction at phase gate (T5).

---

#### Phase 2 — Component Specs

| Component | Type | Behaviour |
|:---|:---|:---|
| **"Building Your Picture" Indicator** | Pinned strip with dimension pills + running number | Hybrid — fixed above stream. 8 pills + optional 9th (D9). Animated count-up, smooth morph, warm/cool pulse on directional changes. |
| **Structured option chips** | Inline tappable options per dimension | §C1 for categorical (employment, city, age, dependents). Multi-select for dependency mapping. |
| **Confirm Chip (§C2)** | Inline editable number with ✓ and ✏️ | Flow-blocking until confirmed. Used for income and fixed obligations. |
| **Range Picker / Slider** | Slider with ₹ amounts and labelled markers | Used for income (range brackets) and obligations (slider pre-anchored at ~50% of income). |
| **Multi-Select Chip Set** | Group of toggleable chips | Used for dependency mapping. Multi-select mode. |
| **City Selector** | Search-enabled grouped selector | Metro cities listed individually; Tier 2/3 as groups with search. |
| **"Your Safety Net So Far" Card** | Read-only inline card with factor attribution | Shows at mid-assessment value-after. Each factor with directional impact. Smooth slide-up. Not editable. |
| **§C3 Summary Card** | Editable summary card with two CTAs | All 8+ data points. Each row tappable for §C4. Flow-blocking. |
| **§C4 Edit-in-Place** | Inline editor matching original input method | Triggered from §C3 rows. Downstream recalculation on edit. |
| **Insurance Advisory Flag** | Non-blocking inline advisory message | Appears when D3 signals no/inadequate health cover. Conversational, not a modal. |
| **Sensitive Data Text Fallback** | Text input with privacy framing | Offered for D8 (health). AI does not echo verbally. |

---

#### Phase 2 — Handover Notes

| Item | Detail |
|:---|:---|
| **Dimension capture order** | Engineering must NOT enforce a fixed sequence. The state agent tracks which dimensions are filled regardless of order. Phase 3 is data-dependent, not sequence-dependent. |
| **Multi-info turn parsing** | When a user provides multiple data points in one utterance ("I'm 34, salaried, two kids"), the LLM must parse and capture all of them. Engineering defines how multi-slot extraction works in the conversational agent. |
| **Value-after computation** | The computation agent calculates the running estimate after each dimension cluster. Engineering defines the trigger logic (how many new dimensions before a value-after fires). |
| **Progress indicator animation** | Directional guidance only: "smooth count-up," "number morph," "warm pulse." Exact ms timings and easing curves deferred to BuildSpec. |
| **§C3 downstream recalculation** | When a user edits a value at the summary gate, the computation agent recalculates the target and all affected downstream values. Engineering defines the recalculation pipeline. |
| **Agent interface schema — open collaboration point** | The input schema (user state, journey state, computed values) and output schema (component selection, configuration, render instructions) between agents is a joint product-engineering decision. Product provides the contract requirements (this document and the component specs). Engineering defines the technical schema. This must be co-designed — it cannot be product-only or engineering-only. Phase 2 is the most complex coordination point (4 agents, batch dimension capture, running computation, adaptive rendering) and should be the schema design starting point. |

---

### Phase 3: Target Setting ("Why This Number")

**User question:** "What's my number? And why?"

**AI's job:** Reveal the personalised target with full dimension attribution. Make the number feel inevitable and personal — not arbitrary. This is CarrotFin's signature trust-building moment.

**Screen type:** Hybrid (conversational reveal + inline Target Card with attribution strip)

**Modality pivot:** The AI's role shifts from questioner (Phase 2) to narrator (Phase 3). The user's role shifts from data provider to evaluator. Voice narrates headlines; screen carries detail. Simultaneous, not sequential (§0B Rule 3).

---

#### Phase 3 — LLM Guidance Prompt

> **Architecture note:** Like Phases 4 and 5, Phase 3 does not run from a script. The block below is the context and intent guidance for the LLM intelligence layer. The LLM adapts framing, pacing, and the editorial "key insight" to this specific user.

```
PHASE 3 INTENT — Target Setting ("Why This Number")

Your goal: Reveal the user's personalised EF target with full attribution so they
understand WHY the number is what it is. Phase 3 ends when the user accepts a target
(or adjusts and re-confirms) and all post-target steps (D3 buffer, Starter Shield)
are resolved.

WHAT YOU KNOW AT THIS POINT:
- All 8 dimensions (+ D9 if any) — confirmed at §C3 gate
- Computed target (₹ amount + months) — from computation agent
- Attribution breakdown: each dimension's directional contribution (+ or − months)
- User's literacy level and emotional signals from Phases 1–2
- Re-entry context (Scenario B existing savings amount, Scenario C user's own target,
  Scenario E existing EF corpus)

THE REVEAL SEQUENCE:
1. Conversational setup: brief narrative pause before the Target Card materialises.
   Build a moment — don't jump straight to the visual. The pause distinguishes
   this from a calculator output.

2. Target Card materialises inline with:
   - Headline: ₹ amount + months of essential expenses
   - Attribution strip (DD07): tiered linear arrow diagram. Each dimension as a
     row with factor label + directional adjustment (+ or −) + month contribution.
     Rows fill in with staggered animation (top to bottom). The stagger makes the
     logic feel "assembled" — reinforcing that the AI computed something.
   - Two CTAs: "Looks right ✓" (primary) / "Adjust an answer" (secondary)

3. While the card animates, narrate ONLY the headline number. Do NOT read the
   attribution strip — the screen carries that detail (§0B Rule 3).

4. If the target diverges materially from "3–6 months" generic advice:
   auto-expand the "Why is this different?" section within the card. Explain
   which specific factors push the user above/below the generic range.
   If target IS within 3–6 months: keep the link tappable but don't auto-expand.

5. Deliver ONE key insight — the single most surprising or high-impact factor from
   the attribution. This is your editorial voice: interpret the data, don't just
   read it. Choose the factor that would most change the user's mental model.
   Example: "The biggest thing people miss is the uninsured parent — that single
   factor adds a full month."

6. Go silent. Let the user evaluate. Do not prompt, rush, or filler-talk.
   Speak again only if the user asks a question or initiates an edit.

TARGET CARD CTAs:
- "Looks right ✓": user accepts → proceed to D3 buffer check (Step 3-D3) or
  Starter Shield (Step 3-SS) or Phase 4, depending on what triggers.
- "Adjust an answer": §C4 flow. Re-render the §C3 Summary Card. User edits a
  data point → computation agent recalculates → Target Card number morphs, attribution
  strip re-renders. AI narrates: "Updated — let me recalculate."

ATTRIBUTION STRIP (DD07):
Full design rules (layout, colour coding, collapse behaviour, row tappability,
literacy fallback) are in DD07. Phase 3–specific notes:
- D9 row (if triggered) appears as last factor before the total, labelled with
  its specific context.
- Each row is tappable: first tap → one-sentence explanation (§5.2 Level 2).
  Second tap → formula/data source (§5.2 Level 3).
- For low-literacy users (detected from calibration signals): replace the visual
  strip with a plain-English conversational breakdown. Offer "Show me the
  breakdown" to recover the visual.

RE-ENTRY ADAPTATIONS:
- Scenario B (existing savings): headline includes gap analysis — "Your target:
  ₹4.2L. You have ₹2.5L — you're 60% there." Gap shown AFTER attribution, not
  within it. Key insight emphasises validation: "You've already built a solid
  foundation."
- Scenario C (validation path): show comparison — "Your estimate: ₹5L. My
  recommendation: ₹5.8L." Two CTAs: "Go with ₹5.8L" / "Stick with my ₹5L."
  User autonomy first (DD05).
- Scenario E (review): gap or surplus framing. If surplus: "You're over-funded
  — the extra could work harder elsewhere."

WHAT YOU DO NOT DO:
- Read the attribution strip aloud row by row
- Frame the number as an obligation — it's a recommendation the user controls
```

---

#### §3-D3 — Social Obligation Buffer (Post-Target, Optional)

> **D3 buffer trigger:** Surfaces as a separate, visually distinct prompt after the user accepts the primary target. Not gated by archetype — all users get the offer.

**LLM framing:** Frame as "separate from your safety net." The word "separate" is critical — prevents mental conflation with the core EF target.

**D3 calculation:** DETERMINISTIC — computation agent handles the math. See BuildSpec for formula. The LLM decides when to prompt; the computation agent calculates.

```
┌──────────────────────────────────────────────┐
│  🎁  Family Obligation Buffer (Optional)      │
│                                              │
│  A small separate fund for weddings,         │
│  ceremonies, and family support — so your    │
│  emergency fund stays untouched.             │
│                                              │
│  ┌──────────────┐  ┌───────────────────┐    │
│  │  Add a buffer │  │  Skip — not now    │   │
│  └──────────────┘  └───────────────────┘    │
└──────────────────────────────────────────────┘
```

If "Add a buffer": AI asks for typical obligation cost (range chips) and frequency. Computation agent calculates. D3 Confirmed Card shows annual amount, explicitly labelled "separate from your ₹[X]L safety net." D3 buffer flows as a separate sub-fund into Phase 4 allocation and Phase 5 contribution.

---

#### §3-SS — Starter Shield (Conditional Milestone Framing)

**Trigger (DETERMINISTIC):** Target ≥ ₹5L AND at least one of:
- Age bracket: 20s or early 30s
- Monthly income ≤ ₹80K
- Target-to-monthly-income ratio > 6×

This is a hard rule, not LLM-decided. If conditions are met, the Starter Shield fires.

**LLM framing:** Reframe the daunting total as an achievable first step. "You don't need to build it all at once. Your first month is the most important one." The Target Card transforms with a milestone overlay:

```
┌──────────────────────────────────────────────┐
│  🛡️  Your Emergency Fund Target               │
│        ₹11.5L  ·  12 months                  │
│                                              │
│  ── Your milestones ──────────────────────── │
│  ★ Starter Shield     ₹96K   (1 month)  ←── │
│    3 Months Secure    ₹2.9L  (3 months)      │
│    Half-Year Shield   ₹5.8L  (6 months)      │
│    Fully Funded       ₹11.5L (12 months)     │
│                                              │
│  [Got it — let's plan →]                      │
└──────────────────────────────────────────────┘
```

Attribution strip collapses. Only Starter Shield row is active (★ marker, warm glow). Others greyed. AI narrates only the Starter Shield amount — not all milestones.

If Starter Shield NOT triggered: no milestone overlay in Phase 3. Milestones appear in Phase 5 (Contribution Planning) as part of the timeline.

---

#### Phase 3 — Voice Handling Principles

Phase 3 is a modality pivot. Key rules:
1. **AI is narrator, not reader.** Narrate headlines and editorial insights. The screen carries data and attribution.
2. **Silence is a tool.** After the reveal, go quiet. The user needs space to evaluate.
3. **Voice carries emotion, screen carries logic.** The spoken key insight is the emotional hook. The attribution strip is the logical proof.

---

#### Phase 3 — Component Specs

| Component | Type | Behaviour |
|:---|:---|:---|
| **Target Card** | Inline card: headline number + collapsible attribution strip + 2 CTAs | Slide-up materialisation. Card grows vertically based on attribution row count (5–9 rows). Headline renders first, then attribution rows stagger in. |
| **Attribution Strip (DD07)** | Vertical stacked arrow diagram with tappable rows | Each row: factor label + directional adjustment + month contribution. Warm/cool colour coding. Rows tappable → expand explanation (§5.2 Level 2/3). Collapsible. |
| **Literacy Fallback** | AI message with plain-English attribution | Replaces visual strip for low-literacy users. "Show me the breakdown" recovery affordance. |
| **Myth-Bust Expansion** | Collapsible inline section within Target Card | Auto-expands when target diverges from "3–6 months." Tappable when within range. |
| **D3 Prompt Card** | Optional inline card with 2 CTAs | Visually distinct from Target Card: different background tint, dashed border, 🎁 icon. Slides up below Target Card. Fades out on skip. |
| **D3 Buffer Sizing Inputs** | Range chips + frequency selector | §C1 for ranges, §C2 for typed amounts. |
| **D3 Confirmed Card** | Updated D3 card with confirmed buffer | Annual amount, calculation breakdown, "separate from" label. Confirm/Remove CTAs. |
| **Starter Shield Overlay** | Milestone list within Target Card | 4 milestones from philosophy §6. Only Starter Shield active (★, warm glow). Attribution strip collapses. Single CTA: "Got it — let's plan →". |
| **§C4 Re-edit Flow** | Edit-in-Place from "Adjust an answer" CTA | Re-renders §C3 Summary Card. Edits → recalculation → Target Card number morphs, attribution re-renders. |

---

#### Phase 3 — Handover Notes

| Item | Detail |
|:---|:---|
| **Attribution strip row data** | The computation agent provides the dimension-by-dimension breakdown (factor, direction, month contribution). Engineering defines the output schema. |
| **DD07 implementation** | Tiered arrow diagram layout, colour coding, collapse behaviour, tappable expand levels — all defined in DD07. BuildSpec translates to Flutter. |
| **Starter Shield trigger** | Deterministic: target ≥ ₹5L AND (age ≤ early 30s OR income ≤ ₹80K OR target/income > 6×). Engineering implements as a hard rule — not LLM-decided. |
| **D3 buffer math** | Deterministic: midpoint(amount range) × midpoint(frequency band). Engineering implements in computation agent. LLM decides when to prompt; computation agent calculates. |
| **Level 3 data sources** | §5.2 Level 3 expand reveals formula + data source per attribution row. Actual data sources (e.g., paediatric cost differentials) TBD for BuildSpec. |
| **Scenario B/C/E target card variants** | Gap analysis overlay, comparison view, surplus framing — these are layout variants of the same Target Card component. Engineering defines as card states, not separate components. |

---


### Phase 4: Fund Architecture ("Where Should the Money Sit?")

**User question:** "Okay, I know the target. But where do I actually put this money?"

**AI's job:** Recommend how to allocate across 3 liquidity layers and explain the structure. No product execution — that belongs to Phase 5. Phase 4 ends when the user has mentally accepted the structure.

**Screen type:** Hybrid (conversational setup → inline Allocation Card with horizontal liquidity gradient strip per [DD08](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD08-allocation-visual-design.md))

**Advisory ceiling (D4 / DD02):** Instrument type only. No specific fund names, AMC names, or bank products.

> [!NOTE]
> **DD08:** The 3-layer allocation visual is a **horizontal liquidity gradient strip** — a single continuous bar with proportionally-sized zones, warm amber (Instant) → cool blue-slate (Stability). This communicates liquidity as a spectrum, not three separate buckets. Resolves Q4 / T1 for Phase 4.

---

#### Phase 4 — LLM Guidance Prompt

> **Architecture note:** Phase 4 does not run from a script. The following block is the context and intent guidance provided to the LLM intelligence layer. The LLM decides exactly what to say, in what order, in what tone — based on what it knows about the user at this point in the journey (income pattern, target size, archetype signals, literacy level, emotional state).

```
PHASE 4 INTENT — Fund Architecture

Your goal: Help the user understand WHERE to put their emergency fund money across 3 liquidity layers.
Phase 4 ends when the user verbally or tap-confirms they understand and accept the structure.
Do NOT discuss contribution amounts or timelines here — that is Phase 5.

WHAT YOU KNOW AT THIS POINT:
- User's EF target (₹ amount + months) — confirmed in Phase 3
- Income pattern (variable vs. salaried, sole vs. dual earner) — from Phase 2 Assessment
- Target size — determines DICGC trigger
- D3 social obligation buffer (if added) — treat as a separate sub-fund, mention briefly at the end if relevant

ALLOCATION LOGIC:
Think about the fund in terms of "3 speeds of money":
- Speed 1 (Instant): 1–2 months in savings account or sweep-in FD. Available via ATM or UPI 24/7.
- Speed 2 (Quick): 2–4 months in liquid mutual funds. T+1 in general; up to ₹50K is near-instant for most funds.
- Speed 3 (Stable): Remaining months in short-term FD or ultra-short/debt fund. 1–3 business days.

Adjust the layer weighting to the user's income pattern:
- Variable income / sole earner / gig: Lean toward more Speed 1 (higher instant-access proportion). Income is lumpy — they need more buffer available without any friction.
- Stable salaried / dual income: Shift weight toward Speed 2 and 3. Regular income is a natural hedge — they don't need as much sitting in the lowest-yield layer.

HOW TO EXPLAIN THIS:
- Lead with the "why" not the "what." The 3-layer structure exists because emergencies have different speeds.
- Adapt your vocabulary to literacy level. High-literacy user: "liquid fund is T+1 with near-instant for ₹50K." Low-literacy user: "it's like an FD that you can get back the next business day."

DICGC TRIGGER (conditional):
If the user's target exceeds ₹5L, include: "One thing worth noting — if you're putting a large amount in FDs, spread across at least 2 banks. Deposit insurance covers only ₹5L per person per bank. This isn't scary — just a practical distribution."
This is a factual disclosure, not a warning or upsell. Tone should be matter-of-fact.

WHAT YOU DO NOT DO:
- Name specific banks, AMCs, or fund products
- Execute or suggest executing anything — that's Phase 5
- Add unnecessary caveats or hedges about market returns
- Present this as complex — the structure is simple once the "3 speeds" frame lands

PHASE BOUNDARY:
Phase 4 ends with an explicit user acceptance (tap or voice). The AI asks something like: "Does this structure make sense for how you want to manage it?" and waits for confirmation before transitioning to Phase 5.
```

---

#### Phase 4 — Allocation Card (Component Spec)

The Allocation Card materializes inline in the conversational stream **simultaneous** with the AI's verbal framing of the 3 speeds (§0B Rule 3 — simultaneous, not sequential).

**Allocation Card layout:**

```
┌────────────────────────────────────────────────────────────┐
│  🏦  Your Fund Structure                                    │
│                                                            │
│  ━━[ Instant Access ]━━━━━━[ Quick Access ]━━[ Stable ]━━  │
│    (warm amber)             (neutral)         (cool slate) │
│    ₹96K                    ₹2.9L             ₹2.5L         │
│    1 month                 3 months          ~3 months     │
│                                                            │
│  ──────────────────────────────────────────────────────   │
│                                                            │
│  ⚡ Instant Access  ·  ₹96K  ·  Savings / Sweep-in FD      │
│     Available instantly via ATM or UPI.                    │
│                                                            │
│  ⏱ Quick Access    ·  ₹2.9L  ·  Liquid Mutual Fund         │
│     Back the next business day. Up to ₹50K is near-       │
│     instant.                                               │
│                                                            │
│  🔒 Stable Reserve  ·  ₹2.5L  ·  Short-term FD / Debt Fund │
│     1–3 business days. Highest idle yield.                 │
│                                                            │
│  ┌─────────────────────┐  ┌─────────────────────┐         │
│  │  This works for me ✓ │  │  Tell me more        │        │
│  └─────────────────────┘  └─────────────────────┘         │
└────────────────────────────────────────────────────────────┘
```

**Card behaviour:**
- The gradient strip zone widths are proportional to ₹ amounts — wider zone = more money there.
- "Tell me more" CTA: AI continues in conversational stream with a deeper explanation of whichever layer the user taps/asks about.
- "This works for me ✓" CTA: Accepts the structure. This phase gate uses a **lighter inline confirm** — the user is accepting a recommendation, not verifying data they entered.

**DICGC note rendering (conditional):**

If total target > ₹5L, a **contextual footnote-style text** appears below the card — NOT a warning badge or alert. Styled as plain explanatory prose:

> "If you use FDs for Layer 1 or 3, spread them across 2+ banks. Deposit insurance covers ₹5L per bank — simple to do, worth doing."

No icon, no red. It is a factual note, visually subordinate to the card.

---

#### Phase 4 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **Allocation Card** | Inline card with horizontal gradient strip + detail rows + 2 CTAs | Hybrid (inline in stream, anchors the phase) | Strip zone widths proportional to ₹ amounts. Warm amber → cool blue-slate gradient. Detail rows always visible. Slide-up materialisation (300ms). |
| **Horizontal Liquidity Gradient Strip (DD08)** | Single proportional bar with 3 colour zones | Generative (rendered within Allocation Card) | Zone widths computed from allocation amounts. No axes, no legend — strip is self-labelled. Becomes app-wide idiom for liquidity spectrum. |
| **Layer Detail Rows** | 3 rows: icon + label + amount + vehicle + access time | Generative (rendered within Allocation Card) | ⚡/⏱/🔒 icons reinforce access-speed mental model. Plain English for access time. No product names. |
| **DICGC Footnote** | Inline explanatory text (not a warning/badge) | Generative (below Allocation Card, conditional) | Triggers if total target >₹5L. Plain prose style. No alarm colouring. |
| **"Tell me more" Expansion** | AI conversational continuation in stream | Generative (inline in stream) | Triggered by tapping layer rows or asking follow-up questions. Conversational — not a modal or tooltip. |
| **Phase Gate Confirm (light)** | "This works for me ✓" CTA in Allocation Card | Generative (inline within card) | Lighter than full §C3 — accepting a recommendation, not verifying user-entered data. |

---

### Phase 5: Contribution Planning ("How Do I Get There?")

**User question:** "How do I actually build this over time?"

**AI's job:** Translate the target into a monthly contribution plan. Frame it as a "self-EMI." Connect to the Starter Shield milestone from Phase 3. Hand the user a persistent Action Card (DD09) and an optional salary-day reminder. This is the journey's highest-stakes CTA — the user must leave the app and act.

**Screen type:** Hybrid (conversational setup → Contribution Plan Card → Action Card + reminder opt-in)

**Connects to:**
- §3-SS (Starter Shield) — the first milestone in Phase 5 must be identical to the Starter Shield revealed in Phase 3. Do not re-introduce it; reference it.
- J01 §1A (nudge philosophy) — the exit copy is defined there. Reference it; do not rewrite.
- DD09 — the contribution plan deliverable: Action Card + salary-day reminder.

> [!NOTE]
> **DD09:** When the user confirms their plan, they receive: (1) a persistent **Action Card** saved to their EF goal card on the home surface, with numbered step-by-step external setup instructions per allocation layer; (2) an opt-in **salary-day reminder** that fires monthly. No PDF export in V1. Resolves Q6.

---

#### Phase 5 — LLM Guidance Prompt

> **Architecture note:** Like Phase 4, Phase 5 does not run from a script. The block below is the context and intent guidance for the LLM intelligence layer. The LLM adapts framing, pacing, and sequencing to this specific user's situation.

```
PHASE 5 INTENT — Contribution Planning

Your goal: Turn the user's EF target into a concrete monthly action they will actually start.
The plan is advisory only. You're not executing transfers — you're giving them the plan to do it themselves.

WHAT YOU KNOW AT THIS POINT:
- EF target (₹ amount + months) — confirmed at Phase 3
- Allocation structure (3 layers + amounts per layer) — confirmed at Phase 4
- Monthly income (range or working estimate) — from Phase 2 Assessment
- Income pattern (salary date predictability: fixed payday vs. variable) — infer from employment type
- Starter Shield milestone amount (1 month of essential expenses) — from Phase 3 §3-SS if triggered
- D3 social obligation buffer (if active) — has its own separate contribution line

WHAT TO HELP THE USER FIGURE OUT:
1. How much per month, realistically, can they set aside?
   - Ask for an amount. Don't assume. This is personal.
   - Once they give you a number, compute: at that rate, time to full target.
   - Offer a comparison: "At ₹X more per month, you'd get there Y months sooner."
   - Recommend the faster path — show the cost of the gap in plain terms (the more months before they're funded, the more months they're exposed if something goes wrong).
   - For variable income users: reframe the question. Don't ask for a fixed monthly amount.
     Instead: "What's the minimum you'd commit on a slow month? On a good month, you can top up."
     Frame a floor (e.g., "target ₹X as your floor — whatever extra comes in, send it too").

2. Frame it as a self-EMI.
   The moment to introduce this frame is when you've agreed on an amount.
   "Think of this like an EMI you pay yourself. The difference: this one builds a cushion instead of clearing a debt."
   The self-EMI frame works because it leverages existing mental models — EMI = automatic, non-negotiable, happens before spending.
   For variable income users, the frame flexes: "Set aside what you can on salary credit, aim for ₹X as your floor."

3. Connect to the Starter Shield.
   If the Starter Shield was shown in Phase 3 (§3-SS), the first milestone in Phase 5 is the same number.
   Do NOT re-introduce it as if it's new. Reference it: "Your first milestone is the Starter Shield — ₹[X] — same one we talked about earlier."
   Show how many months at their chosen contribution rate until they hit the Starter Shield.
   This is the anti-paralysis anchor — it makes the full target feel reachable by shrinking the first step.

4. Milestone timeline.
   Show the full milestone path with estimated dates:
   - Starter Shield (1 month): at ₹[contribution]/mo, you hit this in [N] months — around [Month Year]
   - 3 Months Secure: [date]
   - Half-Year Shield: [date]
   - Fully Funded: [date]
   Dates are more motivating than "N months" — they create a concrete mental anchor.

ADVISORY OUTPUT (D4 / DD02):
The plan specifies amounts and timing, but not products:
"Set up an auto-transfer of ₹[Layer 1 amount] on your salary date to a savings account or sweep-in FD.
Set up a separate recurring amount of ₹[Layer 2 amount] to a liquid mutual fund."
No specific bank, AMC, or fund name. User executes externally.

EXIT COPY:
When the user confirms, use the exit copy defined in J01 §1A — do not improvise a new version.
The canonical phrasing: "Your plan is set. I'll keep an eye on things and reach out when something
meaningful happens — a milestone hit, a life change, or a chance to optimise. You won't hear from
me just to check in."
This tone (proactive, non-intrusive, purposeful) is the CarrotFin monitoring state voice.

WHAT YOU DO NOT DO:
- Name specific products, apps, or platforms for execution
```

---

#### Phase 5 — Contribution Plan Card (Component Spec)

Materializes inline after the contribution amount is agreed upon. Simultaneous with the AI's verbal confirmation of the plan.

**Contribution Plan Card layout:**

```
┌────────────────────────────────────────────────────────────┐
│  📅  Your Contribution Plan                                 │
│                                                            │
│  ₹6,000 / month  ·  on your salary date                   │
│                                                            │
│  ── Your milestone path ────────────────────────────────── │
│                                                            │
│  ★ Starter Shield  ₹96K  →  Aug 2026   (16 months away)   │
│    3 Months Secure ₹2.9L  →  Oct 2027                     │
│    Half-Year Shield ₹5.8L →  Oct 2028                     │
│    Fully Funded    ₹11.5L →  Apr 2031                     │
│                                                            │
│  Your self-EMI: ₹6,000 · every salary day · auto-transfer │
│                                                            │
│  ┌──────────────────────┐                                  │
│  │  Confirm my plan  →  │                                  │
│  └──────────────────────┘                                  │
└────────────────────────────────────────────────────────────┘
```

**Card behaviour:**
- Contribution amount shown prominently. Tappable to edit (§C4 — edit triggers recalculation of all milestone dates).
- Milestone rows: dates, not month counts. The Starter Shield row is ★-marked (same marker as Phase 3 §3-SS — visual continuity).
- "Confirm my plan →" CTA: primary, full-width. On tap — triggers the Action Card + reminder flow below.
- The card uses the self-EMI label intentionally as a persistent UI element, not just a spoken phrase.

---

#### Phase 5 — Action Card + Reminder (Post-Confirmation, DD09)

Fires immediately after the user taps "Confirm my plan."

**Sequence:**

**Step 1 — AI acknowledgement (spoken + on-screen):**
AI voice: "Perfect. Here's everything you need to set this up — saved to your emergency fund card so you can come back to it anytime."

**Step 2 — Action Card materializes:**

```
┌────────────────────────────────────────────────────────────┐
│  ✅  Your Action Plan                                       │
│  Set these up on your next salary date.                    │
│                                                            │
│  1. ⚡ Instant Access layer — ₹[X]                         │
│     Set up an auto-transfer to your savings account        │
│     or a sweep-in FD. Do this first — it's your immediate  │
│     buffer.                                                │
│                                                            │
│  2. ⏱ Quick Access layer — ₹[Y]                            │
│     Set up a recurring amount to any liquid mutual fund.   │
│     Money is back next business day when you need it.      │
│                                                            │
│  3. 🔒 Stable Reserve layer — ₹[Z]                         │
│     Set up a recurring FD or short-term debt fund.         │
│     Do this once you've built Layer 1 and 2 first.         │
│                                                            │
│  [Remind me on salary day]      [Dismiss]                  │
└────────────────────────────────────────────────────────────┘
```

**Action Card behaviour:**
- Three numbered steps, one per allocation layer, in order of priority (Layer 1 first — most critical, easiest to start).
- Per-layer amounts pulled from Phase 4 allocation. Instrument type only (DD02).
- Steps are sequenced deliberately: Layer 3 is explicitly "do this once you've built 1 and 2 first" — prevents the user from over-investing in the lowest-yield stable layer before instant access is built.
- Card saves to the EF goal card on the home surface — accessible without re-entering the conversational flow. Persistent across sessions.

**Step 3 — Salary-day reminder opt-in:**
"Remind me on salary day" → user taps → system prompt for notification permission (standard OS flow) → if granted: recurring monthly reminder set for their stated/inferred salary date. Content: "Your ₹[X] self-EMI is due. Tap to see your action steps." Deep-links back to the Action Card.

If notification permission denied: AI responds in stream — "No worries — your action plan is saved right here whenever you're ready."

**Step 4 — Exit (§1A canonical copy):**

AI voice and on-screen text (verbatim from J01 §1A):
> "Your plan is set. I'll keep an eye on things and reach out when something meaningful happens — a milestone hit, a life change, or a chance to optimise. You won't hear from me just to check in."

This line is never paraphrased or improvised. It is the CarrotFin monitoring-state voice — calibrated to be distinct from notification-spam apps. The phrasing "you won't hear from me just to check in" is the differentiation claim.

**Monitoring state begins.** EF goal card on home surface updates to show: fund target, current amount (₹0 or existing corpus from Scenario B/E), next milestone, contribution plan summary.

---



#### Phase 5 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **Contribution Plan Card** | Inline card with amount, milestone path (dates), self-EMI label, Confirm CTA | Hybrid (inline in stream, anchors the phase) | Milestone rows show dates (not month counts). Starter Shield row ★-marked for visual continuity with Phase 3. Amount row tappable for §C4 edit with live date recalculation. |
| **Action Card (DD09)** | Persistent numbered instruction card with 3 steps + reminder CTA | Hybrid (inline stream on confirmation; persists to EF goal card on home) | Numbered steps per allocation layer in priority order. Instrument type only. Saved to home surface goal card. |
| **Salary-Day Reminder (DD09)** | Opt-in recurring notification trigger | System (OS notification infrastructure) | Monthly cadence. Deep-links to Action Card. Graceful degradation on denial. |
| **Milestone Timeline Rows** | 4 rows with label, ₹ amount, target date | Generative (rendered within Contribution Plan Card) | Same milestone labels as §3-SS. Dates computed from contribution amount and gap. ★ on Starter Shield row. |
| **§C4 Contribution Edit** | Tappable amount field → inline editor → date recalculation | Generative (inline within Contribution Plan Card) | Editing the contribution amount recalculates all milestone dates live. No full summary card needed — amount is the only input at this stage. |
| **Exit Confirmation Message** | AI message bubble with canonical §1A copy | Generative (inline in stream) | Constraint: exact phrasing from J01 §1A — see Phase 5 LLM prompt. |
| **EF Goal Card (Home Surface)** | Static goal card showing: target, current amount, next milestone, plan summary | Static (home surface — outside conversational flow) | Updated after Phase 5 confirmation. Shows Action Card entry point. Progress bar reflects current corpus vs. target. |

---

## 7. Open Questions — All Resolved

All 6 phase-specific open questions resolved as of Step 2D. See DD06–DD09 for decision records.

---

*This document contains phase-by-phase interaction specifications for the J01 Emergency Fund journey. For product scope, boundaries, patterns, and metrics, see [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md).*

