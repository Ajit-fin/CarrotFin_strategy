# J01: Emergency Fund Setup

> **Workflow:** `/product` — Phase 1: Product Scoping & Flow Definition
> **Last updated:** 2026-04-16
> **Status:** Product definition locked. §0 updated for multi-agentic LLM architecture. Interaction specs (`J01-interaction-specs.md`) pending fresh rewrite with LLM-first architecture.
> **Trigger:** First-launch onboarding. New user opens CarrotFin for the first time.
> **User segments:** All — EF setup IS onboarding. Primary design anchors: New Parent (30–38) and Sandwich Generation (35–45). The Adaptive Engine (see §0 below) handles all 9 archetypes at runtime.
> **Assumptions depended on:** EF-11 (8 core sizing dimensions + 1 open/contextual dimension — see §2), C5 (conversational+visual integration feasible), C6 (adaptive composition viable in Flutter)
> **Design decisions:** DD01 (EF as onboarding), DD02 (advisory-only model), DD03 (V1 scope boundary), DD04 (voice+screen hybrid), DD05 (multi-stage re-entry), DD06 (assessment stream-primary)
> **Source philosophy:** `research/market/emergency-fund-philosophy.md`

---

## 0. What Is the Adaptive Engine — and Where Does It Sit?

> **For Phase 2 onwards:** This section clarifies a term used throughout J01 so downstream design and engineering phases are aligned.

The **Adaptive Engine** is CarrotFin's runtime intelligence layer — the system that determines what the AI says, what components are rendered, and how the experience adapts based on what it knows about the user at any given moment. It is **not a rules engine or a hardcoded decision tree** — it is an LLM-driven intelligence layer that receives structured context (prompt blocks with intent, constraints, and user state) and makes real-time decisions about conversation, composition, and adaptation.

In practice, this may be a single LLM with role-specific prompt sections, or a **multi-agentic system** where specialised agents handle different concerns in parallel:

| Agent concern | Responsibility | Example |
|:---|:---|:---|
| **Conversational** | What to say next, how to frame it, how to react to user input (including multi-info turns where the user provides several data points at once) | "User said 'I'm 34, salaried, two kids' — capture all three, acknowledge naturally, ask about the next unknown dimension" |
| **Computation** | EF target calculation, allocation splits, milestone dates, contribution timelines | "8 dimensions captured → compute target using sizing model → return ₹4.9L / 7 months" |
| **Composition** | Which UI components to render, with what data, in what order | "User confirmed target → render Target Card with attribution strip → queue D3 prompt if applicable" |
| **State** | Track what's known about the user, what's still needed, what phase they're in, what signals have been observed | "Income: captured. Dependents: captured. Insurance: not yet asked. Literacy signal: high (fast taps, no hesitation)" |

The product design layer (this document + the interaction specs) does **not prescribe the agent architecture**. It provides:
1. **LLM guidance prompts** — intent, context, constraints, and phase-exit conditions for each journey phase
2. **Component specs** — the palette of UI elements the composition layer can select from
3. **Handover notes** — items explicitly deferred to engineering implementation

**Where it sits in the product build journey:**

| Phase | What is built | Adaptive Engine involvement |
|:---|:---|:---|
| **Phase 1 (this doc)** | Product scope + flow definition | Defines what decisions the engine must make and the constraints it operates under |
| **Phase 2 (Interaction Specs)** | LLM guidance prompts + component specs | Provides the prompt context each agent needs + the component palette the composition agent selects from |
| **Phase 3 (Component Specs)** | Detailed adaptive component library | Defines the full component palette with interaction behaviors |
| **Phase 4 (Stitch Briefs)** | Mockup generation | Visualises representative paths the engine would produce |
| **Phase 5 (BuildSpec)** | Engineering handoff | Translates guidance prompts into agent prompt engineering + component implementation |

> **Key point:** The adaptive engine is not a rule-based system. It is an LLM intelligence layer (single or multi-agent) that receives prompt context from the product design layer and makes adaptive decisions at runtime. The product design layer's job is to give it the right context and constraints — not to script its behavior.

---

## 0B. Cross-Cutting Interaction Patterns — Voice + Screen, Re-Entry, Confirmation

> **For Phase 2 onwards:** These patterns govern interaction mechanics across ALL journey phases. They are architectural decisions, not phase-specific. Each journey phase's interaction spec (Phases 1–5 in §2) instantiates these patterns in its specific context.
>
> **Design decision records:** [DD04](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD04-voice-screen-hybrid-modality.md) (voice+screen hybrid modality), [DD05](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/DD05-multi-stage-re-entry.md) (multi-stage re-entry)

---

### Voice + Screen Hybrid Modality Model

CarrotFin's EF flow operates in a **voice + mobile screen** hybrid modality — or in **screen-only (text+tap)** mode if the user disables voice. Voice is a first-class input/output channel that overlays the existing conversational stream, not a separate mode.

#### Core Architecture

| Layer | Role | Example |
|:---|:---|:---|
| **Screen (visual)** | Source of truth. Renders conversation, components, confirmations, and persistent state. Always present. | The conversational stream, inline cards, progress indicators, summary cards |
| **Voice input** | Alternative to typing/tapping. User speaks; input is transcribed and rendered in the stream. | "My income is about 80 thousand" → appears as user message + triggers confirm chip |
| **Voice output (AI narration)** | AI speaks key messages while the screen renders richer content simultaneously. Supplements, doesn't replace, screen. | AI says "Your target is ₹4.2L" while the screen renders the full attribution card |

#### Design Rules

1. **Screen-first, voice-layered.** Every interaction is designed for text+tap as the baseline. Voice affordances are layered on top. No interaction requires voice.
2. **Screen is the source of truth.** Voice is ephemeral; the screen persists. After any voice input, the captured value appears on screen. The user confirms via screen, not via voice alone.
3. **Simultaneous, not sequential.** When the AI narrates, the screen renders in parallel — not after. The AI's spoken message is a summary of what the screen shows. The screen always contains equal or richer information.
4. **Voice does not gate.** Every AI question that can be answered by voice can also be answered by tapping a structured option or typing.
5. **Privacy-aware activation.** Voice is user-activated (opt-in toggle). The app does not default to voice-on.

#### Voice Input Handling by Data Type

| Input type | Voice UX | Screen UX on capture | Confirmation pattern |
|:---|:---|:---|:---|
| **Categorical** (employment type, city tier, age bracket, yes/no) | User speaks: "I'm freelance" / "Bangalore" / "I'm 32" | AI maps to category. Screen highlights the matched option in the structured choice set. | **Light Confirm** (§C1): Matched option highlights. AI echoes verbally. Auto-proceeds unless user corrects. |
| **Numeric** (income, obligations, insurance amount) | User speaks: "About 80 thousand" / "My rent is 25K" | Number appears as a **confirm chip** — prominent, editable. | **Active Confirm** (§C2): Chip remains until user taps ✓ or edits. AI pauses and echoes: "80 thousand a month — that right?" Flow waits. |
| **Open-ended narrative** (D9 factors, situation descriptions) | User speaks naturally: "I also support my brother's education" | Transcribed text appears in stream as user message. AI responds conversationally. | **No explicit confirm.** AI responds with its interpretation. User can correct if interpretation is wrong. |
| **Sensitive data** (health conditions, debts) | AI offers: "You can type this if you'd rather not say it aloud." | Same as above, with explicit text-input fallback affordance. | Per input type. The AI does **not** verbally echo sensitive data — screen-only confirmation. |

#### Voice Output: When the AI Speaks

The AI does NOT narrate everything on screen. It narrates selectively:

| Scenario | AI speaks? | Why |
|:---|:---|:---|
| Conversational turns (questions, follow-ups, value-after insights) | ✅ Yes | Natural conversation flow |
| Visual component materialization (chart, card, attribution strip) | ✅ Brief narration | AI provides a spoken summary ("Here's how your factors add up"). The visual carries the detail. AI does NOT read every label. |
| Structured input prompts (tappable options) | ✅ Speaks the question | "What's your employment type? You can pick from the options or just tell me." |
| Summary cards at phase gates | ❌ No narration (unless requested) | Summary cards are visual — user reads/reviews at their own pace. AI says only: "Take a look — does everything check out?" |
| Error/correction moments | ✅ Yes | "Hmm, let me fix that — did you mean 8 lakh, not 80 thousand?" |

#### Voice-Off Mode

When voice is disabled:
- All voice input falls back to tap+type. Structured options (inline buttons, sliders, range pickers) are the primary input.
- All AI narration falls back to on-screen text in the conversational stream. No information is lost.
- The experience is identical in content and flow — only the input/output modality changes.
- **Engineering implication:** Voice is an I/O layer, not a branching path. The underlying conversation engine, state machine, and component rendering are modality-agnostic.

---

### Multi-Stage Re-Entry Model

Users arrive at the EF journey at different stages of readiness. The AI must detect where the user is and route them to the appropriate entry point — never forcing redundant steps.

#### Entry Detection: How the AI Determines User Stage

| Signal source | What the AI knows | How it routes |
|:---|:---|:---|
| **App state (returning user)** | Serialized journey state from prior session — which beats are complete, which data points captured | Resume from last checkpoint (Scenario D) |
| **User's first response** | The goal discovery question ("What's on your mind financially?") and the user's answer reveal intent and readiness | Route based on signals in the response |
| **Probing question** | If readiness is ambiguous, AI asks: "Have you thought about emergency savings before, or is this fresh territory?" | Route based on answer |

#### Five Entry Scenarios

> **Clarification:** "Skip Phase 1" in the scenarios below means skipping the Discovery/Education content within the EF flow (§2, Phase 1). The app's goal selection surface (§1B) is always shown as the app entry point — it is not a journey phase and cannot be skipped. All re-entry routing happens *after* the user selects "Build my safety net" from §1B.

**Scenario A: Fresh User (Zero EF Context)**
- **Signal:** No prior sessions. No signals of prior EF work.
- **Entry:** Phase 1 (Discovery). Full flow.
- **AI opening:** Discovery conversation — establish relevance and urgency.

**Scenario B: User with Existing Savings**
- **Signal:** User says "I have some savings" / "I've been saving but I'm not sure if it's enough."
- **Entry:** Skip Phase 1 (Discovery). Enter Phase 2 (Assessment) with "health check" framing.
- **AI response:** "Let me check if your savings are sized right for your situation. Roughly how much have you set aside?" → Capture existing corpus → Run 8-dimension assessment → Compare existing vs. recommended.
- **UX nuance:** Frame as validation, not "let me build one from scratch." Respect the user's existing effort.

**Scenario C: User with a Known Target**
- **Signal:** User says "I've calculated I need about ₹5L" / "I read online that 6 months of expenses."
- **Entry:** AI offers validation: "That's a solid starting point. Online calculators often miss a few factors — mind if I run a quick check? Takes 2 minutes."
  - **If user agrees:** Enter Phase 2 (Assessment). At Target Setting, compare AI's number with theirs: "Your estimate of ₹5L was close — I'd suggest ₹5.8L because [attribution]. Want to go with mine or stick with yours?"
  - **If user declines:** Accept their number. Skip to Phase 4 (Fund Architecture): "Fair enough. ₹5L it is. Let me show you the best way to structure it."
- **Key principle:** User autonomy first. Never force redundant assessment. Offer value (validation) with low-commitment ask.

**Scenario D: Returning User (Abandoned Previous Session)**
- **Signal:** App has serialized journey state from a prior session.
- **Entry:** AI surfaces saved state: "Welcome back. Last time, we figured out your income profile and dependency situation. Want to pick up where we left off, or start fresh?"
  - **Pick up:** Resume from last completed beat. Show a quick summary card of captured data before continuing.
  - **Start fresh:** Clear state. Re-enter from Phase 1.
- **State persistence:** Journey state is saved per-beat (not per-phase). If the user completed Assessment Beat 1 and Beat 2 but abandoned during Beat 3, resume from Beat 3 with Beats 1–2 data intact.
- **Staleness rule:** If >30 days since last session, AI offers to re-verify key data: "It's been a while — still at [employer]? Still in [city]?" Quick confirmation of key data points before continuing.

**Scenario E: User with Existing EF (Review/Optimization)**
- **Signal:** User says "I have ₹3L in savings and a ₹2L FD. Is that enough?" / "I want to check if my emergency fund is right."
- **Entry:** Skip Phase 1 + early Assessment. Pre-fill existing fund data. Run 8-dimension assessment to compute recommended target. Show gap or surplus.
- **AI response:** "You have ₹5L total — solid foundation. Let me check if it's the right amount for your situation." → Assessment → Gap analysis: "Based on your profile, I'd recommend ₹6.2L. You're at 81% — here's what the gap covers."
- **UX nuance:** Emphasize validation and optimization, not "you're doing it wrong." These users are proactive — reinforce their behavior.

#### Engineering Implications for Re-Entry

- **Per-beat state serialization:** Journey state includes beat completion status + all captured data points + timestamps. Stored locally + synced.
- **Each phase is data-dependent, not sequence-dependent.** Phase 3 (Target Setting) needs the 8-dimension data, not Phase 2's "completed" flag. If data is available from any source (re-entry, user-stated, pre-filled), the phase can execute.
- **The AI is the router.** No hardcoded phase-sequence enforcement. The state machine supports arbitrary entry points.
- **Phase 1 content must be available on-demand.** Users who skip Discovery (Scenarios B, C, E) may still benefit from myth-busting and emergency framing content. Surface these as contextual cards during Assessment or later phases if relevant.

---

### Confirmation & Validation Patterns

Four patterns used across all phases. Each phase's interaction spec references these by ID (§C1–§C4).

#### §C1: Light Confirm (Categorical Input)

**When:** Voice captures a categorical selection (employment type, city, age bracket, yes/no).

**How it works:**
1. User speaks → AI maps to category
2. Screen highlights the matched option in the structured choice set or displays the category as an inline chip
3. AI verbally echoes: "Freelance — got it."
4. Flow proceeds to next question. No explicit tap required.
5. **If wrong:** User says "No, I meant salaried" or taps a different option → AI corrects

**Rationale:** Categorical inputs have low error rates (finite set). Forcing explicit confirmation for every selection turns conversation into a form. Verbal echo + visual display is sufficient.

**In text-only mode:** User taps structured option. Same highlight behavior. No echo needed — tapping IS confirmation.

#### §C2: Active Confirm (Numeric Voice Input)

**When:** Voice captures a numeric value (income, obligations, insurance amount) — data where mis-recognition has material downstream impact.

**How it works:**
1. User speaks → AI transcribes
2. Screen renders a **confirm chip**: the number displayed prominently with confirm (✓) and edit (✏️) affordances
3. AI verbally echoes with interpretive context: "80 thousand a month — that right?"
4. Flow **pauses** until user acts: tap ✓, tap edit, say "yes", or speak a correction
5. **If wrong:** User taps edit → inline number editor appears → user corrects → chip updates → flow resumes

**Rationale:** Numeric voice recognition errors compound. ₹80,000 vs. ₹8,00,000 is 10× downstream impact on EF target. One extra tap is worth the accuracy. But it's a single tap — feels like a natural conversation beat, not a verification step.

**In text-only mode:** Range pickers and sliders handle numeric input. Captured value appears inline with edit affordance. Same confirm chip, same pattern.

#### §C3: Summary Confirm (Phase-Gate Transition)

**When:** At the boundary between major journey phases — before the flow uses captured data to produce a significant output (e.g., Assessment → Target Reveal).

**How it works:**
1. AI announces the transition: "Let me pull together what we've covered."
2. A **summary card** materializes in the stream showing all key data captured in the current phase, displayed as a scannable list:
   - Each data row shows label + captured value (e.g., "Income: ₹80,000/mo (salaried, large pvt)")
   - Two CTAs at the bottom: **Looks right** (primary) / **Let me adjust** (secondary)
3. Each data row is tappable → triggers Edit-in-Place (§C4)
4. Flow **waits** for explicit CTA before proceeding
5. Voice option: User can say "Looks right" or call out a specific correction

**Rationale:** Phase gates are the right place for deliberate friction (per tension T5). The user is about to receive a life-impacting number. Ensuring input data is correct prevents "garbage in" distrust of the output. But it's a single card review, not a 10-field form.

**When summary confirms trigger:**

| Phase transition | Summary content |
|:---|:---|
| Assessment → Target Setting | All 8 (+ D9) dimension data points |
| Target Setting → Fund Architecture | Agreed target (₹ amount + months), social obligation buffer if any |
| Fund Architecture → Contribution Planning | Target + 3-layer allocation. *(Recommendation confirm: user accepts the AI's allocation advice, not verifying their own input data. Step 2D decides if a full §C3 summary card is needed or a lighter inline confirm suffices.)* |

#### §C4: Edit-in-Place (Retroactive Correction)

**When:** User wants to change a previously given answer — either during the flow or when reviewing a summary card (§C3).

**How it works:**
1. User taps a data point in the summary card or says "Actually, my income changed — it's 90 thousand now"
2. The data row transforms into an editable field (text input, range picker, or category selector — matching the original input method)
3. User enters new value → field collapses back to display mode with updated value
4. **Downstream recalculation:** Any computed values that depend on this data point update live
5. AI acknowledges: "Updated. That changes your picture a bit — let me recalculate."

**Rationale:** Financial planning is iterative. Users remember things mid-flow, situations change between sessions, and voice recognition errors need easy correction. Edit-in-place treats the assessment as a live profile, not a one-time form (per J01 §5: "No permanent locks").

---

### Modality Selection Heuristics Per Phase

Directional guidance for how voice+screen patterns play out across phases. The LLM intelligence layer decides specifics at runtime — this table captures the design intent, not a rigid rule.

| Journey Phase | Dominant modality | Confirmation patterns used |
|:---|:---|:---|
| **Phase 1: Discovery** | Conversational stream (voice or text). Open-ended, exploratory. | §C1 for option selections |
| **Phase 2: Assessment** | Stream + inline structured inputs. User provides data — may give multiple data points in a single turn. | §C1 for categorical (employment, city, age). §C2 for numeric (income, obligations). Text fallback for sensitive data (health). §C3 at assessment exit. |
| **Phase 3: Target Setting** | Conversation → visual pivot. AI narrates while screen renders. Low user input. | §C3 at target confirmation. §C4 if user edits. |
| **Phase 4: Fund Architecture** | Conversation + inline visual. AI explains allocation. | Lighter inline confirm (recommendation acceptance, not data verification). |
| **Phase 5: Contribution Planning** | Conversational setup → persistent action card. | §C2 for contribution amount. §C3 at plan exit. |

---

## 1. Product Boundaries

### Entry Points

| Entry Point | Trigger | User State at Entry | Notes |
|:---|:---|:---|:---|
| **First-launch onboarding** | App installed, first open | New, zero context, zero trust | Primary V1 entry. The app presents a multi-path starting point (see §1B below) — but only the EF path is active in V1. All other paths are visible placeholders. |
| **Home surface prompt** | Returning user, EF not set up | Low trust, some context exists | For users who skipped or abandoned onboarding mid-flow. |
| **Ambient nudge** | App detects no emergency fund data and user has been active for 7+ days | Warm trust, some context | Re-entry via notification: "You haven't set up your safety net yet." |

> The conversational stream (per `interaction-model.md`) is the entry modality for all paths — not a form, not a dashboard. The AI starts the conversation.

#### §1B — First-Run Experience: Multi-Path Shell, EF as the Only Active Path

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
| **Goal Tracker** | EF appears as Goal #1 in the goal list on the home surface | Navigation scaffold exists in V1 (Philosophy D2 / DD01). |
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
V1 ────────────────────────────────────────────────────┐
                                                       │
  [1] Discovery → [2] Assessment → [3] Target Setting  │
                                         ↓             │
                                   [4] Fund Architecture│
                                         ↓             │
                                   [5] Contribution     │
                                       Planning        │
                                         ↓             │
                                    ✅ V1 EXIT:        │
                                    Plan confirmed,     │
                                    monitoring begins   │
───────────────────────────────────────────────────────┘

V2 ────────────────────────────────────────────────────┐
  [6] Monitoring & Nudging                              │
  [7] Crisis Mode (Withdraw + Rebuild)                  │
  [8] Recalibration (Life event → target update)       │
───────────────────────────────────────────────────────┘
```

---


> **Detailed interaction specs for each phase are in [J01-interaction-specs.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-interaction-specs.md).**
> Phases 1–2 are fully specced. Phases 3–5 are skeleton only, pending Steps 2C–2D.

---

## 3. V1 Scope Boundary

### In V1

| Feature | Phase | Notes |
|:---|:---|:---|
| Conversational onboarding (EF as first-run) | Phase 1–5 | Philosophy D2 / DD01: EF is CarrotFin's active V1 path |
| 8-dimension assessment | Phase 2 | Adaptive, not a form |
| Personalized target with attribution | Phase 3 | "Why This Number" reveal |
| Social obligation buffer (optional) | Phase 3 | D3: Post-target, skippable |
| 3-layer allocation recommendation | Phase 4 | Instrument type only (Philosophy D4 / DD02) |
| Contribution plan (month, milestone) | Phase 5 | Advisory only. External execution. |
| Insurance gap flag | Phase 2 | Advisory flag, no dedicated flow |
| App shell with EF + placeholder goals | Navigation | Philosophy D2 / DD01: Home + 2–3 greyed goal cards |

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

*This is the product definition document for J01. Phase-by-phase interaction specifications (step-by-step conversation flows, adaptive variations, component inventories) are in [J01-interaction-specs.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-interaction-specs.md).*

