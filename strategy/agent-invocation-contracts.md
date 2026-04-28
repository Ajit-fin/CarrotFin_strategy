# Agent Invocation Contracts

> AG-01 | Version: 0.2.0 | Date: 2026-04-18
> Status: Framework Draft — contracts defined at interface level, not implementation level
> Changelog: v0.2.0 — Added §3.5 (deliberation loops), §5.6 (knowledge grounding), modality-aware output coordination across §2.2/§2.5/§2.6, §3.6 (challenge handling)
> Upstream: BS-001 §1.5 (Agent Architecture), agent-invocation-contracts-objective.md
> Consumer: Dev workspace orchestration layer

---

## 0. How to Read This Document

This document defines **what crosses agent boundaries** — not how agents work internally.

**What this specifies:**
- The semantic shape of each agent's input and output
- Which fields are required vs. optional vs. extensible
- The dependency graph between agents (who needs what from whom)
- Failure modes and recovery expectations
- Prompt architecture principles

**What this deliberately leaves open:**
- Exact JSON field names and nesting (engineering decides)
- Whether agents are separate LLM calls, tool-use branches, or functions (model-agnostic)
- Internal reasoning strategies within each agent
- Serialization format (JSON used illustratively, not prescriptively)
- Performance optimizations (parallelism, caching, batching)

**Design philosophy:** Define the invariants. Leave the implementation space open. The orchestration layer should be able to evolve without breaking these contracts.

---

## 1. Agent Roles & Boundaries

Four agent concerns. Each may be a separate LLM call, a tool-use branch within a single call, or a pure function — the contracts are topology-agnostic.

### 1.1 Conversational Agent

**Job:** Process user input. Produce a response. Extract structured signals from unstructured input. When the user challenges or pushes back on the AI's reasoning, this agent must defend, explain, concede, or adjust — in real time.

**Boundary rule:** This agent sees the user's words. It does NOT see computed values (₹ targets, allocation splits) — those come from Computation. It does NOT decide what to render — that's Composition's job. However, when handling user challenges (see §3.6), it may trigger a deliberation loop that re-invokes Computation before producing its final response.

**What makes it "agentic":** It decides *how* to ask, *what* to say, *when* to probe deeper, *what signals* to extract, and *how to handle pushback* — all within the guardrails embedded in its system prompt. It reasons over product philosophy and behavioral principles (via the knowledge grounding layer, §5.6) to justify its positions.

### 1.2 Computation Agent

**Job:** Produce deterministic and semi-deterministic numerical outputs from captured dimension values.

**Boundary rule:** This agent receives structured data, not raw user utterances. It produces numbers, not prose. Its outputs are consumed downstream — it never speaks to the user directly.

**What makes it "semi-deterministic":** The formulaic core (zone widths, milestone dates, allocation splits, contribution projections) is deterministic. The base EF target calibration — where within a dimension's range a specific user falls — requires LLM-assisted contextual reasoning. The contract must clearly separate these two output classes.

### 1.3 Composition Agent

**Job:** Decide what the user sees *and hears*. Select components, configure adaptive variants, assemble the rendering instruction set, and — in voice/hybrid mode — produce the voice narration layer that accompanies visual components.

**Boundary rule:** This agent receives the richest context (user state + journey state + computed values + conversational context + active modality). It produces rendering instructions AND modality-coordinated output specs — it does NOT produce the conversational response text (that's Conversational) or the underlying numbers (that's Computation). It DOES produce voice narration scripts for visual components in voice/hybrid mode (see §2.6).

**What makes it the "primary orchestrator interface":** It's the agent that translates all upstream context into a concrete, modality-aware user experience. Its contract is the most complex and the highest-priority deliverable.

**Modality coordination responsibility:** When the user is in voice or hybrid mode and the turn involves visual components (charts, cards, attribution strips), the Composition agent produces a `voice_narration` for each visual component — a spoken summary or walkthrough that accompanies the visual. The Conversational agent produces the conversational response; the Composition agent produces the visual walkthrough narration. These are different voices with different purposes:
- **Conversational response** (from Conversational agent): editorial, emotional, relational. "The biggest thing people miss is the uninsured parent."
- **Visual narration** (from Composition agent): descriptive, structural, explanatory. "This chart shows your 3-layer allocation — the green zone is instantly accessible..."

### 1.4 State Agent

**Job:** Maintain the canonical user-state snapshot. Process dimension updates, infer adaptive dimensions, and persist journey state.

**Boundary rule:** This agent is the single source of truth for user state. All other agents read from it. Only the Conversational agent writes to it (via extracted dimension updates). The State agent itself decides how to update the 4 adaptive dimensions (literacy, trust, emotion, engagement) — other agents consume these values but don't set them.

---

## 2. Context Schemas

Each agent receives a defined context envelope. Schemas below are **semantic** — they define what information crosses the boundary, not the exact wire format.

### 2.1 Conversational Agent — Input

```
ConversationalInput {
  // REQUIRED
  user_utterance: string | structured_input    // Raw text, voice transcript, or structured tap/selection
  dialogue_history: Message[]                  // Recent conversation turns (window managed by orchestrator)
  current_user_state: UserState                // From State agent — read-only snapshot

  // CONTEXTUAL (orchestrator decides what to include per turn)
  journey_state: JourneyState                  // Current phase, completed beats, captured dimensions
  active_guardrails: Guardrail[]               // Phase-specific MUST/MUST NOT constraints (from system prompt)
  active_modality: voice | text | hybrid       // Current interaction modality — affects response shaping

  // KNOWLEDGE GROUNDING (see §5.6)
  grounded_context: GroundedContext[]           // Retrieved reference passages relevant to this turn
                                               // (behavioral principles, design philosophy, domain knowledge)
                                               // Orchestrator retrieves; agent reasons over.

  // OPTIONAL
  voice_metadata: VoiceInfo                    // If voice input: confidence, language, ambient noise level
}
```

**Context window rule:** The orchestrator manages dialogue history depth. The contract specifies *what* the agent needs, not *how much*. Recommended: full current-phase history + summarized prior phases. The orchestrator should err toward more context when ambiguous — the agent can ignore irrelevant context, but it can't reason about context it doesn't have.

### 2.2 Conversational Agent — Output

```
ConversationalOutput {
  // ALWAYS PRESENT
  response_text: string                        // User-facing message — the conversational response
  turn_intent: enum                            // What the agent decided to do this turn
                                               // (ask, acknowledge, reveal, educate, confirm, bridge,
                                               //  stay_silent, defend, concede, adjust)
                                               // Note: defend/concede/adjust are challenge-handling intents (§3.6)

  // 0–N PER TURN (batch capture)
  extracted_dimensions: DimensionUpdate[]       // Each: { dimension_id, value, confidence, source_quote }
  
  // INFERRED (may be empty if no signal)
  state_signals: StateSignal[]                 // Observations for the State agent's adaptive dimension inference
                                               // e.g., { dimension: "literacy", signal: "used_term_SIP", direction: "up" }

  // CONDITIONAL
  confirmation_request: ConfirmationSpec       // If the agent needs user confirmation (§C1–§C4 pattern + data)
  voice_output: VoiceSpec {                    // Voice-specific output (voice/hybrid modality only)
    narration_text: string,                    // What the AI speaks — may differ from response_text
                                               // (e.g., shorter summary for voice, richer detail on screen)
    pacing_hints: PacingHint[],                // Pause points, emphasis markers
    sensitive_data_suppress: boolean,          // If true, voice output omits this content (screen-only)
    sync_with_visual: ComponentId | null       // If set, voice narration pauses until this visual component
                                               // has started rendering — enables simultaneous delivery
  }
  recomputation_request: RecomputationSpec     // If challenge handling (§3.6) requires Computation re-run
                                               // before the Conversational output is finalized
}
```

**Batch capture invariant:** A single turn can produce 0–N `extracted_dimensions`. The downstream pipeline MUST handle batch updates atomically — partial application of a multi-dimension extraction is a bug.

### 2.3 Computation Agent — Input

```
ComputationInput {
  // REQUIRED
  captured_dimensions: CapturedDimension[]     // All D1–D8 (+ D9 if present) with values and confidence
  computation_type: enum                       // What to compute: ef_target | allocation_splits | 
                                               // milestone_dates | contribution_plan | recalculation

  // CONTEXTUAL
  user_overrides: Override[]                   // User-edited values from §C4 (value + which dimension)
  existing_corpus: number                      // For Scenarios B/E: user's existing EF amount (₹)
}
```

**No raw utterances.** This agent never sees user text. It receives structured, typed values only.

### 2.4 Computation Agent — Output

```
ComputationOutput {
  // DETERMINISTIC OUTPUTS (pure math — no LLM reasoning)
  zone_allocations: {                          // 3-layer split
    instant_access: { amount: ₹, percentage: % },
    quick_access: { amount: ₹, percentage: % },
    stable_reserve: { amount: ₹, percentage: % }
  }
  milestone_dates: MilestoneDate[]             // 4 milestones with projected dates
  contribution_monthly: ₹                      // Recommended monthly amount
  
  // SEMI-DETERMINISTIC OUTPUTS (formulaic core + LLM-calibrated range resolution)
  ef_target: {
    amount: ₹,
    months: number,
    dimension_attributions: Attribution[]       // Each: { dimension_id, direction: +/-, months_impact, 
                                               //         reasoning_summary }
  }
  calibration_reasoning: {                     // Isolated reasoning trace for the range resolution step
    dimensions_calibrated: DimensionCalibration[],  // Which dimensions had range ambiguity
    resolution_rationale: string               // Why the agent chose this point within each range
  }

  // METADATA
  computation_version: string                  // Formula version for reproducibility
}
```

**Separation invariant:** Deterministic outputs MUST be reproducible given the same inputs — no LLM call involved. Semi-deterministic outputs carry their `calibration_reasoning` so downstream consumers (and verification tests) can distinguish "the math" from "the judgment."

### 2.5 Composition Agent — Input

```
CompositionInput {
  // REQUIRED
  user_state: UserState                        // Full adaptive dimensions (literacy, trust, emotion, engagement)
                                               // with confidence bands
  journey_state: JourneyState                  // Current phase, beat, captured data, completed gates
  active_modality: voice | text | hybrid       // Current interaction modality — governs output shape
  
  // REQUIRED WHEN AVAILABLE
  computed_values: ComputationOutput           // From Computation agent (when phase requires computed data)
  conversational_context: {                    // From Conversational agent's current turn
    turn_intent: enum,
    response_text: string,
    confirmation_request: ConfirmationSpec     // If any
  }

  // REFERENCE (stable across session, loaded once)
  phase_component_palette: ComponentSpec[]     // Available components for this phase (from BuildSpec §3.x.8)
  phase_guardrails: Guardrail[]               // Phase-specific rendering constraints
  global_composition_rules: CompositionRules   // §1.5 rules: viewport density, conflict resolution, mandates
}
```

**Richest context:** This agent receives the union of all other agents' outputs plus the full composition grammar. This is by design — it makes holistic rendering decisions.

### 2.6 Composition Agent — Output

```
CompositionOutput {
  // ALWAYS PRESENT
  components: ComponentInstruction[]           // Ordered list of components to render this turn
                                               // Each: { component_id, variant, configuration, mandate_type,
                                               //         position_hint, motion_config }

  // PER COMPONENT
  adaptive_decisions: AdaptiveDecision[]       // For each component: what variant was chosen and WHY
                                               // { component_id, decision: string, 
                                               //   governing_dimension: string, confidence: float }

  // MODALITY-COORDINATED OUTPUT (voice/hybrid mode)
  visual_narrations: VisualNarration[]         // One per visual component that warrants spoken accompaniment
                                               // Each: { component_id, narration_text, narration_type,
                                               //         sync_point: before | during | after }
  //
  // narration_type values:
  //   summary    — headline walkthrough ("This shows your 3-layer allocation...")
  //   annotation — callout on specific data point ("Notice the warm zone — that's your instantly accessible cash")
  //   silent     — no voice narration (component speaks for itself — e.g., a simple confirmation chip)
  //
  // sync_point governs timing relative to visual render:
  //   before  — narration starts, visual materializes mid-narration (build anticipation)
  //   during  — narration and visual start simultaneously (the default for most components)
  //   after   — visual renders, silence, then narration (for complex visuals that need scanning time)

  // CONDITIONAL
  layout_metadata: {
    viewport_density: enum,                    // low / mid / high (from literacy band)
    emotional_override_applied: boolean,
    cards_suppressed: ComponentId[],           // Components dropped due to density limits
    modality_adaptations: string[]             // List of modality-driven layout changes applied
                                               // (e.g., "added voice pause point before Attribution Strip")
  }
}
```

**ComponentInstruction is the rendering contract.** The rendering layer receives this and builds the UI. It does NOT re-evaluate adaptive logic — the Composition agent's decisions are final for this turn.

**Modality coordination invariant:** In voice/hybrid mode, every visual component with non-trivial information content MUST have a corresponding `VisualNarration` entry. The rendering layer uses `sync_point` to coordinate animation timing with voice playback. In text-only mode, `visual_narrations` is empty — the conversational `response_text` from the Conversational agent carries all textual content.

### 2.7 State Agent — Input

```
StateInput {
  // FROM CONVERSATIONAL AGENT
  dimension_updates: DimensionUpdate[]         // Batch of 0–N dimension captures
  state_signals: StateSignal[]                 // Literacy/trust/emotion/engagement observations

  // FROM SESSION
  session_metadata: {
    turn_number: number,
    session_duration: duration,
    input_modality: enum,                      // voice | text | tap | mixed
    dwell_times: DwellTime[]                   // Per-component dwell times from previous turn's render
  }
}
```

### 2.8 State Agent — Output

```
StateOutput {
  // ALWAYS PRESENT
  user_state: UserState {
    literacy: { band: 1-5, confidence: float }
    trust: { level: New|Warming|Trusting|Dependent, confidence: float }
    emotion: { state: Calm|Anxious|Overwhelmed|Excited, confidence: float }
    engagement: { mode: Conversational|Visual|Hybrid, confidence: float }
  }
  
  journey_state: JourneyState {
    current_phase: number,
    current_beat: string,
    captured_dimensions: Map<DimensionId, CapturedValue>,
    completed_gates: GateId[],
    re_entry_scenario: A|B|C|D|E|null
  }

  // CONDITIONAL
  state_delta: StateDelta                      // What changed this turn (for logging, debugging, replay)
}
```

**Single source of truth invariant:** Other agents read `UserState` and `JourneyState` from State's output. They do NOT maintain their own copies or override State's values.

---

## 3. Invocation Flow

### 3.1 Per-Turn Dependency Graph

Not a fixed pipeline. A dependency graph — the orchestrator can parallelize where dependencies allow.

```
User Input
    │
    ▼
┌─────────────────┐
│  Conversational  │ ← needs: user_utterance, dialogue_history, current_user_state
│                  │ → produces: response_text, extracted_dimensions, state_signals
└────────┬────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌────────┐ ┌────────────┐
│ State  │ │Computation │  ← these CAN run in parallel if no dependency
│        │ │(if needed) │     State needs: dimension_updates from Conversational
└───┬────┘ └─────┬──────┘     Computation needs: captured_dimensions from State
    │            │             (but may use stale state if dimensions haven't changed)
    └─────┬──────┘
          ▼
   ┌──────────────┐
   │ Composition   │ ← needs: user_state (from State) + computed_values (from Computation)
   │               │         + conversational_context (from Conversational)
   └──────┬───────┘
          ▼
    Render to User
```

### 3.2 Invocation Rules

These are invariants — the orchestrator MUST respect them regardless of optimization strategy:

| Rule | Rationale |
|:---|:---|
| Conversational fires first on every user turn | It's the only agent that processes raw user input |
| State receives Conversational's output before Composition fires | Composition needs the updated user state, not stale state |
| Computation fires only when the phase requires numerical outputs | Phases 1 and late Phase 5 don't need Computation |
| Composition fires last | It consumes all other agents' outputs |
| Batch dimension updates are atomic | If Conversational extracts 4 dimensions, State processes all 4 before any downstream consumer reads state |

### 3.3 What the Orchestrator Decides (Not This Spec)

- Whether State and Computation run in parallel or sequentially on a given turn
- Whether to cache Computation results across turns when dimensions haven't changed
- Whether to pre-compute likely Composition outputs while the user is still typing
- How to manage context window size for dialogue history
- Whether to use streaming responses or wait for full agent completion
- Connection pooling, retry logic, timeout values

These are engineering decisions. This spec defines the dependency invariants — the orchestrator optimizes within them.

### 3.4 Turns That Skip Agents

Not every turn invokes all 4 agents. The orchestrator should evaluate per-turn:

| Scenario | Agents Invoked | Why |
|:---|:---|:---|
| User sends a greeting or off-topic message | Conversational + State (signal only) | No dimensions captured, no computation needed, no new components |
| User confirms a §C1 Light Confirm | Conversational + State + Composition | Dimension captured, state updates, but no new computation needed (existing range still valid) |
| User edits via §C4 Edit-in-Place | Conversational + State + Computation + Composition | Full pipeline — edit changes a dimension, which changes the target, which changes the render |
| User taps "Looks right ✓" at phase gate | Conversational + State + Composition | Phase transition. State updates journey phase. Composition renders next phase's entry. |
| AI is in "silent" mode (Phase 3 post-reveal) | None until user acts | The AI is waiting. No agent fires. |
| User challenges AI reasoning (see §3.6) | Deliberation loop — potentially multiple rounds | Conversational may trigger Computation re-run before finalizing response. See §3.5 and §3.6. |

### 3.5 Deliberation Loops — Multi-Agent Rounds Before Rendering

The dependency graph in §3.1 shows the happy path — one pass through the agents per user turn. But some turns require **internal deliberation** — multiple agent round-trips before producing user-facing output.

**When deliberation loops occur:**

| Trigger | What Happens | Max Rounds |
|:---|:---|:---|
| **User challenges a computed value** ("7 months seems too high") | Conversational flags a `recomputation_request`. Orchestrator re-invokes Computation with adjusted parameters or reasoning emphasis. Conversational receives recomputation output and formulates defense/concession. | 2 (Conversational → Computation → Conversational → Composition) |
| **Composition detects missing data** (phase needs computed values but Computation wasn't invoked) | Composition returns a `data_dependency` error. Orchestrator routes to Computation, then re-invokes Composition. | 1 (Composition → Computation → Composition) |
| **Constraint violation detected post-agent** (DETERMINISTIC trigger missed, guardrail breach) | Orchestrator re-invokes the violating agent with constraint reinforcement. | 1 (re-invocation with explicit constraint) |
| **Conversational needs computation to formulate response** (user asks "what if I saved ₹10K/month instead?") | Conversational identifies a what-if query, emits a `recomputation_request` with hypothetical parameters. Computation runs. Conversational reformulates response with the result. Composition renders. | 2 |

**Critical constraint: The user sees NOTHING during deliberation.** All multi-agent rounds complete before any output reaches the rendering layer. The user experiences a single response, not a visible back-and-forth between agents.

**Latency budget:** The orchestrator must enforce a total deliberation time ceiling. If deliberation exceeds the acceptable latency window (engineering decides the threshold, likely 2–4 seconds), the orchestrator terminates the loop and uses the best-available output from the last completed round. The Conversational agent MAY signal to the user that it needs time ("Let me check that...") if the orchestrator anticipates a multi-round deliberation.

**Invariants:**
1. Deliberation loops are invisible to the user — they see a single cohesive turn, not intermediate agent outputs.
2. The orchestrator caps loop depth — no unbounded recursion between agents.
3. State is only persisted once per user turn, after deliberation completes — intermediate states during deliberation are ephemeral.
4. If deliberation is interrupted by the latency ceiling, the response must still satisfy all MUST guardrails. Degraded quality is acceptable; guardrail violation is not.

### 3.6 User Challenge Handling — The "Push Back" Pattern

When a user challenges the AI's reasoning ("Why 7 months? My friend only needs 3", "That allocation doesn't make sense for me", "₹8K/month is too much"), this isn't a data capture turn or a standard conversational turn. It's a **contestation** — the AI must reason in real time to defend, explain, concede, or adjust.

**Why this needs special treatment:** The Conversational agent must access the *reasoning behind* the output being challenged, not just the output itself. If the user challenges the EF target, the agent needs the `dimension_attributions` and `calibration_reasoning` from the Computation agent's output. If the user challenges an allocation, it needs the allocation logic. This means the Conversational agent, in challenge mode, receives richer context than on a normal turn.

**Challenge handling flow:**

```
User: "7 months seems way too high"
    │
    ▼
Conversational Agent (challenge detection):
    → Classifies: this is a contestation about ef_target
    → Retrieves: relevant dimension_attributions from last ComputationOutput
    → Retrieves: relevant behavioral principle from grounded_context (§5.6)
    → Reasons: is the user's pushback based on a misconception, personal context,
               or a legitimate edge case?
    │
    ├── If defense: explain the reasoning, cite the driving dimensions.
    │   "Your single-earner status and uninsured parent are the two biggest factors.
    │    Without those, you'd be closer to 4–5 months."
    │   turn_intent: defend
    │
    ├── If concession: user has valid context the model missed.
    │   "You're right — if your parents have their own pension and health cover,
    │    that changes things. Let me adjust."
    │   turn_intent: concede
    │   → Emits recomputation_request → deliberation loop (§3.5)
    │   → Revised target renders with updated attribution
    │
    └── If adjustment: user wants a different plan, not a different number.
        "The target stays, but we can stretch the timeline. ₹4K/month instead of
         ₹8K means 14 months to your first milestone instead of 7."
        turn_intent: adjust
        → Emits recomputation_request with adjusted contribution parameter
```

**Key behavioral guardrails for challenge handling:**
1. **Never dismiss.** Every pushback gets a substantive response. "Trust me" is never acceptable.
2. **Cite the dimensions.** Defense should reference the specific factors driving the output, with the user's own data.
3. **Concede when warranted.** If the user reveals context the model didn't have, adjust. User autonomy > model confidence.
4. **Latency discipline.** Challenge handling is where latency matters most — a slow response to a pushback feels like the AI is "making something up." The orchestrator should prioritize these turns.
5. **Knowledge grounding (§5.6) is critical here.** The Conversational agent may need to reason over behavioral framework principles (why loss framing was used, why the target is anchored this way) to formulate a credible defense. This context is injected via the `grounded_context` field.

---

## 4. Error & Fallback Taxonomy

### 4.1 Failure Mode Categories

Three categories. Each has a different recovery posture:

| Category | Definition | User Impact | Recovery Posture |
|:---|:---|:---|:---|
| **Soft failure** | Agent produces output, but with low confidence or partial data | User sees a degraded but functional experience | Proceed with conservative defaults. Flag internally for monitoring. |
| **Hard failure** | Agent fails to produce usable output (timeout, malformed output, exception) | User's current turn is blocked | Retry once. If retry fails, use fallback. Never silently drop. |
| **Constraint violation** | Agent produces output that violates a MUST guardrail | User would see a harmful or incorrect experience | Block the output. Re-invoke with explicit constraint reinforcement. If re-invocation fails, use safe static fallback. |

### 4.2 Per-Agent Failure Modes

#### Conversational Agent

| Failure Mode | Category | Recovery |
|:---|:---|:---|
| Extraction failure (can't parse dimensions from utterance) | Soft | Proceed with response_text only. Ask a clarifying follow-up. No dimensions captured this turn. |
| Low confidence extraction (parsed a value but unsure) | Soft | Include the extraction with confidence score. Downstream consumers (State, Composition) can treat low-confidence values as provisional — flag for user confirmation. |
| Response generation failure | Hard | Retry once. If fails, fall back to a generic safe acknowledgment: "I didn't quite get that — could you rephrase?" |
| Guardrail violation (e.g., names a specific bank) | Constraint | Block output. Re-invoke with guardrail reinforcement. If second attempt violates, use a static safe response for the current phase. Log for review. |

#### Computation Agent

| Failure Mode | Category | Recovery |
|:---|:---|:---|
| Missing required dimension (can't compute target) | Soft | Return partial output with a `missing_dimensions` list. The orchestrator routes back to Conversational to capture the missing data. |
| Calibration failure (LLM range resolution times out or produces nonsensical range) | Soft | Fall back to midpoint of each dimension's range. Flag `calibration_reasoning` as "midpoint_fallback". The target will be less personalized but structurally sound. |
| Math error (allocation splits don't sum to 100%, dates are in the past) | Constraint | This is a bug, not a runtime failure. Block output. Log with full input for debugging. Use last-known-good computation if available. |

#### Composition Agent

| Failure Mode | Category | Recovery |
|:---|:---|:---|
| Component selection failure (can't decide which components to render) | Hard | Fall back to the phase's mandatory component set at default configuration. No adaptive variants — just the baseline experience. |
| Density conflict (too many mandatory components for the viewport) | Soft | Apply the conflict resolution hierarchy from §1.5: drop Optional first, collapse Conditional, reduce density. If still over limit, stack with scroll rather than suppress Mandatory components. |
| DETERMINISTIC trigger missed (Starter Shield should fire but Composition suppresses it) | Constraint | This is caught by APV4 verification. DETERMINISTIC triggers should be enforced outside the Composition agent's discretion — either hardcoded in the orchestrator or as a post-Composition validation step. |

#### State Agent

| Failure Mode | Category | Recovery |
|:---|:---|:---|
| Adaptive dimension inference failure (can't assess literacy/trust/emotion) | Soft | Hold previous turn's values. If first turn (no previous), default to conservative: literacy=3, trust=New, emotion=Calm, engagement=Hybrid. |
| State persistence failure (can't save journey state) | Hard | Block the turn. The user cannot proceed if state isn't persisted — otherwise re-entry (Scenario D) breaks. Surface a retry UX. This is the one failure mode where "try again" is the only safe option. |
| Conflicting dimension updates (two signals contradict) | Soft | Prefer the higher-confidence signal. If confidence is equal, prefer the more recent signal. Log the conflict for review. |

### 4.3 Graceful Degradation Hierarchy

When the system is in degraded mode, the user experience falls through these levels:

```
Level 0: Full adaptive experience (all agents healthy)
   ↓
Level 1: Reduced personalization (State or Composition partially failing)
          → Default adaptive dimensions, mandatory components only, no emotional overrides
   ↓
Level 2: Static experience (Composition fully failing)
          → Pre-rendered phase templates with computed values injected
          → Still conversational, still personalised numbers, but fixed layout
   ↓
Level 3: Conversational-only (Composition + Computation failing)
          → Text-only conversation, no inline cards or visualisations
          → AI can still converse and capture data, but can't render the experience
   ↓
Level 4: Error state (Conversational or State failing)
          → Static error message: "Something went wrong. Your data is safe. 
             Let's try again in a moment."
          → Retry button. No data loss.
```

**Invariant:** User data is NEVER lost during degradation. The State agent's persistence is the last thing to fail — if it fails, the system stops rather than risk data loss.

---

## 5. Prompt Architecture

### 5.1 Prompt Structure — Universal Pattern

Every agent's prompt follows the same structural pattern, with content that varies per agent:

```
┌─────────────────────────────────────────┐
│  SYSTEM PROMPT (stable across turns)     │
│                                          │
│  ┌──── Identity ────────────────────┐   │
│  │ Who you are. What you do.         │   │
│  │ What you DON'T do.                │   │
│  └──────────────────────────────────┘   │
│                                          │
│  ┌──── Constitutional Guardrails ───┐   │
│  │ MUST constraints (from BS-001 §4) │   │
│  │ MUST NOT constraints              │   │
│  │ These NEVER change per turn.      │   │
│  └──────────────────────────────────┘   │
│                                          │
│  ┌──── Capabilities ────────────────┐   │
│  │ What tools/functions are available │   │
│  │ Output schema definition          │   │
│  │ Phase-specific component palette  │   │
│  └──────────────────────────────────┘   │
│                                          │
├─────────────────────────────────────────┤
│  CONTEXT BLOCK (per-turn, injected)      │
│                                          │
│  ┌──── User State ──────────────────┐   │
│  │ Current adaptive dimensions       │   │
│  │ Journey state (phase, beat, data) │   │
│  └──────────────────────────────────┘   │
│                                          │
│  ┌──── Turn Context ────────────────┐   │
│  │ Dialogue history (windowed)       │   │
│  │ Upstream agent outputs (if any)   │   │
│  │ Per-turn instructions             │   │
│  └──────────────────────────────────┘   │
│                                          │
├─────────────────────────────────────────┤
│  USER MESSAGE (this turn's input)        │
└─────────────────────────────────────────┘
```

### 5.2 Separation Principle

| Prompt Layer | Changes When | Managed By |
|:---|:---|:---|
| **Identity + Guardrails** | Product decision (rare — decision-log entry required) | Product team. Embedded at deploy time. |
| **Capabilities + Output Schema** | Phase transition or feature release | Orchestrator. Swapped when journey phase changes. |
| **User State + Turn Context** | Every turn | Orchestrator. Injected from State agent output + dialogue buffer. |
| **User Message** | Every turn | Passed through from user input. |

**Why this matters:** Guardrails are constitutional — they don't drift, they don't get pushed out of the context window, and they don't get overridden by user messages or context. The separation ensures that a long conversation doesn't gradually erode the system prompt's constraints through context window pressure.

### 5.3 Guardrail Embedding — Not Post-Hoc Checking

BS-001 §4 constraints (MUSTs, SHOULDs, NEVERs) are embedded in the system prompt, not checked after the agent responds. This is the design principle from the objective document's Guiding Constraint #2.

**Rationale:** Post-hoc checking creates a generate-then-filter pattern that wastes compute and creates UX latency. Embedding constraints in the system prompt means the agent reasons *within* the constraint space, not outside it.

**Exception:** DETERMINISTIC triggers (e.g., Starter Shield) are verified post-Composition as a safety net. The Composition agent SHOULD fire them based on its system prompt, but the orchestrator MUST verify and enforce them regardless. This is the one case where post-hoc checking is warranted — because the consequence of missing a DETERMINISTIC trigger is a constraint violation, not just suboptimal output.

### 5.4 Adaptive Behaviour Tables in Prompts

The adaptive behaviour tables from BS-001 (§3.x.7 / §3.x.8 sections) are encoded as structured decision guidance within the Composition agent's system prompt — not as if/else rules.

**Encoding principle:** Present the tables as "here are the dimensions you consider and the rendering options available." Let the agent reason about the right combination, rather than prescribing a lookup table. The tables are *training examples for the agent's judgment*, not *code for the agent to execute*.

**Why this matters:** Rigid if/else encoding makes the Composition agent a rules engine with extra steps. The tables should inform the agent's reasoning without collapsing its adaptive capacity. If the right answer is a rules engine, build a rules engine — don't use an LLM to execute hardcoded rules.

### 5.5 Per-Agent Prompt Notes

| Agent | Key Prompt Design Consideration |
|:---|:---|
| **Conversational** | System prompt includes the full phase-specific AI Intent & Guidance (BS-001 §3.x.2). This is the agent's "script" — intent and constraints, not exact words. The agent improvises within it. |
| **Computation** | May not need a traditional LLM prompt at all for deterministic outputs. The semi-deterministic calibration step is the only part that requires LLM reasoning. Consider: prompt for calibration only, pure function for everything else. |
| **Composition** | Largest system prompt. Includes: component palette, adaptive behaviour guidance, viewport rules, mandate vocabulary, conflict resolution hierarchy. This prompt benefits most from structured formatting (tables, clear section headers) since the agent must reason across multiple dimensions simultaneously. |
| **State** | System prompt focuses on inference methodology: how to update adaptive dimensions from signals (not rules — the agent observes and infers). Includes: dimension definitions, confidence update rules, default-to-conservative principle. |

### 5.6 Knowledge Grounding Layer

The agents don't reason in a vacuum. Some turns require access to **reference knowledge** beyond what's in the system prompt and per-turn context — product philosophy, behavioral science principles, domain-specific facts about Indian personal finance, and the reasoning traces behind previous outputs.

**The problem this solves:** When a user asks "Why do you recommend liquid funds instead of FDs?" or challenges "Your target seems too conservative," the Conversational agent needs to reason over product philosophy (advisory-only model, risk framing principles) and domain knowledge (FD vs. liquid fund characteristics). Embedding ALL of this in the system prompt is impractical — it would blow the context window and dilute the guardrails.

**Architecture: Retrieval-augmented, not embedded.**

The orchestrator maintains a knowledge index over reference documents (behavioral-framework.md, emergency-fund-philosophy.md, design-principles.md, domain facts). Per turn, before invoking the Conversational agent, the orchestrator:

1. **Classifies the turn's knowledge needs** based on the user utterance and current phase.
2. **Retrieves relevant passages** — not entire documents, but focused chunks.
3. **Injects them as `grounded_context`** in the Conversational agent's input (see §2.1).

```
GroundedContext {
  source: string                               // Source document identifier
  passage: string                              // Retrieved text passage
  relevance: string                            // Why this was retrieved (one-line)
  retrieval_trigger: automatic | challenge      // Was this proactively retrieved or triggered by user pushback?
}
```

**What gets grounded and when:**

| Trigger | Knowledge Retrieved | Consuming Agent |
|:---|:---|:---|
| Phase entry | Phase-specific behavioral guardrails, AI Intent guidance from BuildSpec | Conversational |
| User challenge / pushback | Relevant behavioral principle (loss aversion, anchoring, etc.), dimension attribution reasoning from last Computation output | Conversational |
| Value-after moment | Domain facts relevant to the dimension cluster just captured (e.g., DICGC limit, liquid fund characteristics) | Conversational |
| Composition needs guidance | Relevant adaptive behaviour table, component spec for the current phase | Composition |
| Novel user question | Domain FAQ-style knowledge ("What's a liquid fund?", "Is my money safe?") | Conversational |

**What this is NOT:**
- Not a full RAG system with vector embeddings at launch. The knowledge corpus is small enough (< 50 documents) that keyword/semantic matching over structured document chunks is sufficient.
- Not a replacement for the system prompt. Constitutional guardrails and agent identity live in the system prompt. Grounded context provides per-turn supplementary knowledge.
- Not visible to the user. The user never sees "I retrieved document X." They see a well-reasoned, contextually grounded response.

**Invariant:** Grounded context is *reference material*, not *instruction*. The agent reasons *over* retrieved passages — it doesn't blindly follow them. If a retrieved passage conflicts with a system prompt guardrail, the guardrail wins.

---

## 6. Extension Points

This framework is designed to grow. The following are anticipated extension paths — not commitments:

### 6.1 New Journeys

When a new journey (J02, J03…) is added:
- New `phase_component_palette` entries are added to the Composition agent's capabilities
- New phase-specific guardrails are added to the Conversational agent's system prompt
- The Computation agent gets new `computation_type` values
- The State agent's `JourneyState` gains new journey-specific fields
- **No contract shapes change.** The schemas are journey-agnostic by design.

### 6.2 New Agents

If a 5th agent concern emerges (e.g., a dedicated Compliance agent):
- Add it to the dependency graph
- Define its Input/Output schema following the same pattern
- Add it to the invocation rules with explicit dependency declarations
- The existing 4 contracts don't change — the new agent slots in.

### 6.3 Multi-Model Topology

If different agents run on different LLMs:
- The contracts don't change — they're model-agnostic
- The orchestrator manages model routing
- Prompt architecture per agent may need model-specific formatting adjustments (this is an orchestrator concern, not a contract concern)

---

## 7. Relationship to Deferred Items

| Deferred Item | How This Framework Addresses It |
|:---|:---|
| AG-02 (Composition fallback) | §4.2 Composition Agent failure modes + §4.3 degradation hierarchy |
| AG-03 (Error/recovery taxonomy) | §4 in full |
| AG-04 (Batch capture protocol) | §2.2 batch capture invariant + §3.2 invocation rules |
| AG-05 (Performance budgets) | Explicitly deferred to engineering — §3.3 declares this an orchestrator concern |
| AG-06 (Computation model formula) | Out of scope. This framework defines the Computation agent's I/O boundary (§2.3, §2.4). The internal formula is a separate artifact. |

---

## 8. Verification Interface

Each contract in §2 implies testable assertions. The dev agent can verify:

| What to Verify | How |
|:---|:---|
| **Schema compliance** | Every agent output conforms to its Output schema. Required fields are present. Types are correct. |
| **Dependency satisfaction** | No agent fires until its required inputs are available. Composition never fires before State has processed Conversational's output. |
| **Batch atomicity** | When Conversational extracts N dimensions, State processes all N before any downstream read. |
| **Deterministic reproducibility** | Given the same `ComputationInput`, the deterministic outputs are identical across invocations. Semi-deterministic outputs may vary but must carry `calibration_reasoning`. |
| **Guardrail enforcement** | MUST constraints from BS-001 §4 are present in system prompts. DETERMINISTIC triggers fire regardless of Composition agent output. |
| **Degradation correctness** | At each degradation level (§4.3), the user sees the expected fallback experience — not a blank screen, not stale data, not a guardrail violation. |
| **BS-001 BV/APV alignment** | Every BV and APV criterion in BS-001 §6 should be expressible as an assertion on agent outputs defined in this spec's §2. |

---

## Open Questions for Engineering

These are genuine questions — not placeholders for decisions already made:

1. **State agent persistence granularity:** Per-turn snapshots or per-beat? Per-turn is safer for replay but more storage. Per-beat is more practical but makes mid-beat recovery harder.

2. **Computation agent LLM boundary:** Should the semi-deterministic calibration step be a separate LLM call from the deterministic math? Or should the entire Computation agent be one call where the LLM does calibration and the deterministic math is embedded as tool use?

3. **Composition agent context budget:** The Composition agent receives the richest context. At what point does context volume degrade its reasoning quality? Is there a practical ceiling where summarization of upstream context outperforms raw injection?

4. **Streaming and partial renders:** Can the Composition agent emit partial `ComponentInstruction` sets while the Computation agent is still running? This would enable progressive rendering (show the conversation response immediately, then materialize cards when computation finishes). The contracts support this — but the orchestrator would need to manage partial state.

5. **Offline / poor connectivity:** The contracts assume synchronous agent invocation. If the user is on a flaky connection, should the orchestrator cache and replay? Or should there be a fundamentally different offline mode? This is a product question masquerading as an engineering one.

6. **Deliberation loop latency budget:** What's the acceptable ceiling for multi-agent deliberation before showing the user a response? 2 seconds? 4 seconds? Should the system show a "thinking" indicator during deliberation, and if so, at what delay threshold? This affects challenge handling (§3.6) most directly.

7. **Knowledge grounding index strategy:** At launch, the knowledge corpus is < 50 documents — simple chunking + keyword/semantic matching suffices. At what corpus size does this need to upgrade to a proper vector store? And should the orchestrator pre-fetch likely-needed knowledge at phase entry (proactive) or only retrieve on demand (reactive)?

8. **Voice narration generation latency:** The Composition agent produces `visual_narrations` for voice/hybrid mode (§2.6). Is TTS latency additive to the agent pipeline, or can TTS synthesis run in parallel with visual rendering? If additive, it may push total turn latency past acceptable thresholds for complex turns with multiple visual components.

9. **Challenge detection accuracy:** The Conversational agent must distinguish between a genuine challenge ("7 months is too high"), a question ("Why 7 months?"), and an adjustment request ("Can we do less?"). Misclassifying a question as a challenge triggers unnecessary deliberation; misclassifying a challenge as a question produces a weak response. What's the acceptable misclassification rate, and should there be a fallback where ambiguous cases default to the richer (challenge) handling?

---

> *This framework defines interface contracts — the "what crosses the boundary." Implementation choices within each agent and within the orchestrator are engineering decisions, not product decisions, unless they violate the invariants defined here.*
>
> *Referenced from: `strategy/buildspecs/_index.md`*
> *Source objective: `brain/9fb859ce/agent-invocation-contracts-objective.md`*
