# CP02: BYP Progress Indicator

> **Type:** Indicator (pinned, read-only)  
> **Used on:** Hybrid (Phase 2 only)  
> **M3 base:** `FilterChip` (dimension pills), `SizedBox`/`Container` pinned via `Scaffold` top slot  
> **Date:** 2026-04-16  
> **Status:** Complete

---

> [!NOTE]
> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

---

## Purpose

Pinned horizontal strip between top nav bar and conversational stream. Tracks the 8 assessment dimensions (+ optional D9) as the AI captures them. Shows a running estimated target range that narrows in real time. Primary orientation mechanism during Phase 2.

NOT a progress bar. NOT a form stepper. A live data portrait.

---

## Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| Dimension pills (empty ○) | `FilterChip` (M3) — unselected state, `StadiumBorder` by default | Minimal — set `side` to `outline` token, label opacity 60% |
| Dimension pills (filled ✓) | `FilterChip` (M3) — selected state | Minimal — `selectedColor: primaryContainer`, add leading checkmark icon |
| D9 pill (materialises) | `FilterChip` (M3) — same as filled pill | Minimal — slide-in animation wrapper (`AnimatedSlide` + `AnimatedOpacity`) |
| Strip container | `Container` with `surfaceContainerHigh` fill, pinned via `Scaffold` `appBar` bottom or `SliverPersistentHeader` | None |
| Running estimate text | `RichText` (M3 typography tokens) — two `TextSpan`s (prefix + value) | None |
| Number morph animation | Custom `AnimatedSwitcher` / `TweenAnimationBuilder` on number string | **Yes — no native M3 number morph; use `TweenAnimationBuilder<double>` with spring curve** |
| Directional colour pulse | `AnimatedDefaultTextStyle` or `TweenAnimationBuilder<Color>` | Minimal — no custom widget needed |
| Overwhelmed collapse | `AnimatedSwitcher` (pills ↔ single-line text) + `LinearProgressIndicator` (indeterminate) | Minimal |

---

## Structural Layout

See layout reference in J01-interaction-specs.md §BYP ASCII diagram.

---

## Visual Design

| Property | Value | Reference |
|:---|:---|:---|
| Position | Fixed between top nav and scroll area | — |
| Background | `surfaceContainerHigh` | DS01 §1 |
| Elevation | Level 6 | DS01 §4 |
| Internal spacing | 12dp horizontal, 8dp vertical | DS01 §5 |
| Pill count | 8 (D1–D8), always rendered. 9th (D9) if triggered | — |
| Pill labels | Income · Dependents · Protection · Obligations · City · Age · Health · Household | — |
| Pill shape | `StadiumBorder` (pill-shaped) | DS01 §3 |
| Pill empty (○) | `outline` token border, label `onSurface` at 60% opacity | DS01 §1 |
| Pill filled (✓) | Background `primaryContainer`, label `onPrimaryContainer`, checkmark icon | DS01 §1 |
| Pill label typography | Label Medium (12sp/500) | DS01 §2 |
| Running estimate prefix | Label Small (11sp/500) — "Your range:" | DS01 §2 |
| Running estimate value | Body Medium (14sp/400) — number portion | DS01 §2 |
| Estimate — no data | "Your range: — months" | — |
| Estimate — partial | "Your range: X–Y months" | — |
| Estimate — complete | "Your target: N months (₹X.XL)" | — |

---

## State Machine

> **Agent autonomy:** The states below are available to the composition agent based on data events from the LLM. The agent updates pill states as dimensions are captured — no fixed order or timing is imposed. DETERMINISTIC states are marked.

| State | Trigger | Visual Change | Motion |
|:---|:---|:---|:---|
| Start | Phase 2 entry | 8 empty pills (○). Estimate: "— months" | — |
| Dimension captured | LLM captures any D1–D8 | Pill ○ → ✓ | Spring: stiffness 400, damping 0.75 (DS01 §6) |
| First value-after | Income cluster (D1+D7) captured | Estimate count-up from — to first range | Spring: stiffness 250, damping 0.85 (DS01 §6) |
| Subsequent value-afters | Each dimension cluster | Range narrows/shifts. Directional colour pulse on estimate (see §Directional Colour) | Number morph: stiffness 250, damping 0.85 |
| All captured | All 8 pills filled | Estimate → "Your target: N months (₹X.XL)". All pills pulse `warmPositive` once | Completion micro-animation |
| D9 triggered | User volunteers D9 context | 9th pill materialises, fills ✓ immediately. Estimate updates | See §D9 Pill Behaviour |

---

## Directional Colour on Range Changes

| Condition | Colour | Duration | Curve | Scope |
|:---|:---|:---|:---|:---|
| Range increases (more months) | `warmPositive` pulse | 300ms | `Curves.easeInOut` | Running estimate text only |
| Range decreases (fewer months) | `coolNegative` pulse | 300ms | `Curves.easeInOut` | Running estimate text only |
| After pulse | Returns to `onSurface` | — | — | — |

Do NOT apply directional colour to pills.

---

## D9 Pill Behaviour

| Property | Value |
|:---|:---|
| Pre-rendered | No — materialises on trigger |
| Entry animation | Slides in from right edge. Spring: stiffness 300, damping 0.8 (DS01 §6 card materialisation) |
| Label | Contextual — LLM assigns (e.g., "Legal buffer", "Family support") |
| Fill state | Fills ✓ immediately on materialisation (capture + confirm simultaneous) |
| Estimate update | Number morph animation (stiffness 250, damping 0.85) |

---

## Composition Rules

| Rule | Detail |
|:---|:---|
| Visibility | Always visible during Phase 2 — from entry to §C3 gate confirmation |
| Dismissal | Fades out on §C3 confirmation (opacity 0, 200ms `Curves.easeOut` — DS01 §6) |
| User dismissal | Not allowed — structural, not actionable |
| Density accounting | Excluded from viewport card limit (CG01 §3 — pinned components) |

---

## Adaptive Behaviour

| Condition | Rendering |
|:---|:---|
| Literacy 1–2 | Simplified pill labels: "Job" (not "Income"), "Cover" (not "Protection"). Estimate: months only ("about 8 months"), no ₹ amount until all pills filled |
| Literacy 3 | Standard pill labels. Estimate: range format ("6–8 months"), collapses to months + ₹ when complete |
| Literacy 4–5 | Standard pill labels. Estimate: shows ₹ amount throughout (not just at completion) |
| Emotional: Overwhelmed | Strip collapsed to single line: "Building your picture…" with indeterminate progress indicator. Pills hidden. Expands when emotional state normalises |
| Emotional: Anxious | Standard strip rendered. Directional colour pulses suppressed (no warm/cool flashes) |
| Engagement mode | No effect — strip renders normally and is read-only in all modes |

---

## Re-entry Scenario Notes

> **Agent context — not additional component states.** The following notes inform the composition agent's rendering decisions for returning users. The BYP uses its existing states (§State Machine above) with contextually appropriate initial conditions.

| Scenario | Agent behaviour |
|:---|:---|
| **B (existing savings)** | Agent pre-fills dimension pills for any data points already known from the user's prior session or stated corpus. BYP starts partially filled (e.g., D1, D5, D6 already ✓). Running estimate reflects the pre-filled data. The agent decides which pills are pre-filled based on what data is available and confirmed — not a fixed set |
| **E (existing EF, review)** | Same as Scenario B — pre-fill known dimensions. If all 8 are known from a prior completed assessment, BYP renders in the "All captured" state immediately and the §C3 gate fires for re-confirmation. The agent may flag stale data for re-verification per §0B re-entry model |

---

## User Context Inputs

| Dimension | Effect |
|:---|:---|
| Financial literacy | Pill label complexity, ₹ amount display timing, range vs. headline format |
| Emotional state | Strip collapse (Overwhelmed), directional pulse suppression (Anxious) |
| Engagement mode | No effect — strip is always visible and read-only |
| Trust level | No effect on strip rendering |

---

## Interaction Affordances

**Read-only.** No tap, swipe, or dismiss.

Tapping a pill does NOT navigate to that dimension — navigation happens from §C3 rows. The strip is informational infrastructure, not interactive navigation.
