# J01: Emergency Fund — Phase Interaction Specs

> **Parent document:** [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md)
> **Last updated:** 2026-04-16
> **Status:** Phases 1–2 fully specced (Step 2B). Phases 3–5 skeleton only (Steps 2C–2D pending).
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

**Advisory output (Philosophy D4 / DD02):** The plan tells the user: "Set up an auto-transfer of ₹X on your salary date to a [sweep-in FD / savings account]. Do the same for ₹Y to a liquid mutual fund." No specific platform or product named.

**Exit confirmation:** "Your plan is set. I'll keep an eye on things and reach out when something meaningful happens — a milestone hit, a life change, or a chance to optimise. You won't hear from me just to check in." → Monitoring state begins (see [J01 §1A](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md) for nudge philosophy).

---

## 7. Open Questions for Phase 2 (UX Journey Design)

> **Resolved in §0B:** Cross-cutting interaction patterns — voice+screen hybrid modality, multi-stage re-entry, and confirmation/validation UX — are addressed in [J01 §0B](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md). The questions below are phase-specific.

These are scoped questions — scope is locked, but the UX design of each phase will need to resolve these:

1. ~~**Assessment modality:**~~ **✅ Resolved (Step 2B → DD06).** Stream-primary — no composed surface in any beat. See [DD06](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD06-assessment-stream-primary.md) and Phase 2 §BYP + beat-by-beat spec above.
2. ~~**"Building Your Picture" progress indicator:**~~ **✅ Resolved (Step 2B → §BYP).** Dimension-pill progressive reveal with delayed number appearance (after Beat 1 value-after). Smooth morph + colour pulse on updates. Range → single number progression. See §BYP in Phase 2 above.
3. **Attribution visualization:** For the "Why This Number" reveal card — is the attribution a text list (simple), an arrow diagram (moderate), or a force graph / weighted bubble chart (rich)? Per literacy level: at least 2 designs needed. (T7: Verification Depth vs. Cognitive Load)
4. **3-layer allocation visual:** Bar chart (familiar), layered area chart, or a conceptual illustration (like stacked safe-deposit boxes)? The visual must communicate liquidity as a spectrum, not three separate buckets. (T1: Simplicity vs. Financial Rigor)
5. **Social obligation buffer UX:** The optional step after target reveal — how does it animate in? How does it maintain the "this is separate from your core EF" mental model visually?
6. **Contribution plan CTA:** What happens when the user "confirms the plan"? Do they get a step-by-step external instruction card? A shareable PDF? A calendar reminder? This is the highest-stakes CTA in the flow.

---


*This document contains phase-by-phase interaction specifications for the J01 Emergency Fund journey. For product scope, boundaries, patterns, and metrics, see [J01-emergency-fund-setup.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md).*
