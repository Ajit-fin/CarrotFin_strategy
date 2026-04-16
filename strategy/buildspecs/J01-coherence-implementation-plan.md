# J01 Coherence Fixes — Implementation Plan

> **Goal:** Address all 9 review findings from `j01_coherence_review.md` in logical batches. Avoid context overload. Each batch is self-contained and leaves the artifact suite internally consistent before the next batch begins.

---

## Sequencing Rationale

The changes form a dependency chain:

```
Batch 1: Agentic Architecture (CG01 foundation)
    ↓  establishes the framing everything else references
Batch 2: User-State Inference (LLM-native signals)
    ↓  defines HOW the adaptive system works
Batch 3: Component Spec Updates (CP01–CP05 + D3 move)
    ↓  applies the new framing to all component specs
Batch 4: Scenario Coverage + Handover Notes
       wraps up edges and collaboration notes
```

---

## Batch 1: Agentic Architecture Foundation

**Files modified:** `CG01-composition-grammar.md`  
**New files:** None (integrated into CG01 as new sections)

### Changes:

#### 1a. CG01 §0 — Reframe rendering rules as guardrails

Add explicit framing to CG01 §0.1 that:
- Rendering rules (§2) are **guardrails for agent context**, not decision trees to hardcode
- The agent's value is handling intersections, edge cases, and nuance the matrix doesn't cover
- Engineering must NOT implement §2 as `if-else` cascades — the agent receives these rules as structured context and makes rendering decisions within their bounds

#### 1b. CG01 §0 — Agent Interface Architecture (new §0.3)

Define the structural contract between agents at the architectural level:

- **Agent roles:** Conversational, Computation, Composition, State (from J01 §0)
- **Communication pattern:** How agents exchange context — structured JSON context blocks, NOT hardcoded enum handoffs
- **State agent protocol:** Explicitly support batch updates (multi-slot capture from single user turn) — NOT sequential slot-filling
- **Composition agent contract:** Receives user state + journey state + computed values as context, returns component selection + configuration — NOT enum lookups

#### 1c. CG01 §0 — Multi-info turn batch protocol

In §0.3, explicitly state that:
- The state agent accepts **batch dimension updates** (a single user utterance can update 0-N dimensions simultaneously)
- This is trivially handled by LLM extraction — the state update protocol must NOT be built as a sequential slot-filler
- The BYP indicator (CP02) animates multiple pill fills when batch captures occur

**Validation:** Read CG01 after batch is complete — verify §0 flows logically with existing §1–§4.

---

## Batch 2: User-State Inference — LLM-Native

**Files modified:** `CG01-composition-grammar.md` (new §5)

### Changes:

#### 2a. CG01 §5 — User-State Inference Architecture

Add a new section defining how the four adaptive dimensions are inferred. The key principle: **use LLM intelligence for inference, not hardcoded heuristic scores.**

| Dimension | Inference approach |
|:---|:---|
| **Financial literacy** | LLM observes vocabulary, question depth, response speed, interaction modality. Outputs a band (1–2 / 3 / 4–5) with confidence. Reassessed continuously — NOT scored once on entry |
| **Trust level** | Progressive — advances based on data shared, recommendations accepted, return frequency. LLM assesses qualitatively from session history, NOT from a point-scoring formula |
| **Emotional state** | LLM infers from conversational signals (word choice, pacing, topic sensitivity). Updated per-turn. NOT from quantitative metrics like tap speed |
| **Engagement mode** | LLM observes modality preference over the last N turns. Updated continuously |

The structure should define:
- **What the LLM observes** (signal types, not scoring formulas)
- **What it outputs** (dimension band + confidence)
- **How it's consumed** (composition agent receives current state as context)
- **When it updates** (per-turn for emotional state, per-cluster for literacy, per-session for trust)

This deliberately avoids hardcoded thresholds. The LLM's contextual reasoning IS the scoring mechanism.

**Validation:** Verify the new §5 is referenced by §2 (rendering rules) — §2 says "at literacy 1–2, render X" and §5 now says "how the agent determines the user is at literacy 1–2."

---

## Batch 3: Component Spec Updates

**Files modified:** CP01, CP02, CP03, CP04, CP05, CG01 (Phase 1 note)  
**Structural change:** Move D3 Confirmed Card from CP05 → CP03

### Changes:

#### 3a. Illustrative framing note — all CP specs

Add a standardised framing note at the top of each component spec (CP01–CP05):

> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

Default assumption: layouts are **illustrative** unless explicitly marked otherwise. Where a layout IS canonical (e.g., the gradient strip zone proportional-width rule in CP04), it's already marked as such.

#### 3b. Move D3 Confirmed Card: CP05 Appendix A → CP03 State 4

1. Add D3 Confirmed Card as State 4 in CP03's state machine table
2. Move the full spec (Flutter mapping, structural layout, visual design, interaction, composition) from CP05 Appendix A into CP03
3. Remove Appendix A from CP05
4. Update CP03's scope note (currently references "D3 Confirmed Card — to be specced separately")
5. Update CP04's D3 scope note (currently says "see CP05 or appendix to CP03" — change to definitively point to CP03)
6. Verify CP05's D3 buffer references still point correctly (CP05 references D3 flowing into Phase 4/5 — this stays)

#### 3c. Phase 1 — Mark as Agent-Improvised

Based on the agentic architecture established in Batch 1, and the overall approach of LLM-native composition:

- Update CG01 Phase 1 from `[DEFERRED — not in scope for this build]` to an explicit **Agent-Improvised** designation
- Add a brief note: Phase 1 components are composed by the agent from M3 primitives using the Phase 1 LLM prompt as sole guidance. No pre-defined component specs. The LLM prompt (in J01 interaction specs) provides intent, constraints, and component descriptions — the agent improvises the rendering.
- This aligns with the overall vision: Phase 1 is the most conversational, least structured phase — it SHOULD be the most agent-driven.

**Validation:** After batch completion, verify all cross-references between CP03, CP04, CP05 are clean. Verify CG01 Phase 1 note is consistent with J01 interaction spec Phase 1 component table.

---

## Batch 4: Scenario Coverage + Handover Notes

**Files modified:** CP02, CP04, CP05, J01 interaction specs (handover section)

### Changes:

#### 4a. Re-entry scenario notes — CP02, CP04, CP05

Add brief, LLM-intelligent notes for Scenario B/C/E handling. These are NOT prescriptive rendering rules — they are **context the composition agent uses to make rendering decisions:**

| Component | Note |
|:---|:---|
| CP02 (BYP) | For Scenario B/E: agent may pre-fill known dimensions. BYP starts partially filled. Agent decides which pills are pre-filled based on what data is known from prior session or user-stated corpus |
| CP04 (Allocation Card) | For Scenario B/E with existing savings: agent should account for existing allocation in its verbal framing. Card layout unchanged — the amounts reflect the new/remaining allocation, not the full target |
| CP05 (Contribution Plan) | For Scenario B/E with existing corpus: milestone dates account for existing progress. Action Card steps reference only the remaining setup needed |

Framing: These are directional notes for the agent, not additional component states. The agent uses existing card states with contextually appropriate data.

#### 4b. Engineering collaboration note

Add a handover note to J01 interaction specs (the "Handover Notes" sections) flagging:

> **Agent interface schema — open collaboration point:** The input schema (user state, journey state, computed values) and output schema (component selection, configuration, render instructions) between agents is a joint product-engineering decision. Product provides the contract requirements (this document). Engineering defines the technical schema. This must be co-designed — it cannot be product-only or engineering-only.

Place this in the Phase 2 handover notes (where the multi-agent coordination is most complex).

**Validation:** Full read-through of modified sections to verify no orphaned references or inconsistencies.

---

## Execution Order

| Step | Batch | Estimated scope |
|:---|:---|:---|
| 1 | Batch 1 | CG01: ~3 new sub-sections in §0 |
| 2 | Batch 2 | CG01: 1 new section (§5) |
| 3 | Batch 3 | CP01–CP05 (framing note), CP03+CP05 (D3 move), CG01 (Phase 1 note) |
| 4 | Batch 4 | CP02, CP04, CP05, J01 interaction specs (light additions) |

Each batch leaves the artifact suite internally consistent. If we stop after any batch, the system is coherent (just not yet complete).

---

## Open Questions

None. All user decisions are clear from the review comments:
- Rendering rules = guardrails ✓
- Agent interface = structural, solve now ✓
- Multi-info turn = fix structurally ✓
- User-state inference = LLM-native, not hardcoded ✓
- Phase 1 = agent-improvised (aligns with overall solution) ✓
- Layouts = illustrative unless specific value tradeoff ✓
- D3 move = implement + check references ✓
- Agent schema = collaboration note for handover ✓
- Re-entry = balance prescriptive vs. LLM intelligence ✓
