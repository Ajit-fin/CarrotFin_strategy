# CG01: Composition Grammar — J01 Emergency Fund

> **Date:** 2026-04-16  
> **Status:** Foundation — referenced by all component specs and the composition agent  
> **Scope:** J01 journey only. Other journeys will extend this grammar.

---

## 0. Architecture Principles

### 0.1 LLM Composition Agent Autonomy

Component specs define **available states and constraints** — not mandated sequences. The LLM composition agent selects which state to render based on runtime user context.

| Term | Meaning |
|:---|:---|
| **Mandatory** | This component type is available and should be offered to the agent for this phase. The agent decides exact timing, sequencing, and configuration. |
| **Conditional** | Surfaces only when the specified trigger condition is met. Agent decides if/when the trigger fires, except where marked DETERMINISTIC. |
| **DETERMINISTIC** | A hard business rule (e.g., Starter Shield threshold, DICGC ₹5L limit). Agent has no discretion — the rule fires automatically when conditions are met. |
| **Optional** | Agent may choose to surface this based on conversational context. No trigger rule. |

> [!IMPORTANT]
> **§2 rendering rules are guardrails, not decision trees.** The rendering matrices in §2 define the *bounds* within which the composition agent operates. They are structured context the agent receives — NOT `if-else` cascades for engineering to hardcode. The agent's value is handling intersections, edge cases, and nuance the matrix doesn't cover. Where cells conflict or underspecify, the agent reasons within the guardrails and the conflict resolution hierarchy (§4). Engineering implements these as **context injected into the composition agent's prompt**, not as deterministic lookup tables.


### 0.2 Flutter / Material Design 3 Native-First

Before specifying any custom component behaviour, identify the nearest M3 widget primitive. Build custom only when M3 has no equivalent or when the required appearance would override >50% of the widget's default styling.

| Priority | Approach |
|:---|:---|
| **1 — Use native** | Map to an existing M3 widget (e.g., `FilterChip`, `Card`, `ListTile`, `Slider`, `SearchAnchor`) |
| **2 — Configure native** | Use the M3 widget with `ThemeData` overrides or constructor params (colour, shape, size) |
| **3 — Extend native** | Subclass or wrap a native widget to add one behaviour (e.g., trailing action on `InputChip`) |
| **4 — Build custom** | Only when 1–3 are not feasible. Document the reason in the component spec. |

### 0.3 Agent Interface Architecture

The Adaptive Engine (J01 §0) coordinates four agent concerns. This section defines the **structural contract** between them — what each receives, produces, and how they communicate.

#### Agent Roles

| Agent | Receives | Produces | Downstream consumer |
|:---|:---|:---|:---|
| **Conversational** | User utterance + dialogue history + current user state | Response text + extracted data signals (0–N dimensions per turn) | State agent (batch update); Composition agent (conversational context) |
| **Computation** | Captured dimension values (all 8 + D9) | ₹ target, allocation splits, milestone dates, contribution amounts | Composition agent (structured computed values). Semi-deterministic — formulaic core with LLM-calibrated range resolution for base EF target (see BS-001 §1.5 note) |
| **Composition** | User state (4 adaptive dimensions + confidence) + journey state (phase, beat) + computed values + conversational context | Component selection + configuration: which component, which state, which adaptive variant | Rendering layer. Decisions bounded by §2 guardrails |
| **State** | Dimension updates from conversational agent + session signals (modality, pacing, vocabulary) | Current user-state snapshot: 4 adaptive dimensions with confidence bands + all captured journey data | All other agents (read-only). Persistent across turns |

#### Communication Pattern

Agents exchange **structured JSON context blocks** — not hardcoded enum handoffs. Key constraints:

- Context blocks are **append-friendly** — each agent adds to the shared context, not replaces it
- The composition agent receives **full context** (user state + journey state + computed values + conversational signals) and makes rendering decisions holistically
- No agent-to-agent path is synchronous-blocking except **computation → composition** (composition waits for computed values before rendering data-dependent components)
- The input/output schema between agents is a **joint product-engineering decision** — product defines the contract requirements (this document); engineering defines the technical format

#### State Agent: Batch Update Protocol

The state agent accepts **batch dimension updates**. A single user utterance can update 0–N dimensions simultaneously.

> **Example:** *"I'm 34, salaried, two kids in Bangalore"* → conversational agent extracts D6 (age), D1 (employment type), D2 (dependents), D5 (city) in a single parse. State agent receives all four as one batch update.

This is trivially handled by LLM extraction. The state update protocol **must NOT** be built as a sequential slot-filler — doing so discards the primary advantage of LLM-native intelligence over rules-based NLU.

**Visual feedback:** When a batch capture occurs, CP02 (BYP Progress Indicator) animates multiple pill fills in a single staggered sequence (simultaneous fills with 60ms offset per pill), visually acknowledging the user's efficiency.

#### Composition Agent Contract

The composition agent is the bridge between intelligence and rendering. Its contract:

1. **Input:** Receives the full context block — never a reduced enum or mode flag
2. **Output:** Returns component selection + configuration as structured instructions — not natural language descriptions
3. **Autonomy boundary:** Selects *which* component state to render from the defined state machines (CP01–CP05). Does NOT invent new component states. Layout within each state is illustrative (see component spec framing notes) unless explicitly marked canonical
4. **Guardrail compliance:** All rendering decisions must fall within §2 bounds. The agent may exercise judgement at intersections and edge cases the matrix doesn't cover, but may not contradict an explicit matrix cell
5. **DETERMINISTIC overrides:** The agent has no discretion over DETERMINISTIC triggers (§0.1). These fire automatically regardless of the agent's rendering preferences

---

## 1. Phase-Level Component Mandates

> **Reading note:** "Mandatory" below means the component type is available and appropriate for this phase — not that it fires at a fixed moment. See §0.1 for definitions.

### Phase 1: Discovery — Agent-Improvised

> **Agent-Improvised.** Phase 1 has no pre-defined component specs. The conversational agent composes Phase 1 screens from M3 primitives using the Phase 1 LLM prompt (J01-interaction-specs.md) as sole guidance. The prompt provides intent, constraints, and component descriptions — the agent improvises the rendering.
>
> This aligns with the overall architecture: Phase 1 is the most conversational, least structured phase — it SHOULD be the most agent-driven. No component spec is needed — the agent operates within M3 defaults and DS01 tokens.

---

### Phase 2: Assessment

| Component | Mandate | Trigger / Condition |
|:---|:---|:---|
| BYP Progress Indicator | **Mandatory** | Pinned from Phase 2 entry to §C3 confirmation |
| Structured option chips (§C1) | **Mandatory** | One set per categorical dimension (D1 type, D5, D6, D7) |
| Confirm Chip (§C2) | **Mandatory** | Fires for income (D1 amount) and fixed obligations (D4) |
| Range Picker / Slider | **Mandatory** | Income bracket selector, obligations slider |
| Multi-Select Chip Set | **Mandatory** | Dependency mapping (D2) |
| City Selector | **Mandatory** | D5 capture |
| "Your Safety Net So Far" Card | **Conditional** | After responsibility cluster (D2+D3+D4) value-after |
| §C3 Summary Card | **Mandatory** | Phase gate — fires after all 8 dimensions captured |
| §C4 Edit-in-Place | **Conditional** | User taps a row in §C3 |
| Insurance Advisory Flag | **Conditional** | D3 signals no/inadequate health cover |
| Sensitive Data Text Fallback | **Conditional** | D8 (health) capture — always offered as alternative to voice |

---

### Phase 3: Target Setting

| Component | Mandate | Trigger / Condition |
|:---|:---|:---|
| Target Card | **Mandatory** | Materialises after reveal sequence |
| Attribution Strip (DD07) | **Mandatory** | Within Target Card — unless literacy fallback triggers |
| Literacy Fallback | **Conditional** | Low literacy signal (replaces attribution strip) |
| Myth-Bust Expansion | **Conditional** | Target diverges materially from 3–6 month generic range → auto-expand; within range → tappable only |
| D3 Prompt Card | **Conditional** | Always surfaces after "Looks right ✓" — user can skip in one tap |
| D3 Buffer Sizing Inputs | **Conditional** | User taps "Add a buffer" on D3 Prompt Card |
| D3 Confirmed Card | **Conditional** | After D3 buffer calculation confirmed |
| Starter Shield Overlay | **Conditional** | DETERMINISTIC: target ≥ ₹5L AND (age ≤ early 30s OR income ≤ ₹80K OR target/income > 6×) |
| §C4 Re-edit Flow | **Conditional** | User taps "Adjust an answer" on Target Card |

---

### Phase 4: Fund Architecture

| Component | Mandate | Trigger / Condition |
|:---|:---|:---|
| Allocation Card | **Mandatory** | Materialises with AI's "3 speeds" narration |
| Horizontal Liquidity Gradient Strip (DD08) | **Mandatory** | Within Allocation Card — zone widths proportional to ₹ amounts |
| Layer Detail Rows (3×) | **Mandatory** | Always visible within Allocation Card (not collapsed) |
| DICGC Footnote | **Conditional** | Total target > ₹5L |
| "Tell me more" Expansion | **Optional** | User taps layer row or asks follow-up — agent decides depth |
| Phase Gate Confirm (light) | **Mandatory** | "This works for me ✓" — lighter than full §C3 |

---

### Phase 5: Contribution Planning

| Component | Mandate | Trigger / Condition |
|:---|:---|:---|
| Contribution Plan Card | **Mandatory** | Materialises after contribution amount agreed |
| Milestone Timeline Rows (4×) | **Mandatory** | Within Contribution Plan Card — dates, not month counts |
| §C4 Contribution Edit | **Conditional** | User taps contribution amount to edit |
| Action Card (DD09) | **Mandatory** | Fires on "Confirm my plan →" |
| Salary-Day Reminder | **Conditional** | User taps "Remind me on salary day" — requires OS notification permission |
| Exit Confirmation Message | **Mandatory** | Canonical §1A copy — verbatim, never paraphrased |
| EF Goal Card (Home Surface) | **Mandatory** | Created on Phase 5 completion — static, persistent |

---

## 2. User-State-Driven Rendering Rules

### 2.1 Financial Literacy (1–5)

| Literacy | Selection | Configuration | Density |
|:---|:---|:---|:---|
| 1–2 | Attribution strip → **literacy fallback** (plain-text narration). Myth-Bust auto-expand suppressed. | Labels: plain English, no financial jargon. Attribution shows factor name only (no +/− notation). Layer detail rows: simplified vehicle descriptions ("savings account" not "sweep-in FD"). | Max 2 cards visible per viewport. Extra whitespace between cards. |
| 3 | Attribution strip rendered (default). Myth-Bust tappable. | Labels: mixed — common terms spelled out, ₹ formatting standard. Tappable rows show Level 2 explanation by default. | Max 3 cards per viewport. Standard spacing (DS01 §5). |
| 4–5 | Attribution strip expanded by default. Myth-Bust auto-expand on divergence. All detail rows visible. | Labels: precise financial terminology. Tappable rows open to Level 3 (formula/source) on first tap. DICGC footnote rendered inline (not collapsed). | Max 4 cards per viewport. Compact spacing (section gap 8dp instead of 12dp). |

### 2.2 Trust Level

| Level | Selection | Configuration |
|:---|:---|:---|
| New / Warming | Attribution strip **expanded** by default (first-time — user needs to see the logic). D3 Prompt Card always surfaces. | §C3 Summary Card shows all rows with explicit labels. "Why is this different?" auto-expands on target divergence. |
| Trusting | Attribution strip **collapsed** by default (returning — headline sufficient). D3 surfaces only if detected as relevant. | §C3 rows show condensed labels. Detail expansions start collapsed. |
| Dependent | Minimal attribution (headline + key insight only). D3 buffer deferred unless user asks. | Maximum compression — trust the AI's number. Conversational confirmation over visual proof. |

### 2.3 Emotional State

| State | Selection | Configuration | Pacing |
|:---|:---|:---|:---|
| Calm | Standard rendering | Default spring parameters | Normal stagger (60ms) |
| Anxious | D3 Prompt Card deferred (don't add obligations when user is stressed). DICGC footnote suppressed. | Softer language in AI narration. Warm colour dominance in BYP pulses. | Slower stagger (100ms). Longer pause before Target Card reveal. |
| Overwhelmed | Reduce to mandatory components only — no optional expansions. Myth-Bust suppressed. | Headline-only mode: cards show title + primary CTA, detail collapsed. | Slowest stagger (120ms). Extended silence after reveal. |
| Excited | All components rendered — user is engaged. | Full detail, Level 2 auto-expanded on attribution rows. | Faster stagger (40ms). Snappier springs (stiffness +50). |

### 2.4 Engagement Mode

| Mode | Selection | Configuration |
|:---|:---|:---|
| Conversational (voice-heavy) | Literacy fallback bias — prefer narration over visual attribution strip. | Chips render larger (touch target 48dp). CTA buttons full-width. Confirm patterns favour voice ("say 'looks right'"). |
| Visual (tap-heavy) | Full visual rendering — attribution strip, gradient strip, milestone rows. | Chips standard size (40dp touch target). Expand affordances prominent (chevrons visible). |
| Hybrid | Standard rendering per literacy level. | Balance voice hints and tap affordances. §C1/§C2 accept both modalities equally. |

---

## 3. Density Rules

| Literacy | Max Cards per Viewport | Card Content Padding | Section Gap | Card Vertical Margin |
|:---|:---|:---|:---|:---|
| 1–2 | 2 | 20dp | 16dp | 12dp |
| 3 | 3 | 16dp (DS01 default) | 12dp (DS01 default) | 8dp (DS01 default) |
| 4–5 | 4 | 16dp | 8dp | 8dp |

**Emotional override:** If `emotional_state == overwhelmed`, reduce max cards by 1 regardless of literacy level.

**Pinned components excluded:** BYP Progress Indicator does not count toward viewport card limit — it occupies its own fixed zone.

---

## 4. Conflict Resolution

Resolution hierarchy (from design-principles.md):

> **Adaptive Composition > Conversational+Visual Integration > Hyperpersonalization**

### Conflict Matrix

| Conflict | Rule A | Rule B | Resolution |
|:---|:---|:---|:---|
| High literacy wants all 8 rows + overwhelmed state wants density reduction | Literacy: show all rows | Emotional: headline-only mode | **Adaptive Composition wins.** Reduce to headline + top-3 factors. Collapse remainder behind "Show all factors" tap. |
| Voice mode wants narration-only + visual attribution strip is mandatory for trust level New | Engagement: suppress visual strip | Trust: show expanded strip for first-time users | **Conversational+Visual wins.** Show the strip AND narrate the headline. Both channels deliver simultaneously (§0B Rule 3). |
| High literacy wants compact density + anxious state wants slower pacing | Literacy: compact spacing, fast stagger | Emotional: wider spacing, slow stagger | **Adaptive Composition wins.** Use compact spacing (layout) but slow pacing (animation). Layout and motion are independent axes. |
| Trust level Dependent wants minimal attribution + target diverges from 3–6 month range | Trust: headline only | Divergence: auto-expand "Why is this different?" | **Adaptive Composition wins.** Show the divergence explanation — trust cannot override the need to explain a surprising number. Collapse after user has seen it once. |
| D3 buffer deferred (anxious) + D3 Prompt Card is always-surface | Emotional: defer D3 | Mandate: always surface | **Adaptive Composition wins.** Defer D3 to next session or end of Phase 4. Log deferral for re-surfacing. |

### General Precedence

1. **Mandatory components always render** — but their *configuration* (expanded/collapsed, detail level) is adjusted by user state.
2. **Conditional triggers are hard rules** — Starter Shield fires deterministically; user state cannot suppress a deterministic trigger.
3. **Optional components yield first** — when density limits are exceeded, optional components are dropped before conditional ones are collapsed.
4. **Animation pacing adjusts independently of layout density** — spacing and spring parameters are separate dimensions.

---

## 5. User-State Inference Architecture

> **How the composition agent knows which rendering rules to apply.** §2 defines what to render for a given user state. This section defines how the state agent determines what that user state is.

**Core principle:** LLM contextual reasoning IS the inference mechanism. There are no hardcoded scoring formulas or threshold tables. The state agent observes signals, reasons about them in context, and outputs a dimension band with a confidence level. The band is reassessed continuously — not scored once at session start and frozen.

### 5.1 Inference Rules by Dimension

| Dimension | What the LLM observes | Output | Update cadence |
|:---|:---|:---|:---|
| **Financial literacy (1–5)** | Vocabulary precision (uses "SIP", "sweep-in", "corpus" vs. "savings account", "money"); question depth (asks about allocation rationale vs. just "what do I do?"); response speed on structured inputs; willingness to engage with attribution detail | Band: 1–2 / 3 / 4–5 + confidence (low/medium/high) | Per-cluster (reassessed after each conversational beat, not per-turn). Confidence rises as more signals accumulate. |
| **Trust level** | Data shared voluntarily vs. withheld; recommendations accepted without pushback vs. questioned; return frequency and session depth; whether user asks for explanation before confirming | Level: New → Warming → Trusting → Dependent + confidence | Per-session boundary. Advances within a session when the user accepts a significant recommendation (e.g., confirms target, accepts allocation). Regresses if user repeatedly edits or disputes. |
| **Emotional state** | Word choice (hedging language, exclamations, negative framing); pacing (long pauses between turns, rapid-fire questions); topic sensitivity signals (avoidance of health/debt questions); explicit statements ("I'm really stressed about this") | State: Calm / Anxious / Overwhelmed / Excited | Per-turn. Most volatile dimension — updated after every conversational exchange. Overrides accumulate: two consecutive anxious signals lock the state for the current beat. |
| **Engagement mode** | Input modality distribution over last N turns (voice vs. tap vs. type); dwell time on visual components; whether user expands detail rows or ignores them; CTA response latency | Mode: Conversational / Visual / Hybrid | Per-cluster (last 3–5 turns form the observation window). Single-turn outliers do not shift the mode — pattern must be consistent. |

### 5.2 Output Format

The state agent produces a **user-state snapshot** after each turn: a structured object with four keys (one per dimension), each carrying a band/level value and a confidence level (low / medium / high). The exact schema is a joint product-engineering decision (§0.3).

This snapshot is injected into the composition agent's context block (§0.3) before every rendering decision. Low-confidence dimensions default to the more conservative rendering option in §2 (e.g., low-confidence literacy defaults to band 3 behaviour, not 4–5).

### 5.3 What the LLM Does NOT Do

| Anti-pattern | Why avoided |
|:---|:---|
| Score vocabulary on a point scale (1 pt per jargon term used) | Brittle — context-dependent. A user who says "SIP" once may be echoing something they read, not demonstrating literacy |
| Advance trust after N interactions | Mechanical — trust is qualitative. A user who shares sensitive data on turn 2 is more trusting than one who withholds it on turn 20 |

### 5.4 Relationship to §2 Rendering Rules

§2 (rendering rules) and §5 (inference) are the full specification loop — no intermediate rules engine sits between them.
