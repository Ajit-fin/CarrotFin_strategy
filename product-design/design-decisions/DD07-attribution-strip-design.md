# DD07: Attribution Strip — Tiered Linear Arrow Diagram

> **Date:** 2026-04-16  
> **Tension resolved:** T7 (Verification Depth vs. Cognitive Load) — partially resolved for Phase 3 context  
> **Assumptions depended on:** EF-11 (8 core dimensions), C6 (users trust AI enough to act)

## Context

Phase 3 (Target Setting) must show **why** the EF target is what it is — not just the number. The attribution visualization is the "Show Your Work" mechanism (behavioral framework §5) that earns trust for a number the user will anchor their entire financial safety net to.

§7 Q3 asks: is the attribution a text list (simple), an arrow diagram (moderate), or a force graph / weighted bubble chart (rich)? T7 governs: verification depth builds trust, but cognitive load erodes it.

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| **A. Text list** ("Salaried base: 5 mo. +1 for infant. +1 for uninsured parent.") | Simplest. Fast to scan. No visual parsing needed. Accessible at all literacy levels. | Loses the flow — doesn't convey that factors *compound* or that the number *evolved* from a baseline. Feels like a receipt, not an explanation. | Low trust-building for users who can handle more depth. |
| **B. Linear arrow diagram** (baseline → factor → factor → final) | Shows the journey: how the baseline was adjusted step by step. Each arrow is a +/- modification. The number *builds* visually. Natural left-to-right reading order. | Requires some visual literacy. Long chains (6+ factors) may overflow horizontally on mobile. | Arrow layout must degrade gracefully on narrow screens. |
| **C. Force graph / weighted bubble chart** | Rich, engaging, "wow factor." Shows relative weight of each factor. | High cognitive load. Requires interaction to parse. Unfamiliar to low-literacy users. Over-engineered for 5–8 data points — force graphs shine with 20+. | Users may focus on the visual instead of understanding the factors. Bubble sizes introduce false precision. |

## Decision

**Tiered Linear Arrow Diagram** — with a literacy-signal fallback to plain-text list.

### Default: Linear Arrow Diagram (Vertical Stack)

The attribution strip renders as a **vertical stacked arrow diagram** (not horizontal — mobile-first). Each row shows one dimension's contribution. See Target Card layout in [Phase 3 interaction spec](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-interaction-specs.md) (Step 3.2) for the full visual.

**Design rules:**
- Each row is labeled with the human-readable factor (not dimension number)
- Positive adjustments are warm-tinted (amber/orange text)
- Negative adjustments are cool-tinted (blue-green text)
- The baseline row has no +/− — it anchors
- A separator line (═) precedes the final total
- The entire strip is collapsible — default state is **expanded** for first-time users at New/Curious trust level; collapsed (headline only) for returning users at Warming+ trust level
- Each factor row is tappable → expands a one-sentence plain-English explanation (§5.2 Level 2: "Reasoning")
- Second-level tap reveals the data source (§5.2 Level 3: "Formula")

### Fallback: Plain-Text List

Triggered when the **literacy signal is low** (behavioral framework §3.3: simple language, one insight at a time):

Detection heuristic: user answered all assessment questions via tap (never typed), spent >median time on each step, and/or the AI's vocabulary calibration from Phase 1 indicates low financial literacy.

In this mode, the attribution strip is replaced with the AI narrating the breakdown conversationally:

> "Your number is 7 months — about ₹4.9L. Here's why: most people in a salaried job at a big company start at 5 months. Having a baby adds about a month. Your mother-in-law isn't covered by health insurance, so I added another month for that. Your household has two incomes, which brings it down a bit. All together: 7 months."

No visual strip. The explanation lives entirely in the conversational stream. User can still ask "Show me the breakdown" to trigger the visual strip.

### Why not the others

- **Text list** is the fallback, not the default. As the primary display for all users, it under-delivers on the "Show Your Work" promise — it lacks the visual *flow* that makes the number feel inevitable rather than arbitrary.
- **Force graph** solves a problem we don't have. 5–8 dimensions don't need spatial encoding. The cognitive overhead of interpreting bubble sizes and positions adds load without proportional trust gain. It's impressive-looking but fails T7's core test: does the user *understand* why the number is what it is?

## Consequences

- **Phase 3 interaction spec**: Attribution strip is inline within the Target Card as a collapsible section. Default expanded. Vertical stack layout.
- **Component spec (Phase 3)**: Requires an `AttributionStrip` component with tiered expand behavior. Must support variable row counts (5–9 depending on D9).
- **Literacy signal routing**: The fallback requires the AI to track a literacy score during assessment. This score informs multiple Phase 3 decisions — not just attribution. BuildSpec must define the signal computation heuristic.
- **T7 is partially resolved**: For Phase 3 (Target Setting attribution), the linear arrow diagram + fallback resolves the verification depth vs. cognitive load tension. T7 remains open for other contexts (e.g., portfolio analysis, contribution plan projections) where the right depth may differ.

## Reversal Trigger

- User testing shows >30% of users collapse the attribution strip immediately without reading (signal: visual overload)
- Alternatively: users at low literacy level show confusion or increased bounce when the visual strip is shown (signal: fallback heuristic is too permissive)
- If bubble/force graph significantly outperforms on trust scores in A/B testing — revisit
