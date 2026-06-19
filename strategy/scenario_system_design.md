# Scenario System Design — Engineering Flows

> **Scope:** High-level engineering flows derived from [Scenario UX Design](file:///Users/kshekhaw/.gemini/antigravity/brain/e17a4252-6bfa-4fe9-aae7-b7f385c972b8/scenario_ux_design.md) product requirements.  
> **Companion to:** [System Entity Analysis](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/system_entity_analysis.md) (entity blueprint)  
> **Date:** 2026-05-18  
> **Status:** In progress — §1 complete

---

## §1 — Scenario Request Lifecycle

Four distinct lifecycle patterns exist for scenario requests. Each maps to different entity involvement, different data paths, and different user-perceived interaction speeds.

### Pattern Overview

| Pattern | Trigger | Entities Involved | Pro? | Typical Scenario Types |
|---|---|---|---|---|
| **A: Numeric What-If** | Voice/text with modifier | Flash → BE → DCE | No | Single-variable, multi-variable, removal |
| **B: Comparison** | Voice/text with `compare` | Flash → BE → DCE (parallel) | No | A vs B (up to 3 alternatives) |
| **C: Life Event** | Categorical modifier or multi-domain impact | Flash → BE → Pro → Flash → BE → DCE | Yes | City move, job change, new dependent |
| **D: Slider** | Screen interaction (tap + drag) | FE → BE → DCE | No | Single-variable numeric exploration |

---

### Pattern A: Numeric What-If

**Covers:** Single-variable, multi-variable (up to 3), removal scenarios. The most common pattern (~80% of scenario interactions).

```mermaid
sequenceDiagram
    participant User
    participant FE as Frontend
    participant BE as Backend
    participant FlashExt as Flash Extraction
    participant FlashConv as Flash Conversation
    participant FM as Field Mapper
    participant DCE
    participant ScS as Scenario Store
    participant Comp as Compositor

    User->>FE: "What if I save 5K more per month?"
    FE->>BE: User input

    par Parallel Flash prompts
        BE->>FlashExt: Input + field schema + plan context
        FlashExt-->>BE: intent: what_if, modifiers [{target_field, operation, number, unit}]
    and
        BE->>FlashConv: Input + plan context + user context
        FlashConv-->>BE: response_text + ui_directive {component_id, layout_intent} + intent
    end

    BE->>BE: Merge outputs, validate intent match

    BE->>FM: modifiers (number + unit)
    FM-->>BE: Normalized delta (e.g., 5 × 1000 = 5000)

    BE->>BE: Apply operation to current User State value
    Note over BE: increase: 10000 + 5000 = 15000

    par Parallel DCE calls
        BE->>DCE: Scenario inputs (full field set with override)
        DCE-->>BE: Scenario results
    and
        BE->>DCE: Baseline inputs (current User State)
        DCE-->>BE: Baseline results
    end

    BE->>BE: Compute delta (scenario - baseline)
    BE->>ScS: Create version (parent: baseline, trigger: user_what_if)
    BE->>BE: Bind delta + results to ui_directive component

    BE-->>FE: response_text + enriched ui_directive
    FE->>Comp: Render scenario_comparison (single_focus layout)
    Comp-->>User: Before/after delta card + voice narration
```

**Key flow decisions:**
- **Baseline always computed fresh** alongside scenario — not cached from a prior turn. Baseline values may have changed between turns.
- **Multi-variable:** BE receives multiple modifiers in one turn. All applied to the same DCE call — not sequential. One scenario version created with all overrides.
- **Removal (`remove`):** Target field set to 0 in the scenario input set. DCE doesn't know the difference — it just receives a full input set.
- **The scenario version stores FULL inputs** — not just the delta. This enables independent re-computation and branching without dependency on parent version availability.

---

### Pattern B: Comparison What-If

**Covers:** "Compare X vs Y" scenarios. User wants side-by-side evaluation of 2-3 alternatives.

```mermaid
sequenceDiagram
    participant User
    participant FE as Frontend
    participant BE as Backend
    participant FlashExt as Flash Extraction
    participant FlashConv as Flash Conversation
    participant FM as Field Mapper
    participant DCE
    participant ScS as Scenario Store
    participant Comp as Compositor

    User->>FE: "Compare saving 15K and 20K per month"
    FE->>BE: User input

    par Parallel Flash prompts
        BE->>FlashExt: Input + field schema
        FlashExt-->>BE: intent: what_if, modifiers [{compare_group: "X", ...}, {compare_group: "X", ...}]
    and
        BE->>FlashConv: Input + user context
        FlashConv-->>BE: response_text + ui_directive {layout_intent: "comparison"}
    end

    BE->>BE: Merge outputs
    BE->>FM: All modifiers (normalize numbers)
    FM-->>BE: Normalized values [15000, 20000]

    BE->>BE: Group modifiers by compare_group

    par Parallel DCE calls (baseline + N alternatives)
        BE->>DCE: Baseline inputs
        DCE-->>BE: Baseline results
    and
        BE->>DCE: Alternative A inputs (savings: 15000)
        DCE-->>BE: Alt A results
    and
        BE->>DCE: Alternative B inputs (savings: 20000)
        DCE-->>BE: Alt B results
    end

    BE->>BE: Compute deltas (A vs baseline, B vs baseline, A vs B)
    BE->>ScS: Create 2 versions (same parent, same compare_group)
    BE->>BE: Bind all results to scenario_comparison component

    BE-->>FE: response_text + enriched ui_directive
    FE->>Comp: Render scenario_comparison (comparison layout)
    Comp-->>User: Multi-column comparison table + narration
```

**Key flow decisions:**
- **`compare_group` is the linking mechanism.** Flash emits it; BE groups by it; Scenario Store versions share it. This is how the system knows "these belong together."
- **Baseline is ALWAYS included** as one column — even if the user only said "compare A and B." The baseline provides the anchor for delta annotations.
- **Max 3 alternatives** per comparison (constraint from UX — cognitive overload guardrail). BE validates this.
- **Parallel DCE calls** — baseline + all alternatives fire simultaneously. Each gets a complete input set. No dependency between alternatives.
- **Scenario Store:** Each alternative is a separate version, all branching from the same parent (baseline), all sharing the same `compare_group`. The `get_comparison(compare_group)` operation retrieves them together.

---

### Pattern C: Life Event Scenario (Pro Escalation)

**Covers:** Categorical what-ifs where Flash detects multi-domain cascading impact. City move, job change, new dependent, marriage, health condition.

```mermaid
sequenceDiagram
    participant User
    participant FE as Frontend
    participant BE as Backend
    participant FlashExt as Flash Extraction
    participant FlashConv as Flash Conversation
    participant Pro
    participant DCE
    participant ScS as Scenario Store
    participant Comp as Compositor

    User->>FE: "What if I move to Bangalore?"
    FE->>BE: User input

    par Parallel Flash prompts
        BE->>FlashExt: Input + field schema
        FlashExt-->>BE: intent: what_if, modifiers [{target_field: "city", operation: "replace", text_value: "Bangalore"}]
    and
        BE->>FlashConv: Input + user context
        FlashConv-->>BE: response_text + escalation {needed: true, deviation_type: "goal_requires_decomposition"}
    end

    BE->>BE: Merge outputs — escalation detected
    BE-->>FE: response_text ("That's a big change! Let me think through...")
    Note over FE,User: User sees acknowledgment immediately

    BE->>BE: Retrieve Scenario Store state + full User Profile
    BE->>Pro: Escalation context + profile + scenario state + guardrails

    Note over Pro: ASYNC — 2-5 seconds
    Note over FlashConv,User: Flash occupies user (mini-insight, context)

    Pro->>Pro: Reason about cascading impacts
    Pro-->>BE: New PlanObject {jit_field_definitions, steps: [ASK_INPUT → COMPUTE → SHOW_RESULT]}

    BE-->>FE: New PlanObject pushed to Flash context

    Note over FlashConv,User: Flash resumes with new plan

    loop For each plan step
        FlashConv-->>User: ASK_INPUT ("What would you pay in rent in Bangalore?")
        User->>FE: Answer
        FE->>BE: User input
        BE->>FlashExt: Extract JIT field value
        FlashExt-->>BE: mapped_fields [{field: "expected_bangalore_rent", is_jit: true}]
        BE->>FM: Normalize
        FM-->>BE: Typed value
    end

    BE->>DCE: Full scenario inputs (city overrides + JIT fields + User State)
    DCE-->>BE: Scenario results
    BE->>DCE: Baseline inputs
    DCE-->>BE: Baseline results

    BE->>BE: Compute delta
    BE->>ScS: Create version (trigger: user_what_if, with JIT fields)

    BE-->>FE: SHOW_RESULT directive
    FE->>Comp: Render scenario_comparison
    Comp-->>User: Bangalore vs current city delta card
```

**Key flow decisions:**
- **Escalation trigger is the conversation prompt** — it evaluates whether the categorical modifier requires multi-domain reasoning. The extraction prompt just maps the field + value; it doesn't decide on escalation.
- **Flash stays warm during Pro thinking.** This is "purposeful occupancy" — not dead time. Flash provides contextual micro-content while Pro reasons.
- **Pro defines JIT fields** for scenario-specific data points (e.g., `expected_bangalore_rent`) that don't exist in the core schema. These fields are scoped to this scenario — they don't persist.
- **The plan is multi-step.** Unlike Pattern A (single turn), Pattern C may require 2-3 turns of data collection before DCE can compute. Each turn follows the normal turn lifecycle from the System Entity Analysis.
- **Only after all plan steps complete** does DCE run the full scenario computation. The delta card is rendered only at the end.

---

### Pattern D: Slider Interaction (Screen-Initiated)

**Covers:** Visual numeric exploration via direct manipulation. No voice, no Flash, no extraction. The highest-engagement micro-interaction.

```mermaid
sequenceDiagram
    participant User
    participant FE as Frontend
    participant BE as Backend
    participant DCE
    participant ScS as Scenario Store
    participant Comp as Compositor

    User->>FE: Tap on "Monthly Savings: ₹10,000"
    FE->>Comp: Show slider (range: ₹5K-₹50K, current: ₹10K)
    Comp-->>User: Slider rendered

    loop While user is dragging (debounced ~200ms)
        User->>FE: Slider at ₹18,000
        FE->>BE: {field: "monthly_savings", value: 18000, operation: "replace"}
        Note over BE: Fast-path — no Flash, no Field Mapper
        Note over BE: Value already typed (INR, from slider position)
        BE->>DCE: Scenario inputs (savings: 18000)
        DCE-->>BE: Delta metrics only (goal_date, corpus)
        BE-->>FE: Delta metrics
        FE->>Comp: Update goal date + corpus live
        Comp-->>User: Numbers animate to new values
    end

    User->>FE: Release slider (final value: ₹18,000)
    FE->>BE: Slider released, final value: 18000

    BE->>DCE: Full computation (scenario + baseline)
    DCE-->>BE: Full results
    BE->>ScS: Create version (trigger: user_what_if)
    BE->>BE: Compute full delta + bind to component

    BE-->>FE: Full delta card
    FE->>Comp: Render scenario_comparison (single_focus)
    Comp-->>User: Full before/after delta card
```

**Key flow decisions:**
- **Bypasses Flash entirely.** No extraction needed (field + value come from slider position). No conversation needed (visual-only interaction). This is the fastest path.
- **Bypasses Field Mapper.** Slider sends already-normalized values in INR — no number × unit decomposition needed.
- **Two-phase response:** (1) During drag: lightweight delta metrics only — fast, streaming, no Scenario Store write. (2) On release: full computation, full delta, Scenario Store version created.
- **Debouncing is FE-side.** BE receives ~5 calls/sec max during active sliding. Each call is independent (no session state between drag events).
- **Baseline is cached during slide session.** BE caches the baseline DCE result when the slider opens. During drag, only the scenario DCE call runs — halving the computation. Full baseline recompute happens only on release.
- **Voice rejoins on release** (if voice mode is active). Flash receives the final value and narrates the delta. If voice is inactive, the delta card alone is sufficient.

---

### Cross-Pattern Lifecycle Summary

```mermaid
flowchart TD
    Input["User input (voice/text/screen)"]

    Input --> IsSlider{Screen slider?}
    IsSlider -->|Yes| D["Pattern D: Slider<br/>FE → BE → DCE"]

    IsSlider -->|No| FlashPar["Flash parallel prompts"]
    FlashPar --> HasMod{Has modifiers?}

    HasMod -->|No| NormalTurn["Normal turn lifecycle<br/>(not a scenario)"]

    HasMod -->|Yes| IntentWI{intent = what_if?}
    IntentWI -->|No| NormalTurn

    IntentWI -->|Yes| HasEsc{Escalation needed?}

    HasEsc -->|Yes| C["Pattern C: Life Event<br/>Flash → Pro → Flash → DCE"]

    HasEsc -->|No| HasCompare{compare_group<br/>present?}
    HasCompare -->|Yes| B["Pattern B: Comparison<br/>Flash → BE → DCE ×N"]
    HasCompare -->|No| A["Pattern A: Numeric What-If<br/>Flash → BE → DCE"]
```

**Routing logic lives in the BE merge step.** After merging the two Flash outputs, the BE evaluates:
1. `intent == what_if`? If no → normal turn.
2. `escalation.needed == true`? If yes → Pattern C.
3. Any modifier has `compare_group`? If yes → Pattern B.
4. Otherwise → Pattern A.

The slider (Pattern D) is the only path that doesn't go through Flash at all — it's detected at the FE level and sent directly to a dedicated BE endpoint.

---

### Scenario Store Version Creation — When and What

| Pattern | When version is created | What's stored |
|---|---|---|
| **A** | After DCE returns | Full inputs, full outputs, delta from parent |
| **B** | After all parallel DCE calls return | One version per alternative, all sharing `compare_group` |
| **C** | After final DCE call (post-plan completion) | Full inputs including JIT fields, outputs, delta |
| **D (drag)** | NOT created during drag | — |
| **D (release)** | After full DCE on final value | Full inputs, full outputs, delta from parent |

**In all patterns:** the version stores FULL inputs (not deltas). This is deliberate — it enables re-computation, independent branching, and comparison without needing to walk the version tree to reconstruct inputs.

---

### Scenario-to-Reality Conversion (All Patterns)

After any scenario result is displayed, the user may choose to "keep this" — converting the scenario into their real profile.

```
User: "Yes, let's go with the ₹15K plan"
  │
  ▼
BE:
  1. Read scenario version inputs from Scenario Store
  2. Identify changed fields (delta_from_parent.changed_inputs)
  3. Write changed fields to User State Store as data_input (same path as normal data collection)
  4. Mark scenario version status: "acted_upon"
  5. Flash confirms: "Done — I've updated your savings target to ₹15K."
```

This is intentionally the **same write path as normal data input** — the User State Store doesn't know or care whether a value came from a conversation or a scenario conversion. This keeps the persistence layer simple.

---

*§2 (Backend Orchestrator Logic), §3 (Scenario Store Data Model), §4-§6 to follow in subsequent sessions.*
