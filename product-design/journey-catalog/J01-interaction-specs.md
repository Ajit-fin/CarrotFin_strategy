# J01: Emergency Fund — Phase Interaction Specs

> **Parent document:** [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md)
> **Last updated:** 2026-04-16
> **Status:** Phases 1–5 fully specced (Step 2D complete). All 6 open questions resolved. See §7.
> **Dependencies:** This file instantiates the cross-cutting patterns defined in J01 §0B (voice+screen, re-entry, §C1–§C4 confirmation patterns). Read §0B first.

---

> [!IMPORTANT]
> **All journey phases described below are one possible scenario — not a rigid script.** The GenAI layer must adapt in real time to what the user actually says and does. The phase descriptions capture the design intent and a representative happy path, not a decision tree the AI must execute literally. Phase 2 (UX Journey Design) defines the adaptive variation matrix; Phase 5 (BuildSpec) defines the engineering contract for how real-time adaptation is implemented in Flutter.
>
> Specific implications:
> - The AI may collapse or skip phases if the user is clearly already past that point (e.g., a user who says "I already have some savings but I'm not sure if it's enough" enters Phase 2 directly).
> - The AI may revisit a phase if the user contradicts an earlier answer or asks to adjust.
> - The sequence within each beat is a guide; the actual conversational path is driven by the user's responses, not a fixed script.

### Phase 1: Discovery / Education — Full Interaction Spec

**User question:** "Do I actually need an emergency fund? I thought my savings/investments cover me."

**AI's job:** Establish relevance and urgency without fear-mongering. Use loss framing (cost of NOT having a fund), not abstract definitions.

**Screen type:** Generative (conversational stream — per `screen-taxonomy.md`)

**Trust level at entry:** Zero/New. The AI earns the right to ask for data by delivering value first.

---

#### Phase 1 — Step-by-Step Interaction Spec

> **Prerequisite:** The user has selected "🛡️ Build my safety net" from the §1B goal selection surface. §1B is NOT part of Discovery — it is the app's entry point and is always shown. Discovery begins below.

**Step 1.1 — AI Opening (The Hook)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | Speaks the opening question aloud (if voice is on). Conversational, direct tone — not a greeting. |
| **Screen** | Conversational stream. AI message bubble appears with the question. Below it: 4 inline tappable option chips. |
| **AI text** | "Let's figure out one thing first — do you actually have an emergency fund right now?" |
| **Structured options** | `Yes, I have one` · `Sort of — some savings` · `Not really` · `Not sure what counts` |
| **Confirmation** | §C1 (Light Confirm) — user taps an option; it highlights. AI echoes verbally: "Sort of — got it." Flow proceeds. |
| **Voice input** | User can say "I have some savings" or "Not really" → AI maps to nearest option, highlights it (§C1). |
| **Literacy calibration** | Starts here. If user taps quickly → high comfort. If user pauses → lower comfort. If user types free text instead of tapping → capture language signal for vocabulary calibration. |

**Step 1.2 — Adaptive Response Branch**

The AI's response depends on the user's Step 1.1 selection. Four paths, converging at Step 1.3:

| User selected | AI response | Routing effect |
|:---|:---|:---|
| **"Yes, I have one"** | "Nice — that puts you ahead of most people. Let me check if it's sized right for your situation. Takes about 2 minutes." | Triggers **Scenario B re-entry** (see §1-RE below). Skip to Phase 2 (Assessment) with "health check" framing. |
| **"Sort of — some savings"** | "That's a start. The question is whether what you have would actually cover you if something went wrong. Let me show you what 'wrong' can look like." | Proceeds to Step 1.3 (Emergency Framing). |
| **"Not really"** | "Honest answer. Most people don't — and most people think they're fine until they're not. Let me show you what's at stake." | Proceeds to Step 1.3 (Emergency Framing). |
| **"Not sure what counts"** | "Fair question — there's a lot of confused advice out there. Let's sort it out." | Proceeds to Step 1.3 (Emergency Framing). |

| Aspect | Spec |
|:---|:---|
| **AI voice** | Speaks the response aloud. Tone: empathetic, not alarmist. For "Yes" branch: respectful, framed as enhancement. |
| **Screen** | AI message bubble. For the "Yes" branch, a small transition animation ("checking..." → moves to Phase 2 assessment stream). |

**Step 1.3 — Emergency Framing (The "What Qualifies" Card)**

> Only reached by "Sort of" / "Not really" / "Not sure" paths. "Yes" path skips to Phase 2.

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Quick check — which of these have happened to you or someone close in the last couple of years?" |
| **Screen** | AI message + an **inline quick-sort card** materializes below. The card is NOT a lecture — it is an interactive element. |
| **Card design** | Two columns: ✅ "Emergency" / ❌ "Not an emergency". 6–8 scenario chips that the user can drag or tap to sort. Pre-populated with common confusions from §1 of philosophy (job loss ✅, vacation ❌, medical ✅, wedding ❌, etc.). |
| **Interaction** | User taps items to toggle their column. AI reacts to each tap (brief, not verbose). Not all items need sorting — user can stop and proceed. |
| **Voice handling** | User can say "Medical happened to us" — AI highlights the medical chip in the ✅ column (§C1 light confirm). |
| **Value delivery** | After 2–3 taps (or 5s of inactivity on the card): AI responds to the pattern. If user marked medical or job-related: "That's exactly the kind of thing an emergency fund absorbs. Without one, most people end up on a credit card — and that's where it gets expensive." |

**Step 1.4 — Contextual Myth Busting (Conditional)**

> Only triggers if the user says or signals a specific myth. Not shown by default.

| Trigger | AI response | Visual |
|:---|:---|:---|
| User says "I'll just use my credit card" | "Credit cards charge 36–42% APR on cash advances. A ₹2L emergency becomes ₹3L+ within a year. That's not a backup plan — that's a debt trap." | Inline cost comparison card: ₹2L emergency → credit card cost vs. EF cost. Two-bar visual, stark contrast. |
| User says "My investments / MFs can cover me" | "Selling equity during a crash is selling at the worst possible time. And stopping your SIPs to cover expenses breaks the compounding curve." | Inline chart: SIP growth with vs. without a 3-month interruption. The gap is the story. |
| User says "My family will help" | "Family support is valuable — but it's declining with urbanization, and it shouldn't be your only plan. Think of it as a bonus, not a strategy." | No visual — conversational response only. Lighter touch. |

| Aspect | Spec |
|:---|:---|
| **AI voice** | Speaks the rebuttal. Tone: matter-of-fact, not preachy. Uses loss framing per behavioral framework §1.1. |
| **Screen** | AI message + conditional inline visual (cost card or chart, depending on myth). |
| **If no myth triggered** | Skip this step entirely. The AI doesn't manufacture myth-busting content. |

**Step 1.5 — Discovery Conclusion / Bridge to Assessment**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Here's the good news — we can figure out exactly what you need. Not a generic rule. Your actual number, based on your situation. Ready?" |
| **Screen** | AI message + single CTA button: `Let's do it` (primary). No secondary "maybe later" — the phrasing is an invitation, not a commitment gate. |
| **Transition** | On tap: smooth scroll/transition to Phase 2. The "Building Your Picture" progress indicator (see Phase 2, §BYP below) animates in at the top of the screen. |
| **Voice** | User can say "Let's go" / "Sure" / "Yes" → same as tapping the CTA. |

---

#### Phase 1 — Adaptive Variations

##### New Parent (34F, salaried IT, ₹14L/yr, infant + mother-in-law, Tier 2, dual income)

| Step | Variation |
|:---|:---|
| **1.1** | Likely selects "Sort of" or "Not sure" — new parents often have informal savings but haven't labeled them as EF. |
| **1.3** | AI's framing card reaction emphasizes medical emergencies with infant: "With a baby in the house, medical surprises go up significantly — midnight ER visits, unexpected paediatric costs. That's where your fund earns its keep." |
| **1.4** | If she mentions investments as backup: AI highlights SIP interruption risk specifically — "Stopping your SIPs now, in your early 30s, is the most expensive time to interrupt compounding." |
| **1.5** | Bridge framing: "Your family setup has some specific dimensions — let me factor those in. Takes about 3 minutes." |

##### Sandwich Generation (42M, SME owner/variable income, ₹22L/yr, 2 kids + aging parents, single earner, metro)

| Step | Variation |
|:---|:---|
| **1.1** | More likely to select "Sort of" — higher earners often have savings but no structured EF. May type a free-text response like "I have FDs and some in savings." |
| **1.2** | If free-text signals existing savings → AI treats as Scenario B (existing savings) and offers: "Sounds like you've been sensible about this. Let me check if the amount matches what your situation actually demands." |
| **1.3** | AI's framing emphasizes single-earner risk: "As the sole earner for 4+ people, you're carrying more weight than most. If something interrupts your income, there's no second salary to fall back on." |
| **1.4** | If mentions "my business can sustain us": AI surfaces variable income risk — "Business income has peaks and troughs. An EF is what lets you ride the troughs without touching the business capital." |
| **1.5** | Bridge framing: "Your situation is more complex than most — variable income, aging parents, school-age kids. That's exactly why a personalised number matters more than a generic guideline." |

---

#### §1-RE — Phase 1 Re-Entry Handling

> How Discovery adapts for users who arrive with existing context (Scenarios B, C, E from §0B). All re-entry happens AFTER §1B (goal selection surface — always shown).

##### Scenario B: User with Existing Savings

**Detection:** Per §0B Scenario B.

**Discovery adaptation:**
1. AI skips Steps 1.3–1.4 entirely (emergency framing and myth busting are irrelevant — user is already motivated).
2. AI delivers a bridge message: "Let me check if your savings are sized right for your situation. We'll look at your specific factors — takes about 2 minutes."
3. Direct transition to Phase 2 (Assessment) with **"health check" framing** — the AI is validating, not building from scratch.
4. **Key tone shift:** "Let me see if you're covered" (validation) vs. "Let me figure out what you need" (fresh build).

**Phase 2 impact:** Assessment Beat 1 includes an additional early question: "Roughly how much have you set aside?" (§C2 — Active Confirm for the numeric input). This pre-fills the existing corpus for the gap analysis in Phase 3.

##### Scenario C: User with a Known Target

**Detection:** Per §0B Scenario C.

**Discovery adaptation:**
1. AI acknowledges the user's work immediately: "That's a solid starting point."
2. AI offers — does not force — a validation check: "Online calculators often miss a few factors specific to your situation. Mind if I run a quick check? It's about 2 minutes."
3. **If user agrees:** Transition to Phase 2 (Assessment). At Target Setting (Phase 3), AI compares its number with the user's: "Your estimate of ₹5L was close — I'd suggest ₹5.8L because [attribution]. Want to go with mine or stick with yours?"
4. **If user declines:** AI accepts. "Fair enough — ₹5L it is." Skip directly to Phase 4 (Fund Architecture). The user's stated target is used without assessment.
5. Steps 1.3–1.5 are skipped entirely.

**Key principle:** User autonomy first (per DD05). Never force redundant assessment.

##### Scenario E: User with Existing EF (Review/Optimization)

**Detection:** Per §0B Scenario E.

**Discovery adaptation:**
1. AI skips all Discovery content — user is proactive, already past the urgency/education stage.
2. AI captures existing fund data: "You have ₹5L total — that's a solid foundation. Let me check if it matches your situation."
3. Transition to Phase 2 (Assessment) with pre-filled existing corpus. Assessment is framing as "gap check," not "build from scratch."
4. Beat 2 may have pre-filled data if user already mentioned specifics (e.g., "I have 2 kids" → dependency question pre-answered).

**Phase 2 impact:** Assessment runs normally but the "Building Your Picture" indicator (see §BYP) starts with some dimensions pre-filled. At Assessment end, AI shows gap/surplus vs. existing corpus rather than a fresh target.

**Key across all re-entry scenarios:**
- Per §0B: Discovery content remains available on-demand as contextual cards in later phases if relevant myths surface.
- The AI route decision is transparent: "Since you already have savings, let me jump straight to checking if they're sized right."

---

#### Phase 1 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **Emergency Quick-Sort Card** | Interactive sorting card with two columns | Generative (inline in stream) | Tappable items, drag optional. 6-8 scenario chips from §1 of philosophy. |
| **Myth Buster Inline Card** | Comparison visual (2-bar or 2-line) | Generative (inline in stream) | Conditional — only renders if a specific myth is triggered. Multiple variants: credit card cost, SIP interruption, etc. |
| **Discovery Bridge CTA** | Single primary button | Generative (inline in stream) | "Let's do it" — transitions to Phase 2. |
| **Re-entry Routing Message** | AI message with tone-shifted framing | Generative (inline in stream) | Different copy variants for Scenarios B, C, E. |

---

### Phase 2: Assessment — Full Interaction Spec

**User question:** "Okay, I need one. How much do I actually need?"

**AI's job:** Walk through the 8 sizing dimensions in a conversational, progressive way — NOT a form. Each question delivers value immediately after. The AI reveals the picture as it builds.

**Screen type:** Hybrid (fixed "Building Your Picture" progress indicator pinned above + generative conversation stream below)

> [!NOTE]
> **§7 Q1 Resolution (DD06):** The Assessment is conducted **entirely within the conversational stream**. No beat uses a dedicated composed surface. The stream's inline components (option chips, confirm chips, range pickers, the progress indicator) provide all the structured interaction needed. A composed surface would break conversational flow, fragment attention, and force a jarring modality switch mid-assessment. The "Building Your Picture" indicator is a pinned hybrid element above the stream — not a separate surface. See [DD06](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD06-assessment-stream-primary.md) for the full decision record.

---

#### §BYP — "Building Your Picture" Progress Indicator

> **§7 Q2 Resolution:** The progress indicator uses a **dimension-pill progressive reveal** pattern, not a live numeric update. The running EF target estimate appears only after Beat 1 delivers its first "value after" — before that, the indicator shows dimensional coverage only.

**Design:**

The indicator is a **pinned horizontal strip** that sits between the top navigation bar and the conversational stream. It is always visible during Assessment, scrolling with the app chrome (not with the conversation).

**Visual anatomy:**

```
┌─────────────────────────────────────────────────────────────┐
│  Building Your Picture                                       │
│  [Income ✓] [Dependents ○] [Protection ○] [Obligations ○]   │
│  [City ○] [Age ○] [Health ○] [Household ○]                  │
│                                    Your range: — months      │
└─────────────────────────────────────────────────────────────┘
```

**Behavior:**

| State | What the indicator shows |
|:---|:---|
| **Assessment start** | 8 empty pills (dimension labels visible but greyed). "Your range: — months" (em-dash placeholder). |
| **Dimension captured** | The corresponding pill fills with a subtle animated pulse (colour fill, 300ms ease-in). A small ✓ replaces the ○. |
| **After Beat 1 "value after"** | "Your range" updates to show the first estimate: e.g., "Your range: 6–8 months". This is the first time a number appears. The animation is a **count-up** from — to the range (e.g., "—" → "6–8 mo"), taking 600ms. |
| **After each subsequent answer** | The range narrows or shifts. Each update triggers a **smooth number morph** (old number → new number, 400ms). If the range moves UP, the indicator briefly pulses a warm amber hue. If DOWN, a cool blue-green. No alarm coloring — this is informational, not evaluative. |
| **After Beat 3 final answer** | Range collapses to a single number: "Your target: 8 months (₹4.2L)". The pills are all ✓'d. A brief celebratory micro-animation (confetti-subtle, not loud) signals completion. |
| **D9 factor added** | If D9 adjusts the number: a 9th pill appears labeled "Your situation" and fills. The number updates with the same smooth morph. |

**Why this design:**
- **Dimension pills** give the user a sense of progress and completeness without revealing a premature number. Showing a running EF target from Step 1 would anchor the user too early (see behavioral framework §1.3 — Anchoring) and create anxiety if the number jumps around.
- **The number appears only after value is delivered** (Beat 1's "based on your employment type, people like you need X–Y months"). By that point the user understands WHY the number is what it is — it's not a mystery box.
- **Smooth morph + colour pulse** makes the number feel alive and responsive to the user's inputs. Each answer visibly changes the picture — reinforcing that this is personalised, not a generic formula.
- **Range → single number** progression mirrors the AI's increasing confidence as more dimensions are captured.

**Voice narration of indicator:**
- AI does NOT narrate every indicator update. The visual carries the detail.
- AI narrates the first appearance of the number (Beat 1 "value after"): "Based on your employment type, I'd start you at around 6–8 months. Let's see how your other factors adjust that."
- AI narrates the final number (Beat 3 conclusion): "Here's what all of that adds up to — 8 months, which is about ₹4.2L for your situation."

---

#### Assessment — Beat-by-Beat Interaction Spec

##### Beat 1 — Income Profile (Dimensions 1, 7)

**Purpose:** Establish the baseline — employment stability and household income structure. These two dimensions set the foundational months-range before other factors adjust it.

**Step 2.1 — Employment Type**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "What's your primary income source — salaried job, freelance or contract work, or running your own business?" |
| **Screen** | AI message + 3 inline option chips: `Salaried` · `Freelance / Contract` · `Own business` |
| **Confirmation** | §C1 (Light Confirm). User taps or speaks → option highlights → AI echoes: "Salaried — got it." → auto-proceeds. |
| **Voice input** | "I'm salaried" / "I do freelance work" / "I run a small business" → AI maps to option (§C1). |
| **Indicator update** | "Income" pill: ○ → ✓ (first half — employment type captured). |

**Step 2.2 — Employer Stability / Income Pattern (Adaptive Follow-up)**

This step branches based on the Step 2.1 selection:

| If salaried | If freelance/contract | If own business |
|:---|:---|:---|
| **AI:** "What type of organisation? Government or PSU, large private company, startup, or mid-size company?" | **AI:** "Is the work fairly regular — consistent projects month to month — or does income tend to be lumpy?" | **AI:** "Has the business been running for more than 3 years, or is it newer?" |
| **Options:** `Govt / PSU` · `Large private` · `Startup` · `Mid-size company` | **Options:** `Regular / Consistent` · `Lumpy / Variable` | **Options:** `3+ years, stable` · `Under 3 years / variable` |
| **Confirmation:** §C1 | **Confirmation:** §C1 | **Confirmation:** §C1 |

**Step 2.3 — Household Income Structure (Dimension 7)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Is yours the only income in your household, or does someone else earn too?" |
| **Screen** | AI message + 3 option chips: `Just me` · `Dual income` · `Multiple earners` |
| **Confirmation** | §C1 (Light Confirm). |
| **Indicator update** | "Household" pill: ○ → ✓. |

**Step 2.4 — Beat 1 Value-After (First Number Reveal)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Got it. Based on [your employment type and household structure], most people in similar situations start with a [X–Y] month baseline. Let's see how your other factors adjust this." |
| **Screen** | AI message bubble. The "Building Your Picture" indicator animates: "Your range: —" → "Your range: X–Y months" with count-up animation (600ms). Income and Household pills both show ✓. |
| **Example (New Parent):** | "Based on a salaried role at a large private company with dual income, most similar households start with a 5–6 month baseline." Indicator: "Your range: 5–6 mo" |
| **Example (Sandwich Gen):** | "Running your own business — especially under 3 years — and being the sole earner puts you at the higher end. I'd start with 9–10 months as a baseline." Indicator: "Your range: 9–10 mo" |

**Why value-after matters here:** The user has given 3 low-sensitivity data points and immediately sees a personalised range. This is the trust-building moment (behavioral framework §6.2 — Value-Before-Ask). The AI has earned the right to ask the more sensitive questions in Beat 2.

---

##### Beat 2 — Responsibility & Protection (Dimensions 2, 3, 4)

**Purpose:** Map the user's dependency load, insurance coverage, and fixed obligations. This is the densest beat — the most questions, the most numeric inputs (§C2), and the highest sensitivity. Voice handling is critical.

**Step 2.5 — Dependency Mapping**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Who depends on your income? Tell me about your household — it helps me gauge how much your fund needs to cover." |
| **Screen** | AI message + structured multi-select chips: `Spouse / Partner` · `Children (young)` · `Children (school-age)` · `Aging parents` · `Other dependents` · `Just me` |
| **Interaction** | Multi-select — user can tap multiple chips. Each lights up on tap. |
| **Confirmation** | §C1 per chip (each one highlights, AI doesn't echo every single one — echoes the summary after selection is done). |
| **Voice input** | "I have a baby and my mother-in-law lives with us" → AI maps to `Children (young)` + `Aging parents`, highlights both. AI echoes: "An infant and an aging parent — understood." (§C1) |
| **Indicator update** | "Dependents" pill: ○ → ✓. |

**Step 2.5a — Adaptive Dependency Follow-ups**

Based on selections in Step 2.5, the AI asks targeted follow-ups:

| If "Aging parents" selected | Follow-up |
|:---|:---|
| **AI** | "Are your parents covered by health insurance?" |
| **Options** | `Yes, adequate cover` · `Some cover, not sure if enough` · `No health insurance` |
| **Confirmation** | §C1 |
| **If "No health insurance"** | AI plants a flag (not a blocker): "Noted — I'll factor that into your safety net. We should also talk about getting them covered separately, but first things first." |

| If "Children" selected | Follow-up |
|:---|:---|
| **AI** | "School-age or younger?" (if not already specified by the multi-select) |
| **Options** | `Pre-school / infant` · `School-age` · `College-age` |
| **Confirmation** | §C1 |

**Step 2.6 — Income Capture**

> This is the first numeric input in the assessment. §C2 (Active Confirm) applies — this is a critical anchoring number.

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Roughly, what's your monthly take-home income? A range is fine — I'm not asking for your exact CTC." |
| **Screen** | AI message + a **range picker slider** with pre-set ranges: `Under ₹30K` · `₹30K–50K` · `₹50K–80K` · `₹80K–1.2L` · `₹1.2L–2L` · `₹2L+`. The user can also type a specific number. |
| **Voice input** | User says "About 1.2 lakh" → number appears as a **confirm chip**: `₹1,20,000/mo` with ✓ and ✏️ affordances. AI echoes: "₹1.2 lakh a month — that right?" Flow **pauses** until user confirms (§C2). |
| **Text input** | User taps the range or types a number → same confirm chip appears (§C2). |
| **§C2 behavior** | Per §0B §C2. Flow-blocking. Edit via ✏️ affordance. |
| **Why explain the ask** | Per behavioral framework §6.2 — contextual justification footnote before sensitive data asks. |

**Step 2.7 — Insurance Coverage**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "How about health insurance — do you have a policy? Roughly what cover?" |
| **Screen** | AI message + structured options: `No cover` · `Under ₹5L` · `₹5L–10L` · `₹10L+` · `Not sure` |
| **Confirmation** | §C1 (Light Confirm — categorical). |
| **Indicator update** | "Protection" pill: ○ → ✓. |
| **If "No cover" or "Under ₹5L"** | AI triggers the insurance advisory flag (from philosophy §3): "Having no adequate health insurance significantly increases your emergency risk. I'll size your fund to account for that — but I'd strongly suggest looking into at least a ₹5L cover in parallel. That's a separate conversation, though — let me finish your safety net first." |

**Step 2.8 — Fixed Obligations**

> Second major numeric input. §C2 (Active Confirm) applies.

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Ballpark: how much of your monthly income goes to non-negotiables? Things like rent or EMI, school fees, insurance premiums, loan payments." |
| **Screen** | AI message + **slider with ₹ amount** (range: ₹0 to user's stated income). Pre-anchored at 50% of stated income (per philosophy §3: Indian households often spend ~60-67% on needs [as of 2024, NHA/household expenditure surveys]). Slider has labeled markers at 30%, 50%, 70%. User adjusts. |
| **Voice input** | "About 60 thousand" → **confirm chip** appears: `₹60,000/mo in fixed obligations` with ✓ and ✏️ (§C2). AI echoes: "₹60K a month in fixed costs — does that sound right?" Flow pauses. |
| **Text input** | User can type a number instead of using the slider. Same §C2 confirm chip. |
| **Indicator update** | "Obligations" pill: ○ → ✓. |

**Step 2.9 — Beat 2 Value-After ("Your Safety Net So Far")**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Here's what I'm seeing so far — your picture is getting clearer." |
| **Screen** | The "Building Your Picture" indicator updates: range narrows based on Beat 2 data. E.g., "Your range: 5–6 mo" → "Your range: 7–8 mo" (smooth morph, 400ms). If the number went UP, the indicator pulses warm amber briefly. |
| **Inline insight** | Below the AI message, a **"Your Safety Net So Far" inline card** appears briefly showing the key factors captured so far and their directional impact: |

**"Your Safety Net So Far" card layout:**

```
┌──────────────────────────────────────────────┐
│  Your Safety Net So Far                       │
│                                               │
│  Salaried, large private        → 5 mo base  │
│  Single infant dependent        → +1 mo      │
│  Mother-in-law (no insurance)   → +1 mo      │
│  High fixed obligations (55%)   → +0.5 mo    │
│                                  ──────────── │
│  Running estimate:               7–8 months   │
│                                               │
│  A few more questions to sharpen this ↓       │
└──────────────────────────────────────────────┘
```

| Aspect | Spec |
|:---|:---|
| **Card type** | Inline in stream. Not tappable for edit (edits happen at §C3 gate). Read-only progress visualization. |
| **Voice** | AI does NOT read the card aloud. AI says only: "A few more questions and we'll have your full picture." |
| **Delight moment** | The card materializes with a subtle slide-up animation (200ms). Seeing the logic visually — each factor with its directional arrow — is the trust-building "Show Your Work" moment (behavioral framework §5). |

---

##### Beat 3 — Context & Risk (Dimensions 5, 6, 8)

**Purpose:** Capture geographic context, life stage, and health profile. Lower friction than Beat 2 — these are quick categorical inputs (mostly §C1) with one sensitive question (health).

**Step 2.10 — City / Cost of Living (Dimension 5)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Which city are you in? Or if it's not a metro, which tier — Tier 2 or Tier 3?" |
| **Screen** | AI message + search-enabled city selector with tier groupings: `Metro (Mumbai, Delhi, Bangalore, Hyderabad, Chennai, Pune, Kolkata)` · `Tier 2 (Jaipur, Lucknow, Chandigarh, Coimbatore, etc.)` · `Tier 3 / Semi-urban`. User can type city name or select tier. |
| **Confirmation** | §C1 — city highlights, AI echoes: "Bangalore — got it." |
| **Voice input** | "I'm in Bangalore" → AI maps to Metro, highlights Bangalore (§C1). |
| **Indicator update** | "City" pill: ○ → ✓. |

**Step 2.11 — Age Bracket (Dimension 6)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "And roughly, how old are you?" |
| **Screen** | AI message + 5 bracket chips: `20s` · `30s` · `Early 40s` · `Late 40s` · `50s+` |
| **Confirmation** | §C1. |
| **Voice input** | "I'm 34" → AI maps to "30s" bracket. §C1: highlights "30s" chip. AI echoes: "Early 30s — got it." |
| **Indicator update** | "Age" pill: ○ → ✓. |

**Step 2.12 — Health Profile (Dimension 8) — Sensitive Data**

> Health is the most sensitive input in the assessment. Per §0B voice handling for sensitive data: AI offers text fallback explicitly. AI does NOT verbally echo health data — screen-only confirmation.

| Aspect | Spec |
|:---|:---|
| **AI voice** | "One more — are there any ongoing health conditions in your family that might mean higher-than-usual medical costs? You can type this if you'd rather not say it aloud." |
| **Screen** | AI message + 3 options: `Yes, some conditions` · `No, generally healthy` · `Prefer not to say`. Plus a text input field offered as an alternative to voice/tap. |
| **Confirmation** | §C1 for "No" and "Prefer not to say". For "Yes" — AI asks a gentle follow-up (see below) but does NOT verbally echo any specifics. Screen shows the selection chip only. |
| **If "Yes"** | AI follow-up (text on screen, NOT spoken aloud): "Is it something that requires regular medication or treatment? That helps me estimate the medical buffer." Options: `Ongoing / chronic` · `Occasional / managed` · `Resolved but flagging it` |
| **If "Prefer not to say"** | AI responds: "Totally fine. I'll use a conservative estimate for your medical buffer — you can always refine this later." → Uses "uninsured dependent + chronic condition" buffer as the cautious default. |
| **Indicator update** | "Health" pill: ○ → ✓. |
| **Privacy note** | Health data is labeled as SENSITIVE in the data model. Never echoed verbally. Never displayed in summary cards beyond "Health profile: factored in" without details. |

**Step 2.13 — Beat 3 Value-After (Dimension Set Complete)**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "That covers it. Here's what all of this adds up to—" |
| **Screen** | The "Building Your Picture" indicator completes: all 8 pills show ✓. The range collapses to a single number with the count-up animation: "Your range: 7–8 mo" → "Your target: 8 months (₹4.2L)". Micro-celebration animation (subtle pill shimmer, not flashy). |
| **Transition** | This is a natural pause point. The AI does NOT immediately launch into the Target Setting reveal. Instead, the §C3 Summary Confirm fires (see Step 2.15 below). |

---

##### Beat 4 — Open Dimension (D9: User-Surfaced Context)

> Beat 4 is NOT a sequential step — it triggers **asynchronously** at any point during Beats 1–3 when the user volunteers information outside the 8 dimensions.

**Trigger conditions:**

| Signal | Example | AI response |
|:---|:---|:---|
| User volunteers extra context mid-answer | "I also send ₹15K to my brother every month for his education" | "That's important — regular financial support to a sibling is a real obligation. I'll factor that in as an additional dependency." |
| User proactively asks about an uncovered factor | "What about my car loan?" | AI checks: is this already captured in "fixed obligations" (Step 2.8)? If yes: "That should be included in your fixed obligations number — did you count it there?" If no: "Good catch — let me add that to your picture." |
| User mentions a pending life event | "We have a legal case going on that might cost money" | "That's the kind of thing your fund should be ready for. I'll add a conservative buffer for it — better to have it covered than not." |

**Processing:**

| Aspect | Spec |
|:---|:---|
| **AI response** | Acknowledge → brief clarifying follow-up if needed → incorporate as contextual adjustment. |
| **Indicator** | A 9th pill appears: `Your situation` (or a short label derived from the context, e.g., "Sibling support"). Pill fills with ✓. |
| **Target impact** | Conservative adjustment: +0.5 to +2 months depending on scale. Labeled honestly in the attribution strip as "+ [N] months (your specific situation: [description])". |
| **What the AI does NOT do** | It does not create a new structured question or form field. It does not ask "\On a scale of 1-10, how serious is this?" It responds conversationally and makes a judgment call on buffer size. |

---

#### Assessment — Confirmation Gate (§C3)

**Step 2.15 — Summary Confirm at Assessment → Target Setting Boundary**

> This is the §C3 (Summary Confirm) that fires at the exit of Phase 2, before the AI uses the captured data to reveal the personalised target in Phase 3.

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Before I show you your number — let me make sure I've got everything right." |
| **Screen** | A **Summary Card** materializes in the stream. It shows all captured data as a scannable list: |

**Summary Card layout:**

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
│  [+ Sibling support: ₹15K/mo — if D9 was triggered]  │
│                                                       │
│  ┌─────────────┐  ┌──────────────┐                    │
│  │  Looks right │  │ Let me adjust │                   │
│  └─────────────┘  └──────────────┘                    │
└──────────────────────────────────────────────────────┘
```

| Aspect | Spec |
|:---|:---|
| **Each row** | Tappable → triggers §C4 (Edit-in-Place). User taps "Income" → inline editor appears with ₹ field → user corrects → chip updates → downstream recalculation. |
| **"Looks right" CTA** | Primary button. User taps → AI proceeds to Phase 3 (Target Setting). |
| **"Let me adjust" CTA** | Secondary button. User taps → the first row highlights with an edit affordance, inviting the user to tap whichever row they want to change. |
| **Voice** | User can say "Looks right" or call out a correction: "Actually, my income is more like 1.3 lakh" → §C2 confirm chip for the new number → card updates → user re-confirms. |
| **AI narration** | AI says only: "Take a look — does everything check out?" AI does NOT read the card aloud. |
| **Health row** | Shows "Factored in" — NOT the specific condition. Per privacy handling in Step 2.12. |
| **Flow pause** | Flow **waits** for explicit CTA tap or voice confirmation before proceeding. This is deliberate friction at the phase gate (per tension T5). |

---

#### Phase 2 — Adaptive Variations (Full Assessment Walkthrough)

##### New Parent Archetype — Full Assessment Path

> **Profile:** 34F, salaried (IT, large pvt), ₹14L/yr (~₹1.17L/mo take-home), infant + mother-in-law (no health insurance), Tier 2 city, dual income household.

| Step | Interaction | Outcome |
|:---|:---|:---|
| **2.1** Employment | Selects "Salaried" | Income pill half-filled |
| **2.2** Employer stability | Selects "Large private" | Income pill: ✓ |
| **2.3** Household income | Selects "Dual income" | Household pill: ✓ |
| **2.4** Beat 1 Value-After | AI: "Salaried at a large private company with dual income — that's the most stable combo. I'd start at 5–6 months." | Indicator: "Your range: 5–6 mo" |
| **2.5** Dependencies | Taps: `Children (young)` + `Aging parents` | Dependents pill: ✓ |
| **2.5a** Parent insurance | Selects "No health insurance" → AI flags | Flag planted |
| **2.5a** Child age | Already specified as "young" (infant) via multi-select | Already captured |
| **2.6** Income | Types or speaks "About 1.2 lakh" → §C2 confirm chip: `₹1,20,000/mo` → taps ✓ | Income captured |
| **2.7** Insurance | Selects "₹5L–10L" | Protection pill: ✓ |
| **2.8** Fixed obligations | Adjusts slider to ₹60K → §C2 confirm chip → taps ✓ | Obligations pill: ✓. Indicator: "Your range: 7–8 mo" (↑ amber pulse) |
| **2.9** Beat 2 Value-After | "Your Safety Net So Far" card shows: salaried base → +1 (infant) → +1 (uninsured parent) → +0.5 (high fixed costs). Running estimate: 7–8 mo. | Card materializes |
| **2.10** City | Types "Coimbatore" or selects "Tier 2" | City pill: ✓ |
| **2.11** Age | Taps "30s" | Age pill: ✓ |
| **2.12** Health | Selects "No, generally healthy" | Health pill: ✓ |
| **2.13** Beat 3 conclusion | AI: "That covers it." Indicator collapses: "Your target: 7 months (₹4.9L)" | All pills ✓ |
| **2.15** §C3 Summary | Summary card shows all data. She reviews, taps "Looks right" | Gate passed → Phase 3 |

**Key moments for New Parent:**
- Uninsured mother-in-law is the biggest upward factor (+1 mo). Dual income is the biggest hedge (lower baseline). Infant adds a paediatric buffer (+0.5–1 mo).

##### Sandwich Generation Archetype — Full Assessment Path

> **Profile:** 42M, SME owner (variable income), ₹22L/yr (~₹1.83L/mo), 2 school-age kids + aging parents (one cardiac patient, no insurance), single earner, metro.

| Step | Interaction | Outcome |
|:---|:---|:---|
| **2.1** Employment | Selects "Own business" | Income pill half-filled |
| **2.2** Business stability | Selects "Under 3 years / variable" | Income pill: ✓ |
| **2.3** Household income | Selects "Just me" | Household pill: ✓ |
| **2.4** Beat 1 Value-After | AI: "Running your own business, under 3 years, and being the sole earner — that puts you at the higher end. I'd start at 9–10 months." | Indicator: "Your range: 9–10 mo" |
| **2.5** Dependencies | Taps: `Children (school-age)` + `Aging parents` | Dependents pill: ✓ |
| **2.5a** Parent insurance | Selects "No health insurance." AI: "Noted. With a cardiac condition on top of no insurance, that's a significant risk factor. I'll size your fund accordingly." | Flag planted, heightened |
| **2.5a** Follow-up | AI senses high-sensitivity moment. Adds: "Getting them a health policy would be the single most impactful thing you could do for your family's financial safety. But let's build the safety net first." | Insurance advisory |
| **2.5b** D9 TRIGGERED | He volunteers: "I also support my brother's family — about ₹20K a month." | AI: "That's a real obligation. I'll factor it in." 9th pill appears: "Sibling support" ✓ |
| **2.6** Income | Says "Income is variable — good months it's 2 lakh, bad months maybe 1 lakh." → AI suggests using ₹1.5L as the working number → §C2 confirm chip: `₹1,50,000/mo (working estimate)` → taps ✓. | Income captured |
| **2.7** Insurance | Selects "Under ₹5L" for himself. AI flags the gap: "₹5L of cover for a family of 6 with a cardiac patient is significantly under-covered." | Protection pill: ✓ |
| **2.8** Fixed obligations | Says "About 80 thousand — EMI, school fees, insurance, and the sibling support." → §C2 confirm chip: `₹80,000/mo` → taps ✓. AI: "That's about 53% of your working income going to non-negotiables." | Obligations pill: ✓. Indicator: "Your range: 10–12 mo" (↑ amber) |
| **2.9** Beat 2 Value-After | Card shows: own business base → +1 (single earner) → +1 (2 school-age kids) → +2 (aging parent, cardiac, no insurance) → +1 (sibling support [D9]) → moderate pull from fixed costs. Running: 10–12 mo. | Card materializes |
| **2.10** City | Says "Mumbai" | City pill: ✓ |
| **2.11** Age | Taps "Early 40s" | Age pill: ✓ |
| **2.12** Health | AI asks. He types (not speaks): "Father has had a bypass surgery. Mother is diabetic." → AI processes silently, does NOT echo details. AI responds on screen: "Understood — I've factored in higher medical buffer for your parents. This stays private." | Health pill: ✓ |
| **2.13** Beat 3 conclusion | Indicator: "Your target: 12 months (₹10.8L)" | All pills ✓ |
| **2.15** §C3 Summary | Summary card. He reviews — notices income might be understated. Taps "Income" row → edits to ₹1.6L → indicator recalculates live: "Your target: 12 months (₹11.5L)". Taps "Looks right." | Gate passed → Phase 3 |

**Key moments for Sandwich Generation:**
- D9 triggers naturally mid-conversation (sibling support volunteered at Step 2.5b). The AI incorporates it without a special form field — this is the cleanest example of how D9 works.
- Health data is entered via text (not voice). The AI's proactive text fallback offer at Step 2.12 is essential for sensitive parental health details.

---

#### Phase 2 — Re-Entry Adaptations

##### Scenario B in Assessment (User with Existing Savings)

| Adaptation | Spec |
|:---|:---|
| **Additional early question** | After Beat 1 "value after," AI asks: "Roughly how much have you set aside so far?" → §C2 confirm chip for the number. This pre-fills the Phase 3 gap analysis. |
| **Tone shift** | Assessment framing is "health check" not "build from scratch." AI says: "Let me see if what you have matches what your situation needs." |
| **Phase 3 output** | Target Reveal includes a comparison: "Your target is ₹4.2L. You have ₹2.5L — you're 60% there. Here's what the gap covers…" |

##### Scenario C in Assessment (User with Known Target)

| Adaptation | Spec |
|:---|:---|
| **User agreed to validation** | Assessment runs normally. At Phase 3, AI compares: "Your estimate of ₹5L was close — I'd suggest ₹5.8L because [attribution]. Want to go with mine or stick with yours?" |
| **User bypassed Assessment** | Per §0B Scenario C (decline path): Assessment skipped, user's target accepted, proceed to Phase 4. |

##### Scenario E in Assessment (User with Existing EF)

| Adaptation | Spec |
|:---|:---|
| **Pre-filled data** | If user already stated breakdown ("₹3L in savings, ₹2L in FD"), these pre-fill the Phase 3 gap analysis AND the Phase 4 allocation review. |
| **Assessment may be shortened** | If user's initial description includes enough signals ("I'm 38, salaried, two kids"), AI may pre-fill some dimension pills and confirm: "You mentioned you're salaried with two kids — let me confirm a few more details and we can check your fund." |
| **Phase 3 output** | Gap analysis or surplus: "You have ₹5L total — based on your profile, I'd recommend ₹6.2L. You're at 81%." OR "You're actually a bit over-funded at 110% — the extra is working less hard than it could elsewhere." |

---

#### Phase 2 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **"Building Your Picture" Progress Indicator** | Pinned strip with dimension pills + running number | Hybrid (fixed element pinned above the stream) | 8 pills + optional 9th (D9). Animated count-up and smooth morph for number updates. Amber/blue-green pulse on directional changes. |
| **Confirm Chip (§C2)** | Inline editable number display with ✓ and ✏️ | Generative (inline in stream) | Used for income, fixed obligations, and any numeric voice input. Flow-blocking until confirmed. |
| **Light Confirm Highlight (§C1)** | Option chip state: selected/highlighted | Generative (inline in stream) | Visual state change for tapped/voice-selected categorical options. Non-blocking. |
| **Range Picker / Slider** | Slider with ₹ amounts and labeled markers | Generative (inline in stream) | Used for income (range picker) and obligations (slider). Pre-anchored to sensible defaults. |
| **Multi-Select Chip Set** | Group of toggleable chips | Generative (inline in stream) | Used for dependency mapping (Step 2.5). Multi-select mode. |
| **City Selector** | Search-enabled grouped selector | Generative (inline in stream) | Metro cities listed individually; Tier 2/3 as groups with search. |
| **"Your Safety Net So Far" Card** | Read-only inline card with factor attribution | Generative (inline in stream) | Shows at Beat 2 value-after. Slide-up animation. Not editable (edits at §C3 gate). |
| **§C3 Summary Card** | Editable summary card with two CTAs | Generative (inline in stream) | All 8+ data points listed. Each row tappable for §C4 edit-in-place. "Looks right" / "Let me adjust" CTAs. Flow-blocking. |
| **§C4 Edit-in-Place** | Inline editor (text, range, or category) | Generative (inline in stream) | Triggered from §C3 Summary Card rows. Matches original input method. Downstream recalculation on edit. |
| **Insurance Advisory Flag** | Inline advisory message (non-blocking) | Generative (inline in stream) | Appears when user has no/inadequate health insurance. Not a page or modal — an inline message in the conversation. |
| **Sensitive Data Text Fallback** | Text input field with privacy framing | Generative (inline in stream) | Offered for health data (Step 2.12). AI does not echo content verbally. |

---

### Phase 3: Target Setting ("Why This Number") — Full Interaction Spec

**User question:** "What's my number? And why?"

**AI's job:** Reveal the personalized target with full dimension attribution. Make the number feel inevitable and personal — not arbitrary. This is CarrotFin's signature trust-building moment — the user sees the AI's reasoning laid bare.

**Modality:** Conversation → Visual pivot (per `interaction-model.md`, "Decide" modality). Stream sets up the reveal. Visual component materializes inline with the number. The AI narrates while the screen renders — simultaneous, not sequential (§0B Rule 3).

**Screen type:** Hybrid (conversational reveal + inline Target Card with attribution strip)

**Trust level at entry:** Warming. The user has invested 3–5 minutes in Assessment, shared sensitive data, confirmed it at §C3. The AI has delivered two "value after" moments. Phase 3 is where this earned trust converts to perceived competence — the AI proving it did something meaningful with the data.

> [!NOTE]
> **Design Decision:** [DD07](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD07-attribution-strip-design.md) — Attribution Strip uses a **tiered linear arrow diagram** (vertical stack) as default, with a plain-text conversational fallback for low-literacy users. Partially resolves T7 for Phase 3.

---

#### Phase 3 — Step-by-Step Interaction Spec

> **Prerequisite:** The user has passed the §C3 Summary Confirm gate at the end of Phase 2. All 8 dimensions (+ optional D9) are captured and confirmed. The "Building Your Picture" indicator shows the resolved single number (e.g., "Your target: 7 months (₹4.9L)"). Phase 3 receives a clean, confirmed data set.

**Step 3.1 — Conversational Setup (The Reveal Beat)**

> The AI builds a brief narrative pause before the Target Card materializes. This beat is emotional pacing — the AI does NOT jump straight to the visual. The pause creates a "moment" that distinguishes the reveal from a calculator output.

| Aspect | Spec |
|:---|:---|
| **AI voice** | Speaks the setup line aloud. Tone: confident, warm, slightly deliberate — like an advisor who has finished their analysis and is about to deliver the verdict. Not rushed. |
| **AI text** | "Here's what all of that adds up to — and why your number is what it is." |
| **Screen** | AI message bubble appears in the stream. A brief pause (800ms) follows before the Target Card materializes — this is not a loading delay, it's pacing. The "Building Your Picture" indicator remains pinned above with the number already visible (from §C3 close). |
| **Voice-off mode** | AI text appears; same 800ms pause before card. No information lost. |

**Step 3.2 — Target Card Materialization**

> The Target Card materializes inline in the conversational stream — not as a separate composed surface.

| Aspect | Spec |
|:---|:---|
| **Animation** | The card slides up from below with a smooth ease-out (300ms). The headline number renders first, then the attribution strip fills in row by row with a 100ms stagger between rows (top to bottom). Total card build time: ~800ms. The staggered build makes the logic feel like it's being "assembled" — reinforcing that the AI computed something, not looked up a table. |
| **AI voice (simultaneous)** | While the card animates, the AI speaks: "Your emergency fund target is [₹X lakh] — that's [N] months of your essential expenses." The AI narrates **only the headline**, not the attribution strip. The visual carries the detail (§0B Rule 3). |
| **Screen — Target Card layout** | See layout below. |

**Target Card layout:**

```
┌──────────────────────────────────────────────┐
│                                              │
│  🛡️  Your Emergency Fund Target               │
│                                              │
│        ₹4.9L                                 │
│        7 months of essential expenses        │
│                                              │
│  ── How we got to your number ──────────── │
│                                              │
│  Salaried, large private      → 5 mo base   │
│          ↓                                   │
│  + Infant dependent           → +1 mo       │
│          ↓                                   │
│  + Uninsured parent           → +1 mo       │
│          ↓                                   │
│  − Dual income household      → −0.5 mo     │
│          ↓                                   │
│  + High fixed costs (50%)     → +0.5 mo     │
│          ═══════════════                     │
│  Your target                    7 months     │
│                                 ₹4.9L        │
│                                              │
│  [Why is this different from "3–6 months"?]  │
│                                              │
│  ┌─────────────────┐  ┌──────────────────┐  │
│  │  Looks right ✓   │  │  Adjust an answer │  │
│  └─────────────────┘  └──────────────────┘  │
└──────────────────────────────────────────────┘
```

**Attribution strip — Phase 3–specific additions:**

> Full design rules (layout, colour coding, collapse behaviour, row tappability, literacy fallback) are in [DD07](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD07-attribution-strip-design.md). Below are Phase 3–specific interaction details only.

| Aspect | Spec |
|:---|:---|
| **Animation** | Rows fill in with 100ms stagger (top to bottom) during Target Card materialization. Total strip build: ~500ms. |
| **Voice** | The AI does NOT read the attribution strip. It narrates only the headline number. The strip is visual-only — screen carries the detail (§0B Rule 3). |
| **D9 row** | If D9 was triggered, it appears as the last factor row before the total, labeled with its specific context (e.g., "Sibling support (₹20K/mo) → +1 mo"). |
| **Level 3 data sources** | Second-level row expand reveals formula/data source (§5.2 Level 3). All data claims must be grounded — e.g., paediatric emergency cost differentials [source TBD for BuildSpec]. |

**Step 3.3 — "Why This Differs" Myth-Busting (Conditional)**

> Triggers ONLY when the revealed target differs materially from the "3–6 months" generic advice. Per philosophy §7: "At target-reveal, show why their number differs from 'the usual advice'."

| Aspect | Spec |
|:---|:---|
| **Trigger condition** | Target is ≥7 months (exceeds the upper bound of generic "3–6 months" advice) OR target is ≤3 months (below common advice — rare but possible for metro single professionals with minimal obligations). |
| **AI voice** | Speaks the myth-bust after a brief pause (400ms) following the card animation: "You may have heard that 3 to 6 months is enough. For most people, it isn't — here's why yours is different." |
| **Screen** | The tappable link within the Target Card — "[Why is this different from '3–6 months'?]" — auto-expands if the myth-bust triggers. Shows a brief inline explanation: |
| **Expanded content** | "The '3–6 months' rule assumes a salaried dual-income household with health insurance, no aging dependents, and a metro job market. Your situation has [specific diverging factors] — that's why your number is [N] months." The specific factors are pulled from the attribution strip's top contributors. |
| **If NOT triggered** | The "[Why is this different…]" link remains in the card as a tappable affordance but does NOT auto-expand. User can tap it anytime. |
| **Voice-off mode** | Same content in AI message bubble. No information lost. |

**Step 3.4 — "Why This Number" Key Insight (AI Narration)**

> After the card has materialized and the optional myth-bust has played, the AI delivers ONE plain-English insight — the single most important thing the user should take away. This is the emotional anchor.

| Aspect | Spec |
|:---|:---|
| **AI voice** | Speaks the insight aloud. Tone: confident, direct, personal. This is the AI's editorial voice — not reading data, but interpreting it. |
| **Content selection logic** | The AI picks the **single most unusual or high-impact factor** from the attribution strip. Not the largest adjustment — the most *surprising* one relative to the user's likely expectations. |
| **Example (New Parent)** | "The biggest thing people miss is the uninsured parent. Your mother-in-law has no health cover — if she needs hospitalisation, that comes straight from your pocket. That single factor adds a full month to your target." |
| **Example (Sandwich Gen)** | "Being the sole earner for six people is the defining factor. If your income stops for any reason, there's no second salary to absorb the impact. That's why your baseline is so much higher than someone with a working spouse." |
| **Screen** | AI message bubble in the stream, below the Target Card. No additional visual — the card carries the data, this message carries the emotional interpretation. |

**Step 3.5 — Target Card CTAs (User Response)**

| Aspect | Spec |
|:---|:---|
| **"Looks right ✓"** (primary CTA) | User accepts the target. AI responds: "Great — let's figure out the best way to structure this." → Proceeds to Step 3.6 (Social Obligation Buffer, if applicable) or Step 3.8 (Starter Shield, if applicable) or directly to Phase 4. |
| **"Adjust an answer"** (secondary CTA) | Triggers §C4 (Edit-in-Place). The §C3 Summary Card from Assessment re-renders inline. User taps the data point to edit → downstream recalculation → Target Card number updates with smooth morph animation (400ms). Attribution strip re-renders to reflect the change. AI narrates: "Updated. Let me recalculate…" → new number appears. |
| **Voice** | User can say "Looks right" / "That makes sense" → same as primary CTA. User can say "My income is actually higher" or "I forgot to mention X" → AI routes to §C4 edit flow. |
| **Flow pause** | Flow **waits** for explicit CTA action. This is deliberate friction at the Target Setting gate (per T5 — this is a life-impacting number). |

---

#### §3-D3 — Social Obligation Buffer (D3: Optional Post-Target Step)

> **§7 Q5 Resolution:** The social obligation buffer surfaces as a **separate, visually distinct prompt** after the user accepts the primary target. It animates in as a clearly secondary element — not an extension of the Target Card. The visual design communicates: "this is an add-on, not a correction to your core number."

**Trigger condition:** Always surfaces after Step 3.5 "Looks right" CTA. This is not gated by archetype — all Indian users get the offer. User can skip in one tap.

**Step 3.6 — D3 Prompt**

| Aspect | Spec |
|:---|:---|
| **Timing** | 600ms after the user taps "Looks right." Enough pause to register that the primary target is confirmed and locked. |
| **AI voice** | Speaks the prompt: "One more thing — and this is separate from your safety net." Deliberate verbal framing: "separate" is the critical word. |
| **AI text** | "Many Indian families also keep a small buffer for things like a wedding in the family, a ceremony, or community support. This isn't part of your emergency fund — it's a separate pot that keeps your EF intact for real emergencies. Want to add one?" |
| **Screen** | A **D3 Prompt Card** slides up below the Target Card (not inside it). Visual differentiation: |

**D3 Prompt Card layout:**

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

| Aspect | Spec |
|:---|:---|
| **Visual differentiation** | The D3 card uses a **different background tint** (soft cream/warm grey) from the Target Card (white/standard). A dotted or dashed border (not solid) reinforces that this is optional/supplementary. The 🎁 icon (or equivalent warm, non-urgent icon) signals "bonus" not "warning." |
| **Animation** | Slides up from below with ease-out (250ms). Appears BELOW the confirmed Target Card — the Target Card does not move, grow, or change. The spatial relationship communicates hierarchy: Target Card = primary (above), D3 = optional add-on (below). |
| **"Skip — not now"** | Secondary CTA. User taps → D3 card fades out (200ms). AI says: "No worries. You can always add one later." → Flow proceeds to Step 3.8 (Starter Shield check) or Phase 4. |
| **Voice** | User can say "No thanks" / "Skip" / "Not now" → same as skip CTA. User can say "Yes, add one" → Step 3.7. |

**Step 3.7 — D3 Buffer Sizing (If User Taps "Add a Buffer")**

| Aspect | Spec |
|:---|:---|
| **AI voice** | "Roughly, how much would a typical family obligation cost you? Think of the most common one — a wedding contribution, a ceremony, or supporting a relative." |
| **Screen** | AI message + inline range chips: `Under ₹25K` · `₹25K–50K` · `₹50K–1L` · `₹1L–2L` · `₹2L+`. User can also type a specific amount. |
| **Confirmation** | §C1 for range selection. If user types a number → §C2 confirm chip. |
| **AI follow-up** | "And how often does something like this come up — roughly once a year, a couple of times?" Options: `Once a year` · `2–3 times a year` · `More often` |
| **Calculation** | AI uses the midpoint of the selected range × midpoint of the frequency band = annual buffer. Divides by 12 for monthly contribution overlay. Example: ₹50K × 2/year = ₹1L annual → ₹8,300/mo additional. |
| **Result display** | The D3 card updates inline: |

**D3 Buffer — confirmed state:**

```
┌──────────────────────────────────────────────┐
│  🎁  Family Obligation Buffer                 │
│                                              │
│  ₹1.0L / year                               │
│  (₹50K × ~2 events/year)                    │
│                                              │
│  This is separate from your ₹4.9L safety net │
│  ┌──────────┐  ┌──────────┐                  │
│  │ Confirm ✓ │  │ Remove ✗  │                 │
│  └──────────┘  └──────────┘                  │
└──────────────────────────────────────────────┘
```

| Aspect | Spec |
|:---|:---|
| **Visual** | Buffer amount is shown clearly as a SEPARATE line item. The card explicitly states "This is separate from your ₹[X]L safety net" to prevent mental conflation with the core EF target. |
| **AI voice** | "Got it — ₹1 lakh a year as a family buffer, separate from your emergency fund. That way when a wedding invite shows up, your safety net stays intact." |
| **Confirm/Remove** | Confirm locks the buffer. Remove eliminates it (same as "Skip"). Either way, flow proceeds. |
| **Impact on Phase 4/5** | The buffer appears as a separate sub-fund in Phase 4 (Fund Architecture) and a separate contribution line in Phase 5 (Contribution Planning). It is NEVER added to the core EF target number. |

---

#### §3-SS — "Starter Shield" Framing for Large Targets

> The Starter Shield reframes a daunting number as an achievable first step. It prevents the **paralysis effect** (behavioral framework §1.2 — Present Bias): a ₹11.5L target feels so distant that the user may never start. The Starter Shield says: "You don't need to get there all at once. Your first month is the most important one."

**Trigger condition:** Target ≥ ₹5L **AND** at least one of:
- User is early-career (Age bracket: 20s or early 30s)
- User's monthly income is ≤ ₹80K (per Step 2.6 capture)
- Target-to-monthly-income ratio exceeds 6× (i.e., target is >6 months of take-home)

> The trigger is deliberately broad. The Starter Shield never hurts — even for users who don't strictly need it, seeing the milestone architecture is motivating. The only users who DON'T see it are those with small targets (<₹5L) where the full amount already feels achievable.

**Step 3.8 — Starter Shield Reveal**

| Aspect | Spec |
|:---|:---|
| **Timing** | After Step 3.5 "Looks right" + D3 resolution (accepted, skipped, or not triggered). Before transition to Phase 4. |
| **AI voice** | Speaks with a reassuring, motivating tone — NOT minimising the target, but reframing the journey: "That's a meaningful goal — and you don't need to build it all at once. The biggest win is your first month." |
| **AI text** | "Your first milestone is your **Starter Shield** — ₹[1 month of essential expenses]. Once you hit that, your most acute risk drops significantly. A single-month income gap won't derail your family." |
| **Screen** | The Target Card transforms — not replaces — with a **milestone overlay** that slides in below the headline number: |

**Starter Shield — Target Card overlay:**

```
┌──────────────────────────────────────────────┐
│                                              │
│  🛡️  Your Emergency Fund Target               │
│                                              │
│        ₹11.5L                                │
│        12 months of essential expenses       │
│                                              │
│  ── Your milestones ────────────────────── │
│                                              │
│  ★ Starter Shield     ₹96K   (1 month)  ←──│
│    3 Months Secure    ₹2.9L  (3 months)     │
│    Half-Year Shield   ₹5.8L  (6 months)     │
│    Fully Funded       ₹11.5L (12 months)    │
│                                              │
│  "A single-month gap won't derail            │
│   your family. Start here."                  │
│                                              │
│  [Got it — let's plan →]                     │
└──────────────────────────────────────────────┘
```

| Aspect | Spec |
|:---|:---|
| **Animation** | The attribution strip collapses (if still expanded) with a smooth fold-up (300ms). The milestone overlay slides in from below (300ms). The ★ Starter Shield row pulses once with a warm glow (amber, 400ms) — drawing the eye to the first milestone. The arrow indicator (←) marks the current focus. |
| **Milestone rows** | 4 milestones from philosophy §6: Starter Shield (1 mo), 3 Months Secure, Half-Year Shield, Fully Funded. Each shows the ₹ amount computed from the user's monthly essential expense (from Assessment obligations + income data). Greyed out except Starter Shield (active). |
| **AI voice (simultaneous)** | While the overlay animates: "Your Starter Shield is ₹[X]. That's one month of your family's essential expenses. Once you reach it, if something goes wrong, you have a full month before you'd need to touch anything else. That's the first win." |
| **Voice-off mode** | Same milestone overlay. AI text carries the Starter Shield framing in the message bubble. |
| **If Starter Shield NOT triggered** | The milestone overlay does NOT appear in Phase 3. Milestones are previewed later in Phase 5 (Contribution Planning) as part of the contribution timeline. The full target feels achievable enough that front-loading milestones would add unnecessary complexity. |
| **CTA** | "Got it — let's plan →" — transitions to Phase 4 (Fund Architecture). |

---

#### Phase 3 — Voice Handling Summary

> Phase 3 is a **modality pivot**: the AI's role shifts from questioner (Phase 2) to narrator (Phase 3). The user's role shifts from data provider to evaluator. Voice handling reflects this inversion.

| Beat | AI speaks | AI does NOT speak | Screen carries |
|:---|:---|:---|:---|
| **Step 3.1** (Setup) | "Here's what all of that adds up to — and why your number is what it is." | — | AI message bubble |
| **Step 3.2** (Target Card) | "Your emergency fund target is ₹[X]L — that's [N] months of your essential expenses." (headline only) | Does NOT read the attribution strip row by row. Does NOT read the card headings or labels. | Full Target Card with attribution strip. The visual is far richer than the narration — simultaneous rendering. |
| **Step 3.3** (Myth-bust) | "You may have heard that 3 to 6 months is enough. For most people, it isn't — here's why yours is different." (if triggered) | Does NOT read the expanded explanation. | Auto-expanded inline explanation within the card. |
| **Step 3.4** (Key insight) | One personalised editorial insight — the most surprising factor. | Does NOT repeat attribution data already shown on screen. | AI message bubble below the card. |
| **Step 3.5** (CTAs) | Silence. The user evaluates. AI speaks only if the user asks a question or initiates an edit. | Does NOT prompt or rush. | Target Card with CTAs. Flow pauses. |
| **§3-D3** (Buffer prompt) | "One more thing — and this is separate from your safety net." Then: the D3 prompt copy. | Does NOT read D3 card labels or layout elements. | D3 Prompt Card — visually distinct from Target Card. |
| **§3-SS** (Starter Shield) | "That's a meaningful goal — and you don't need to build it all at once. The biggest win is your first month." Then: "Your Starter Shield is ₹[X]…" | Does NOT read all milestone rows. Narrates only Starter Shield. | Milestone overlay with 4 rows. Only Starter Shield is active/highlighted. |

**Key voice principles for Phase 3:**
1. **AI is narrator, not reader.** The AI provides editorial commentary on the number. The screen provides the data. The two are complementary, not redundant.
2. **Silence is a tool.** At Step 3.5, the AI goes quiet. The user needs space to evaluate a significant number. Any AI filler ("Take your time!" / "What do you think?") would feel performative.
3. **Voice carries emotion, screen carries logic.** The AI's spoken insight (Step 3.4) is the emotional hook. The attribution strip (Step 3.2) is the logical proof. Together they build both trust and conviction.

---

#### Phase 3 — Adaptive Variations (Full Walkthrough)

##### New Parent Archetype — Full Phase 3 Path

> **Profile:** 34F, salaried (IT, large pvt), ₹14L/yr (~₹1.17L/mo take-home), infant + mother-in-law (no health insurance), Tier 2 city, dual income household.
> **Target entering Phase 3:** ₹4.9L / 7 months.
> **Attribution focus:** Why 7 months is higher than the "3–6 months" rule she probably heard.

| Step | Interaction | Outcome |
|:---|:---|:---|
| **3.1** Setup | AI speaks: "Here's what all of that adds up to — and why your number is what it is." | 800ms pause. |
| **3.2** Target Card | Card materializes. Headline: "₹4.9L — 7 months." Attribution strip builds row by row: salaried large pvt → 5 mo base. +1 infant. +1 uninsured parent. −0.5 dual income. +0.5 high fixed costs. = 7 months. AI speaks simultaneously: "Your emergency fund target is ₹4.9 lakh — that's 7 months of your family's essential expenses." | Target Card rendered with 5-row attribution strip. |
| **3.3** Myth-bust | Triggers — 7 months exceeds "3–6 months." Link auto-expands: "The '3–6 months' rule assumes no dependents needing medical cover and dual-income stability as a safety net. Your infant and your mother-in-law's health risk push you above that baseline." AI speaks: "You may have heard 3 to 6 months is enough. For your situation, it isn't — here's why." | Myth-bust inline expansion visible. |
| **3.4** Key insight | AI speaks: "The biggest thing people miss is the uninsured parent. Your mother-in-law has no health cover — if she needs hospitalisation, that comes straight from your pocket. That single factor adds a full month to your target." | AI message bubble below card. This is the editorial moment — it makes the number feel personal, not formulaic. |
| **3.5** CTAs | She reviews the card. Taps "Looks right ✓." | Target confirmed. |
| **3.6** D3 Prompt | D3 card slides up. AI: "One more thing — and this is separate from your safety net." She reads the prompt. | D3 card visible. |
| **3.6** D3 Response | She taps "Skip — not now." AI: "No worries. You can always add one later." Card fades. | D3 skipped. |
| **3.8** Starter Shield | NOT triggered. ₹4.9L is just below the ₹5L threshold. Even if it were above, dual income + stable large pvt employer signals moderate confidence in ability to build. | Proceed directly to Phase 4. |

**Key moments for New Parent:**
- The myth-bust pre-empts the "but I heard 3–6 months" doubt. The proactive explanation is the pivotal trust moment.
- The uninsured parent insight is unexpected — she likely hadn't thought of her mother-in-law's health as an EF dimension.

##### Sandwich Generation Archetype — Full Phase 3 Path

> **Profile:** 42M, SME owner (variable income), ₹22L/yr (working estimate ₹1.6L/mo after §C3 edit), 2 school-age kids + aging parents (cardiac father, diabetic mother, no insurance), single earner, metro. D9: sibling support ₹20K/mo.
> **Target entering Phase 3:** ₹11.5L / 12 months.
> **Attribution focus:** The Starter Shield framing — ₹11.5L is a daunting number. How does the AI make it feel actionable, not paralysing.

| Step | Interaction | Outcome |
|:---|:---|:---|
| **3.1** Setup | AI speaks: "Here's what all of that adds up to — and why your number is what it is." | 800ms pause. |
| **3.2** Target Card | Card materializes. Headline: "₹11.5L — 12 months." Attribution strip (7 rows + D9): own business (<3 yr) → 9 mo base. +1 single earner. +1 two school-age kids. +1.5 cardiac parent (no insurance). +0.5 diabetic parent (no insurance — already counted, marginal add). −0.5 metro job market (higher re-employment options for skilled SME). +1 sibling support [D9]. = 12.5 → rounded to 12 months. AI speaks simultaneously: "Your emergency fund target is ₹11.5 lakh — that's 12 months of your family's essential expenses." | Target Card rendered with 8-row attribution strip. The strip is longer — card grows but remains scannable. |
| **3.3** Myth-bust | Triggers — 12 months dramatically exceeds "3–6 months." Auto-expands: "The '3–6 months' rule was designed for salaried dual-income households with health insurance. As a sole earner running a business under 3 years, with aging parents who have no health cover, your risk profile is fundamentally different." AI speaks: "This is a lot higher than the '3 to 6 months' advice you've probably seen. Here's why that number was never designed for your situation." | Myth-bust expansion with strong framing. |
| **3.4** Key insight | AI speaks: "Being the sole earner for six people is the defining factor here. If your income stops — whether it's a slow quarter, a health issue, or anything else — there's no second salary. Everyone in your household depends on this fund." | AI message bubble. This is the most emotionally resonant line in his entire journey. |
| **3.5** CTAs | He reviews. The ₹11.5L number is large. He may pause here. He taps "Looks right ✓" — he knows the math checks out because the attribution strip laid it bare. | Target confirmed. |
| **3.6** D3 Prompt | D3 card slides up. AI: "One more thing — and this is separate from your safety net." | D3 card visible. |
| **3.7** D3 Response | He taps "Add a buffer." AI asks about typical obligation cost. He selects "₹50K–1L." AI asks frequency: he taps "2–3 times a year." AI computes: ₹75K (midpoint) × 2.5 = ₹1.9L/year. D3 card updates: "₹1.9L / year — separate from your ₹11.5L safety net." He taps "Confirm ✓." | D3 buffer locked at ₹1.9L/yr. |
| **3.8** Starter Shield | TRIGGERED. ₹11.5L ≥ ₹5L. Single earner, variable income, business <3 years. Target-to-income ratio: ₹11.5L / ₹1.6L = 7.2× — well above 6× threshold. | Starter Shield overlay appears. |
| **3.8** Starter Shield | Attribution strip collapses. Milestone overlay slides in: ★ Starter Shield ₹96K (1 mo) ← active. 3 Months Secure ₹2.9L. Half-Year Shield ₹5.8L. Fully Funded ₹11.5L. AI speaks: "That's a meaningful goal — and you don't need to build it all at once. Your Starter Shield is about ₹96,000 — one month of your family's essential expenses. Once you reach it, a single bad month in your business won't mean touching anything else. That's the first win." | He sees the milestone path. The ₹96K first target feels achievable vs. the ₹11.5L total. This is the anti-paralysis moment. |
| **3.8** CTA | He taps "Got it — let's plan →" | Transition to Phase 4. |

**Key moments for Sandwich Generation:**
- The cardiac parent row (+1.5 mo) is the largest single factor — validates the AI's earlier promise to "factor in" his father's condition. Staggered animation ensures he processes each factor.
- The Starter Shield is the critical anti-paralysis mechanism. ₹96K as first target feels achievable vs. ₹11.5L total — the shift from paralysis to action is the design intent.

---

#### Phase 3 — Re-Entry Adaptations

##### Scenario B in Phase 3 (User with Existing Savings)

| Adaptation | Spec |
|:---|:---|
| **Target Card includes gap analysis** | Headline shows: "Your target: ₹4.2L (8 months). You have ₹2.5L — you're 60% there." The ₹2.5L figure comes from the early Assessment capture (see Phase 2 re-entry, Scenario B). |
| **Attribution strip unchanged** | Shows the same dimension-by-dimension build. The gap is shown AFTER the attribution — not within it. |
| **AI Key Insight adaptation** | "You've already built a solid foundation — ₹2.5L puts you past the Starter Shield. The gap is ₹1.7L — about [N] months of saving ₹[X]/month. Very doable." Tone: validation, not "you're behind." |
| **Starter Shield** | NOT triggered if existing savings already exceed 1 month of expenses. The user is past that milestone. |

##### Scenario C in Phase 3 (User with Known Target — Validation Path)

| Adaptation | Spec |
|:---|:---|
| **Comparison reveal** | Target Card shows: "Your estimate: ₹5L. My recommendation: ₹5.8L." Attribution strip shows the full build to ₹5.8L. Below: "The difference is [specific factor they likely missed]." |
| **User choice** | Two CTAs: "Go with ₹5.8L" (primary) / "Stick with my ₹5L" (secondary). Per DD05 — user autonomy first. |
| **AI voice** | "Your estimate was close — the gap is mostly [specific factor]. Either number is reasonable. It's your call." |

##### Scenario E in Phase 3 (User with Existing EF — Review)

| Adaptation | Spec |
|:---|:---|
| **Gap or surplus framing** | If target > existing: "Your target is ₹6.2L. You have ₹5L — you're at 81%. Here's what the gap covers: [top contributing factor for the deficit]." |
| | If target ≤ existing: "You're actually a bit over-funded — ₹5L against a ₹4.2L target. The extra ₹80K could work harder elsewhere. Want me to show you options?" |
| **Tone** | Pure validation. "You've been doing the right thing. Let me sharpen it." |

---

#### Phase 3 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **Target Card** | Inline card with headline number + collapsible attribution strip + two CTAs | Hybrid (inline in stream, but pinned during Phase 3 interaction) | Contains three sub-sections: headline, attribution strip, CTAs. Card grows vertically based on attribution row count (5–9 rows). Slide-up materialization (300ms). |
| **Attribution Strip (DD07)** | Vertical stacked arrow diagram with tappable rows | Generative (rendered inline within Target Card) | Each row: factor label + directional adjustment (+ or −) + month contribution. Warm/cool colour coding. Rows are tappable → expand one-sentence explanation (§5.2 Level 2). Second-level tap reveals formula (§5.2 Level 3). 100ms stagger animation between rows. Collapsible. |
| **Literacy Fallback Narration** | AI message bubble with plain-English attribution | Generative (inline in stream, replaces attribution strip) | Triggered by low-literacy signal. Full conversational breakdown. "Show me the breakdown" recovery affordance. |
| **Myth-Bust Expansion** | Collapsible inline section within Target Card | Generative (inline within Target Card) | Auto-expands when target diverges from "3–6 months." Stays collapsed (tappable) when target is within range. |
| **D3 Prompt Card** | Optional inline card with two CTAs | Generative (inline in stream, below Target Card) | Visually distinct from Target Card: different background tint, dashed border, 🎁 icon. Slide-up animation (250ms). Fade-out on skip (200ms). |
| **D3 Buffer Sizing Inputs** | Range chips + frequency selector | Generative (inline in stream) | Amount range chips + frequency options. §C1 for ranges, §C2 for typed amounts. |
| **D3 Confirmed State Card** | Updated D3 card with confirmed buffer amount | Generative (inline in stream) | Shows annual buffer, calculation breakdown, "separate from your ₹[X]L safety net" label. Confirm/Remove CTAs. |
| **Starter Shield Overlay** | Milestone list overlaid within Target Card | Generative (replaces attribution strip area within Target Card) | 4 milestone rows from philosophy §6. Only Starter Shield row is active (★ marker, warm glow pulse). Others greyed. Attribution strip collapses to make room. Single CTA: "Got it — let's plan →". |
| **§C4 Re-edit Flow** | Edit-in-Place from Target Card "Adjust an answer" CTA | Generative (inline in stream) | Re-renders §C3 Summary Card. User edits → downstream recalculation → Target Card number morphs (400ms). Attribution strip re-renders. |

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
- Lead with the "why" not the "what." The 3-layer structure exists because emergencies have different speeds. A medical ER visit is a Speed 1 problem. A 3-month job search is a Speed 2/3 problem.
- Don't frame layers as separate products. Frame as different speeds of the same fund. The gradient strip on screen reinforces this — your narration should not contradict it by saying "three separate accounts."
- Adapt your vocabulary. High-literacy user: "liquid fund is T+1 with near-instant for ₹50K." Low-literacy user: "it's like an FD that you can get back the next business day."
- The layer amounts come from the target. Show proportional amounts (e.g., "₹96K in instant access, ₹2.9L in quick access, ₹2.5L in stable reserve").

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
- Warm amber fades to cool blue-slate left to right. Colour is the spectrum signal.
- Detail rows below the strip are always visible (not collapsed) — each layer has: icon, label, ₹ amount, instrument type, access time in plain English.
- "Tell me more" CTA: AI continues in conversational stream with a deeper explanation of whichever layer the user taps/asks about.
- "This works for me ✓" CTA: Accepts the structure. §C3 note: per §0B §C3 table, this phase gate can use a **lighter inline confirm** rather than a full summary card — the user is accepting a recommendation, not verifying data they entered. AI acknowledges: "Perfect. Now let's figure out how to actually build it." → Phase 5.

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
- Pressure the user to commit to a higher contribution than they're comfortable with
- Name specific products, apps, or platforms for execution
- Suggest the user needs to "figure out" setup themselves — the Action Card handles that
- Frame this as the end of the journey — frame it as the beginning of the build
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

#### Phase 5 — Re-Entry Adaptations (Scenarios B, C, E)

> These three scenarios require meaningful contribution plan adjustments — the gap between existing savings and the full target changes what the plan looks like.

| Scenario | Adaptation |
|:---|:---|
| **Scenario B (existing savings)** | Contribution plan starts from the gap, not the full target. "You have ₹2.5L — your gap is ₹1.7L. At ₹6,000/month, you'll close it in about 28 months." Milestone dates reflect the gap, not full target timeline. Starter Shield: if existing savings already exceed 1 month of expenses, the first active milestone is 3 Months Secure. |
| **Scenario C (user's own target, accepted)** | If user chose their own ₹5L target: plan is built on ₹5L. Milestone amounts adjust accordingly. AI does not revisit the target discrepancy. |
| **Scenario E (existing EF, surplus)** | If user is over-funded, Phase 5 framing shifts. "You're fully funded — nothing to build. Your next move is redirecting the ₹80K excess to work harder elsewhere. Want me to show you options?" Phase 5 pivot: from contribution plan to redirection advisory. No contribution plan card — different deliverable. |

The LLM intelligence layer handles Scenario routing — no hardcoded branching. The LLM knows the existing corpus from Assessment and adapts Plan Card content accordingly.

---

#### Phase 5 — Component Needs

| Component | Type | Screen Type | Notes |
|:---|:---|:---|:---|
| **Contribution Plan Card** | Inline card with amount, milestone path (dates), self-EMI label, Confirm CTA | Hybrid (inline in stream, anchors the phase) | Milestone rows show dates (not month counts). Starter Shield row ★-marked for visual continuity with Phase 3. Amount row tappable for §C4 edit with live date recalculation. |
| **Action Card (DD09)** | Persistent numbered instruction card with 3 steps + reminder CTA | Hybrid (inline stream on confirmation; persists to EF goal card on home) | Numbered steps per allocation layer in priority order. Instrument type only. Saved to home surface goal card. |
| **Salary-Day Reminder (DD09)** | Opt-in recurring notification trigger | System (OS notification infrastructure) | Monthly cadence. Deep-links to Action Card. Graceful degradation on denial. |
| **Milestone Timeline Rows** | 4 rows with label, ₹ amount, target date | Generative (rendered within Contribution Plan Card) | Same milestone labels as §3-SS. Dates computed from contribution amount and gap. ★ on Starter Shield row. |
| **§C4 Contribution Edit** | Tappable amount field → inline editor → date recalculation | Generative (inline within Contribution Plan Card) | Editing the contribution amount recalculates all milestone dates live. No full summary card needed — amount is the only input at this stage. |
| **Exit Confirmation Message** | AI message bubble with canonical §1A copy | Generative (inline in stream) | Text is fixed — not generated. The exact phrasing from J01 §1A is the monitored state voice. |
| **EF Goal Card (Home Surface)** | Static goal card showing: target, current amount, next milestone, plan summary | Static (home surface — outside conversational flow) | Updated after Phase 5 confirmation. Shows Action Card entry point. Progress bar reflects current corpus vs. target. |

---

## 7. Open Questions — Status

> All phase-specific open questions are now resolved as of Step 2D.

| # | Question | Status |
|:---|:---|:---|
| 1 | Assessment modality | ✅ Resolved — Step 2B → DD06 |
| 2 | "Building Your Picture" progress indicator | ✅ Resolved — Step 2B → §BYP |
| 3 | Attribution visualization | ✅ Resolved — Step 2C → DD07 (T7 partially resolved for Phase 3) |
| 4 | 3-layer allocation visual | ✅ Resolved — Step 2D → DD08 (horizontal liquidity gradient strip; T1 partially resolved for Phase 4) |
| 5 | Social obligation buffer UX | ✅ Resolved — Step 2C → §3-D3 |
| 6 | Contribution plan CTA | ✅ Resolved — Step 2D → DD09 (Action Card + salary-day reminder) |

---

*This document contains phase-by-phase interaction specifications for the J01 Emergency Fund journey. For product scope, boundaries, patterns, and metrics, see [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md).*

