# CP01: Confirmation Patterns

> **Date:** 2026-04-16  
> **Type:** Confirmation · Flow Control  
> **Used on:** Generative / Hybrid screens  
> **M3 base:** `InputChip` (§C2), `Card` + `ListTile` (§C3), inline widget swap (§C4)  
> **Status:** Complete — referenced by CG01 Phases 2, 3, 5

---

> [!NOTE]
> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

---

## Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| §C2 Confirm Chip | `InputChip` (M3) — supports selected state, trailing icon | Minimal — add ✓/✏️ trailing actions; `warmPositive` tint on confirmation |
| §C3 Summary Card container | `Card` (M3) with `Padding` + `Column` | None |
| §C3 data rows | `ListTile` (M3) — leading label, trailing value, onTap | None — configure `onTap` to trigger §C4 |
| §C3 CTA row | `FilledButton` + `OutlinedButton` (M3) | None |
| §C4 categorical edit | `FilterChip` / `ChoiceChip` (M3) inline set | None |
| §C4 numeric edit | `TextField` with `InputDecoration` (M3), `₹` prefix | None |
| §C4 multi-select edit | `FilterChip` multi-select set (M3) | None |
| §C4 row highlight | `ColoredBox` with `primaryContainer` at 10% opacity | Minimal |

---

## User Context Inputs (all three components)

| Dimension | §C2 Effect | §C3 Effect | §C4 Effect |
|:---|:---|:---|:---|
| Financial literacy | Chip size, label complexity | Row count/visibility, label language | Input field size, helper text |
| Trust level | — | Label density, expansion state | — |
| Emotional state | — | Collapse behaviour, CTA count | Reassurance copy on save |
| Engagement mode | Voice vs. tap confirm | — | Voice vs. tap re-entry |

---

## §C2 — Confirm Chip (Active Confirm)

### Purpose

Flow-blocking inline chip to confirm a numeric value the user provided. AI pauses until confirmation received.

### Where Used

| Phase | Trigger |
|:---|:---|
| Phase 2 | Income confirmation (D1 amount), fixed obligations (D4) |
| Phase 5 | Contribution amount confirmation (variant — single value, no summary card) |

### Visual Design

| Property | Value |
|:---|:---|
| Placement | Inline, below AI message that echoed the number |
| Content | Confirmed value (₹ formatted per DS01 §2: `₹1,20,000`), ✓ button, ✏️ button |
| Background | `secondaryContainer` (DS01 §1) |
| Shape | Small (8dp) — DS01 §3 |
| Elevation | Level 0 (inline in stream) |

### Interaction

| Action | Behaviour |
|:---|:---|
| ✓ tap | Value locked. Chip → confirmed state: subtle `warmPositive` tint, checkmark animation (spring: pill fill, stiffness 400, damping 0.75) |
| ✏️ tap | Value field becomes editable inline. Numeric keyboard opens. On re-submit → new ✓/✏️ cycle |
| Voice "yes" / "that's right" | Equivalent to ✓ tap |
| Voice corrected number | New value replaces chip, triggers new ✓/✏️ cycle |
| Ignore >5s | AI prompts: "Is ₹[X] right?" |

### Flow Control

- **§C2 is a flow-holding state.** Until the user confirms (✓ tap or voice), the AI composition agent treats the numeric value as unconfirmed and does not proceed to the next conversational turn.
- This is a state constraint, not an LLM script. The agent may still respond to direct questions while the chip is pending.

### Adaptive Behaviour

| Condition | Rendering |
|:---|:---|
| Literacy 1–2 | Chip touch target: 48dp. ₹ amount in Display Medium weight. Labels simplified |
| Literacy 3+ | Chip touch target: 40dp. Standard rendering |
| Engagement: voice-heavy | Voice confirmation prominent: "Say 'yes' if this looks right" hint below chip |
| Engagement: tap-heavy | Standard chip, no voice hint |

---

## §C3 — Summary Card (Phase Gate)

### Purpose

Present all captured data for review before AI computes downstream results. Flow-blocking gate at major phase boundaries. User must explicitly approve before the journey advances.

### Where Used

| Phase | Trigger |
|:---|:---|
| Phase 2 → 3 gate | All 8 assessment dimensions captured |
| Phase 3 | Re-rendered on §C4 re-edit from Target Card "Adjust an answer" CTA |



### Visual Design

| Property | Value |
|:---|:---|
| Shape | Large (16dp radius) — DS01 §3 |
| Elevation | Level 3 (6dp) — DS01 §4 |
| Title | "✏️ Your Profile Summary" — Title Large (22sp/400, DS01 §2) |
| Row label | Body Medium (14sp/400) |
| Row value | Body Medium (14sp/400), right-aligned |
| Health row (D8) | Always shows "Factored in" — never displays conditions |
| D9 row | Present only if triggered during assessment |
| Separator | `outlineVariant` line before CTAs |
| CTAs | "Looks right" (primary, filled) + "Let me adjust" (secondary, outlined) |
| Content padding | 16dp (DS01 §5) |

### Row Order (Phase 2 Gate)

Display order (not capture order): see §C3 card layout in J01-interaction-specs.md §C3.

### Interaction

| Action | Behaviour |
|:---|:---|
| Tap any data row | Triggers §C4 Edit-in-Place for that dimension |
| "Looks right" tap | Flow advances to next phase |
| "Let me adjust" tap | Scrolls to first row, all rows highlighted with subtle pulse animation (spring: pill fill, stiffness 400, damping 0.75) |
| Flow control | Card is **flow-blocking** — AI waits for explicit CTA tap |

### Adaptive Behaviour

| Condition | Rendering |
|:---|:---|
| Literacy 1–2 | Simplified labels ("Job type" not "Employment type"). Max 6 rows visible, scroll for rest. Values in plain language |
| Literacy 4–5 | Full labels, all rows visible, precise numbers |
| Trust: New / Warming | All rows expanded with explicit labels |
| Trust: Trusting+ | Condensed labels (e.g., "₹1.2L/mo, Salaried, Bangalore") |
| Emotional: Overwhelmed | Headline summary + single "Looks right" CTA. Detail rows collapsed behind "Review details" tap |
| Emotional: Anxious | Standard rows but softer label language. Both CTAs visible |

---

## §C4 — Edit-in-Place

### Purpose

Allow the user to edit a previously confirmed value without leaving current context. Edits trigger downstream recalculation. Inline — never a modal or new card.

### Where Used

| Phase | Trigger | Recalculation Effect |
|:---|:---|:---|
| Phase 2 | Tap any §C3 Summary Card row | Updated value reflected in §C3 |
| Phase 3 | Target Card "Adjust an answer" CTA | Re-renders §C3 → user edits → Target Card ₹ morphs, attribution strip re-renders (staggered, 60ms) |
| Phase 5 | Tap contribution amount on Contribution Plan Card | All 4 milestone dates recalculate live (number morph on each date) |

### Visual Design

| Property | Value |
|:---|:---|
| Placement | Replaces row content inline — NOT a modal, NOT a new card |
| Input method | Matches original: categorical → chip set; numeric → editable ₹ field; multi-select → toggleable chips |
| Edit row highlight | `primaryContainer` at 0.1 opacity |
| Save | ✓ button or tap outside row |
| Cancel | ✏️ dismiss or swipe away |
| Content crossfade | `Curves.easeInOut`, 250ms (DS01 §6) |

### Interaction

| Action | Behaviour |
|:---|:---|
| Save value | Value updates in §C3 immediately |
| Downstream recompute | Computation agent recalculates. Affected values animate: number morph (spring: stiffness 250, damping 0.85, DS01 §6) |
| Phase 3 re-edit | §C3 re-renders. After edit + save → Target Card ₹ morphs, attribution strip re-renders with staggered animation (60ms delay between rows) |
| Phase 5 contribution edit | Editing contribution amount recalculates all 4 milestone dates live. Number morph animation on each date (spring: stiffness 250, damping 0.85) |

### Adaptive Behaviour

| Condition | Rendering |
|:---|:---|
| Literacy 1–2 | Larger edit field. Input helper text visible ("Type your monthly income in ₹") |
| Engagement: voice | User can state new value verbally → §C2 confirm cycle for the new value |
| Emotional: Anxious | Edit confirmation includes reassurance: "Updated — I'll recalculate with your new number" |
