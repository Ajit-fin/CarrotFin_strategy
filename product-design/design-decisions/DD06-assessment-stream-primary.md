# DD06: Assessment Stream-Primary — No Composed Surface in Assessment

> **Date:** 2026-04-16  
> **Tension resolved:** T2 (Conversational vs. Visual Primacy) — partially, for the Assessment phase specifically  
> **Assumptions depended on:** C5 (conversational + visual integration feasible), EF-11 (8-dimension sizing model)

## Context

J01 §7 Q1 asked: "Does the AI conduct the Assessment purely as a stream, or does one of the beats use a dedicated composed surface (e.g., a dependency/protection map visual) before returning to the stream?"

The question arose because Beat 2 (Responsibility & Protection) is information-dense — mapping dependents, insurance coverage, and fixed obligations. A composed surface (per `screen-taxonomy.md`) could present all of this as a structured form-like layout, potentially making data capture more efficient. Alternatively, keeping everything in the conversational stream maintains the paradigm established in Phase 1 (Discovery) and avoids a jarring modality switch.

## Options Evaluated

| Option | Pros | Cons | Risk |
|:---|:---|:---|:---|
| **A. Stream-only (all beats in conversational stream with inline components)** | Maintains conversational flow. No modality switch. Each question delivers immediate value-after. Progressive trust ramp works naturally — one question at a time. Inline components (option chips, confirm chips, range pickers) handle all structured input within the stream. | Beat 2 has 4–5 sequential questions — may feel slow for users who want to "just fill in the form." No overview of all dimensions at once (user can't see the full picture until the §C3 summary card). | Low — the "Building Your Picture" indicator provides the overview sense without a full composed surface. |
| **B. Hybrid: Stream for Beats 1 & 3, composed surface for Beat 2** | Beat 2's dependency mapping could be presented as a structured layout (dependency tree, insurance matrix). More efficient for data-dense capture. | Breaks conversational flow at the most trust-sensitive moment (Beat 2 is when the AI asks about income and dependents). Forces a visual paradigm switch that may feel like "now fill in this form." Loses the value-after cadence — the user sees a form, not a conversation. Re-entering the stream after the composed surface is awkward. | Medium — the modality switch undermines the trust ramp. Users may perceive the composed surface as a data extraction form rather than a caring conversation. |
| **C. Fully composed surface for entire Assessment** | Maximum data capture efficiency. All 8 dimensions visible at once. | Abandons conversational paradigm. Identical to incumbent onboarding forms. Eliminates trust ramp and value-after cadence. | High — contradicts core product thesis. |

## Decision

**Option A: Stream-only. The Assessment is conducted entirely within the conversational stream.**

The rationale:

1. **The conversational stream IS the product differentiation.** The moment the AI pulls up a form-like composed surface, CarrotFin feels like every other finance app. The conversation IS the assessment — that's the thesis.

2. **Trust is earned question-by-question.** The value-after cadence (ask → answer → show what that data point means) is the mechanism that earns permission to ask the next, more sensitive question. A composed surface collapses all trust-building into a single "fill this in and I'll tell you the answer" transaction.

3. **Inline components solve the structured input need.** Option chips (§C1), confirm chips (§C2), range pickers, multi-select chip sets, and sliders provide all the structured interaction needed — within the stream. The user isn't typing free-form numbers; they're interacting with structured elements that happen to live inside a conversation.

4. **The "Building Your Picture" indicator provides the overview.** The pinned progress indicator with dimension pills gives the user a bird's-eye sense of progress and completeness — the very thing a composed surface would provide — without breaking the stream.

5. **Beat 2's density is managed by grouping and pacing.** The 4–5 questions in Beat 2 are grouped logically (dependents → insurance → obligations) with value-after moments between groups. This is a well-paced conversation, not a slow form.

## Consequences

**Enables:**
- Unbroken conversational flow from Phase 1 through Phase 2 — no jarring modality switches
- Progressive trust ramp operates naturally through the assessment
- Value-after moments at the end of each beat maintain engagement and deliver the "Show Your Work" transparency that builds trust
- D9 (open dimension) works naturally — user volunteers context mid-conversation, and the AI incorporates it seamlessly. A composed surface would need an "Other" field.

**Constrains:**
- The inline component palette must be rich enough to handle all Assessment inputs without feeling like a poor substitute for a form. Phase 3 (Component Specs) must ensure confirm chips, range pickers, multi-select chip sets, and sliders are production-grade.
- Beat 2 must be carefully paced to avoid "question fatigue" — the value-after card at the end of Beat 2 is essential to sustaining engagement.
- Users who strongly prefer form-based interaction may find the conversational assessment slower. Monitor Assessment abandonment rate by beat — if Beat 2 has disproportionate drop-off, consider a "Quick mode" option that collapses Beat 2 into a single structured input surface (but this would be a V2 optimization, not a V1 design).

## Reversal Trigger

- Assessment completion rate < 50% (indicating the conversational format is losing users)
- Beat 2 shows > 30% higher abandonment than Beat 1 (indicating the density is the problem, not the format)
- User feedback consistently requests "just let me fill in a form" — would indicate the conversation is perceived as slow, not valuable
- A/B testing of stream-only vs. Beat 2 composed surface shows the composed surface has materially higher completion rates without reducing trust scores
