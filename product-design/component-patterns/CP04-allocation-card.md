# CP04: Allocation Card + Liquidity Gradient Strip

> **Type:** Card (phase-gate, single-state)  
> **Used on:** Hybrid (Phase 4 only)  
> **M3 base:** `Card` (elevated), `ListTile` (layer detail rows), `FilledButton` / `OutlinedButton` (CTAs)  
> **Date:** 2026-04-16  
> **Status:** Complete

---

> [!NOTE]
> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

---

## Purpose

Phase 4 phase-gate card. Visualises the recommended 3-layer liquidity allocation for the user's confirmed EF target. The anchor visual is a horizontal liquidity gradient strip (DD08) — a single continuous bar where zone widths are proportional to ₹ amounts, communicating liquidity as a spectrum, not three separate buckets.

Below the strip, three always-visible layer detail rows explain each speed tier. A conditional DICGC footnote renders outside the card when total target > ₹5L.

> **LLM agentic layer note:** The composition agent controls the verbal framing ("3 speeds of money"), layer weighting rationale, and depth of follow-up explanations. The visual layout and proportional zone rendering are DETERMINISTIC (computed from allocation amounts). The phase gate uses a lighter confirm — the user is accepting a recommendation, not verifying self-entered data.

---

## Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| Card container | `Card` (M3) — `surfaceContainer`, Large radius 16dp, elevation Level 3 | None |
| Card title "Your Fund Structure" | `Text` with Title Large (22sp/400) | None |
| Card icon 🏦 | `Text` (emoji) | None |
| Gradient strip container | `Row` with `Expanded` children inside `ClipRRect` (Large radius 16dp) | **Yes — custom proportional width layout. Each zone is a `Flexible` with `flex` computed from ₹ amount ratio** |
| Strip zone (each) | `Container` with solid fill (`warmPositive` / `neutralMid` / `coolNegative`) | Minimal — background colour per zone |
| Strip zone label (amount + months) | `Text` overlay within zone — Label Medium (12sp/500) | Minimal — positioned with `Padding` or `Center` |
| Divider below strip | `Divider` — `outlineVariant` | None |
| Layer detail row (each) | `ListTile` — leading: icon, title: label + amount + vehicle, subtitle: access time | Minimal — trailing expand affordance if "Tell me more" tapped |
| Layer detail row expand | `AnimatedCrossFade` or `ExpansionTile` content slot | Minimal — crossfade 250ms `easeInOut` |
| CTA "This works for me ✓" | `FilledButton` (M3) — `primary` | None |
| CTA "Tell me more" | `OutlinedButton` (M3) — `secondary` | None |
| DICGC footnote | `Text` with Body Small (12sp/400), `onSurface` at 60% opacity | None — rendered OUTSIDE card container |
| Card materialisation | `AnimatedSlide` + `AnimatedOpacity` | Minimal — spring stiffness 300, damping 0.8 |
| Row stagger | `AnimatedList` or manual stagger controller with 60ms delay | Minimal |
| Gradient strip materialisation | Coordinated with card — zones expand left-to-right with 80ms stagger per zone | **Yes — sequential zone reveal animation** |

---

## Structural Layout

> **Canonical layout.** J01-interaction-specs.md Phase 4 (L592–617) contains an earlier version of this layout. CP04 is now the authoritative component spec.

```
┌────────────────────────────────────────────────────────────┐
│  🏦  Your Fund Structure                                    │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Instant Access  │  Quick Access       │  Stable     │  │
│  │  ₹96K · 1mo      │  ₹2.9L · 3mo       │  ₹2.5L     │  │
│  └──────────────────────────────────────────────────────┘  │
│      (warm amber)        (neutral sand)     (cool slate)   │
│                                                            │
│  ──────────────────────────────────────────────────────    │
│                                                            │
│  ⚡ Instant Access  ·  ₹96K  ·  Savings / Sweep-in FD      │
│     Available instantly via ATM or UPI.                    │
│                                                            │
│  ⏱ Quick Access    ·  ₹2.9L  ·  Liquid Mutual Fund         │
│     Back the next business day. Up to ₹50K is near-       │
│     instant.                                               │
│                                                            │
│  🔒 Stable Reserve  ·  ₹2.5L  ·  Short-term FD / Debt Fund │
│     1–3 business days. Highest idle yield.                 │
│                                                            │
│  ┌─────────────────────┐  ┌─────────────────────┐         │
│  │  This works for me ✓ │  │  Tell me more        │        │
│  └─────────────────────┘  └─────────────────────┘         │
└────────────────────────────────────────────────────────────┘

(DICGC footnote — conditional, rendered below card if target > ₹5L)
  "If you use FDs for Layer 1 or 3, spread them across 2+ banks.
   Deposit insurance covers ₹5L per bank — simple to do, worth doing."
```

---

## Visual Design

| Property | Value | Reference |
|:---|:---|:---|
| Card shape | Large radius (16dp) | DS01 §3 |
| Card elevation | Level 3 (6dp) | DS01 §4 |
| Card fill | `surfaceContainer` | DS01 §1 |
| Card content padding | 16dp | DS01 §5 |
| Card title | Title Large (22sp/400), `onSurface` | DS01 §2 |
| Gradient strip height | 48dp (touch friendly) | — |
| Gradient strip shape | Large radius (16dp) with `ClipRRect` | DS01 §3 |
| Strip zone 1 (Instant Access) | Fill: `warmPositive` | DS01 §1 |
| Strip zone 2 (Quick Access) | Fill: `neutralMid` | DS01 §1 |
| Strip zone 3 (Stable Reserve) | Fill: `coolNegative` | DS01 §1 |
| Strip zone labels | Label Medium (12sp/500), `onWarmPositive` / `onNeutralMid` / `onCoolNegative` respectively | DS01 §1, §2 |
| Strip zone widths | Proportional to ₹ amounts: `flex = (layerAmount / totalTarget)` | DD08 locked constraint |
| Divider below strip | `outlineVariant`, full-width, 12dp vertical margin above and below | DS01 §1, §5 |
| Layer detail row gap | 8dp | DS01 §5 |
| Layer detail row — icon | ⚡ / ⏱ / 🔒 (emoji, not icon token) | — |
| Layer detail row — label + amount + vehicle | Body Medium (14sp/400), `onSurface` | DS01 §2 |
| Layer detail row — access time | Body Small (12sp/400), `onSurface` at 60% opacity | DS01 §2 |
| Layer detail rows elevation | Level 0 (cards-within-cards rule) | DS01 §4 |
| DICGC footnote | Body Small (12sp/400), `onSurface` at 60% opacity. Level 0. Positioned BELOW card with 8dp margin | DS01 §2, §4, §5 |

### Strip Zone Minimum Width Rule

When proportional sizing compresses any zone below **56dp** (below which the label becomes unreadable on mobile), apply a floor:

- Minimum zone width: 56dp
- Redistribute the overflow proportionally across the other two zones
- If all three zones are near-minimum (total target very small), switch to **equal-width mode** with ₹ amounts as the only differentiator

> **Reversal trigger (DD08):** If user testing shows >30% misinterpret the strip direction, fall back to vertical stacked blocks (top = most liquid). This CP04 spec would need a variant layout.

---

## Motion

| Animation | Config | Reference |
|:---|:---|:---|
| Card materialisation (slide-up + fade) | Spring: stiffness 300, damping 0.8 | DS01 §6 |
| Gradient strip zone reveal | Left-to-right sequential: 80ms stagger per zone. Each zone: width grows from 0 to proportional width with spring stiffness 300, damping 0.8 | DS01 §6 + custom |
| Layer detail row stagger | 60ms delay between rows, spring: stiffness 300, damping 0.8 | DS01 §6 |
| Row expand/collapse ("Tell me more") | Crossfade 250ms `easeInOut` | DS01 §6 |
| DICGC footnote fade-in | Opacity 0→1, 200ms `easeOut`. Appears 200ms after card materialisation completes | DS01 §6 |

### Materialisation Sequence

1. Card container slides up + fades in (spring)
2. Title renders immediately within card
3. Gradient strip zones expand left→right (80ms stagger: zone 1 → zone 2 → zone 3)
4. Divider renders
5. Layer detail rows stagger in (60ms per row)
6. CTAs render
7. DICGC footnote fades in (if triggered)

> Row stagger delays vary by emotional state — see §Adaptive Behaviour — Emotional State.

---

## Horizontal Liquidity Gradient Strip (DD08) — Detailed Spec

### Anatomy

A single continuous horizontal bar. Three colour-coded zones. No axes, no legend, no chart framing. The strip IS the visual — self-labelled with inline text. See §Structural Layout for the canonical wireframe.

### Zone Width Computation

```dart
final total = layer1Amount + layer2Amount + layer3Amount;
final flex1 = (layer1Amount / total * 100).round(); // e.g., 15
final flex2 = (layer2Amount / total * 100).round(); // e.g., 46
final flex3 = 100 - flex1 - flex2;                   // e.g., 39
```

Each zone is a `Flexible(flex: flexN)` child within a `Row`, wrapped in `ClipRRect` with Large radius (16dp). The `ClipRRect` applies to the outer `Row` container — individual zones do NOT have individual corner radii.

### Zone Label Visibility

| Zone width | Label rendering |
|:---|:---|
| ≥ 80dp | Full label: layer name + ₹ amount + months |
| 56dp–79dp | Compact: ₹ amount only |
| < 56dp | Minimum width floor triggered (see §Visual Design) |

### Colour Semantics

| Zone | Fill | On-fill | Semantic |
|:---|:---|:---|:---|
| Instant Access | `warmPositive` (warm amber) | `onWarmPositive` | Warm = accessible, immediate, safe |
| Quick Access | `neutralMid` (muted sand) | `onNeutralMid` | Neutral = middle ground, next-day |
| Stable Reserve | `coolNegative` (blue-slate) | `onCoolNegative` | Cool = locked, patient, highest yield |

> **Reusability:** The warm→cool gradient strip becomes an app-wide idiom for "liquidity spectrum." Future journeys (debt repayment, investment allocation) can reuse the strip widget with different zone counts, labels, and amounts.

---

## Layer Detail Rows — Detailed Spec

### Row Anatomy

```
[Icon]  [Layer Name]  ·  [₹ Amount]  ·  [Vehicle Type]
        [Access time description]
```

### Row Content by Layer

| Layer | Icon | Label | Vehicle (DD02 compliant) | Access Time |
|:---|:---|:---|:---|:---|
| 1 | ⚡ | Instant Access | Savings account / Sweep-in FD | Available instantly via ATM or UPI |
| 2 | ⏱ | Quick Access | Liquid mutual fund | Back the next business day. Up to ₹50K is near-instant |
| 3 | 🔒 | Stable Reserve | Short-term FD / Debt fund | 1–3 business days. Highest idle yield |

> **Advisory ceiling (DD02):** Instrument TYPE only. No specific fund names, AMC names, bank products, or platform names.

### Row Expand Behaviour ("Tell me more")

"Tell me more" CTA triggers the composition agent to continue the explanation in the conversational stream (not a modal or tooltip within the card). The agent decides which layer to elaborate on based on what the user taps or asks.

For **within-card** expansion (if user taps a layer detail row directly):

| Tap | Action | Motion |
|:---|:---|:---|
| First tap | Level 2: one-sentence explanation of why this layer exists and when to use it | Crossfade 250ms `easeInOut` |
| Second tap | Collapses | Crossfade 250ms `easeInOut` |

---

## DICGC Footnote — Detailed Spec

### Trigger

**Conditional:** Total EF target > ₹5L.

This is a factual disclosure, not a warning. No icon, no red, no badge.

### Content

> "If you use FDs for Layer 1 or 3, spread them across 2+ banks. Deposit insurance covers ₹5L per bank — simple to do, worth doing."

### Rendering

| Property | Value |
|:---|:---|
| Position | BELOW the Allocation Card in the conversational stream — NOT inside the card |
| Typography | Body Small (12sp/400) |
| Colour | `onSurface` at 60% opacity |
| Elevation | Level 0 |
| Margin | 8dp above (from card bottom), card content padding on horizontal |
| Motion | Fade-in 200ms `easeOut`, 200ms after card materialisation completes |

### Visibility by Literacy (CG01 §2.1)

| Literacy | DICGC rendering |
|:---|:---|
| 1–2 | **Suppressed** (unnecessary cognitive load at low literacy; the AI can mention it conversationally if relevant) |
| 3 | Rendered. Collapsed behind "Note about deposit insurance" tap |
| 4–5 | Rendered **inline** (not collapsed) |

### Emotional Override (CG01 §2.3)

Anxious state: DICGC footnote **suppressed** regardless of literacy.

---

## Phase Gate Confirm — Detailed Spec

Phase 4 uses a **lighter confirm** pattern than the §C3 summary gate (Phase 2) or "Looks right ✓" (Phase 3). The user is accepting a recommendation, not verifying data they entered.

| Property | Value |
|:---|:---|
| Primary CTA | "This works for me ✓" — `FilledButton`, `primary` |
| Secondary CTA | "Tell me more" — `OutlinedButton`, `secondary` |
| Gate behaviour | Flow WAITS for primary CTA before transitioning to Phase 5 |
| "Tell me more" | Does NOT gate — AI continues in conversational stream. Card remains. User can tap "This works for me ✓" at any time after |
| Voice acceptance | User can say equivalent ("sounds good", "works for me", etc.) — LLM maps to primary CTA |

---

## Adaptive Behaviour — Literacy

| Literacy | Gradient Strip | Layer Detail Rows | DICGC Footnote | Vocabulary |
|:---|:---|:---|:---|:---|
| 1–2 | Rendered (always — the visual is simpler than text). Labels: layer name + ₹ amount only (no months) | **Simplified** vehicle descriptions: "savings account" not "sweep-in FD." Access times in plain English | Suppressed | "Savings account," "a fund you can get back next day," "a fixed deposit" |
| 3 | Standard rendering. Full labels | Standard vehicle descriptions | Collapsed behind tap | Standard |
| 4–5 | Standard rendering. Full labels | Vehicle descriptions include T+1 notation, yield context. Level 2 auto-expanded | Inline (not collapsed) | "Liquid fund is T+1," "sweep-in FD," "ultra-short debt fund" |

---

## Adaptive Behaviour — Trust Level (CG01 §2.2)

| Trust Level | Rendering |
|:---|:---|
| New / Warming | All three layer detail rows **expanded** by default (first-time user needs to see the full logic). "Tell me more" CTA prominent |
| Trusting | Layer detail rows **collapsed** — headline labels sufficient. "Tell me more" available but not prominent |
| Dependent | Gradient strip + headline labels only. Detail rows collapsed. Minimal — trust the AI's recommendation |

---

## Adaptive Behaviour — Emotional State (CG01 §2.3)

| State | Rendering | DICGC | Row stagger |
|:---|:---|:---|:---|
| Calm | Standard | Standard (per literacy) | 60ms |
| Anxious | Standard card. Softer language in AI narration | **Suppressed** | 100ms |
| Overwhelmed | **Strip + headline labels only.** Detail rows hidden behind "Show details" affordance. Single CTA only ("This works for me ✓") | Suppressed | N/A |
| Excited | All detail rows expanded. Level 2 auto-open. Full vocabulary | Standard | 40ms |

---

## Adaptive Behaviour — Engagement Mode (CG01 §2.4)

| Mode | Rendering |
|:---|:---|
| Voice-heavy | CTA buttons full-width. AI narrates the "3 speeds" framing; screen shows the strip simultaneously (§0B Rule 3). Chip/row tap targets 48dp |
| Visual | All affordances prominent. Layer detail rows show expand chevrons. Standard tap targets (40dp) |
| Hybrid | Standard rendering per literacy level |

---

## Adaptive Behaviour — Income Pattern

Layer ₹ amounts are determined by the computation agent based on the user's income pattern (variable vs. salaried, sole vs. dual earner). See J01 Phase 4 LLM prompt (L556–564) for weighting logic. CP04 renders whatever amounts are provided — no layout changes per income pattern.

---

## Composition Rules

| Rule | Detail |
|:---|:---|
| Appears | Once in Phase 4. Does not re-render (no §C4 edit flow — this is a recommendation, not user-entered data) |
| Position | Inline in conversational stream. Materialises simultaneous with AI's verbal "3 speeds" framing (§0B Rule 3) |
| DICGC footnote position | Immediately BELOW Allocation Card in stream. NOT inside card |
| Density accounting | Counts as 1 card toward viewport limit (CG01 §3). DICGC footnote does NOT count — it's text, not a card |
| Phase gate behaviour | Flow WAITS for "This works for me ✓" before transitioning to Phase 5 |
| D3 buffer visibility | If a D3 social obligation buffer was confirmed in Phase 3, the AI mentions it conversationally after the Allocation Card — it does NOT appear inside the card. D3 is a separate sub-fund |

---

## Interaction Affordances

| Affordance | Action |
|:---|:---|
| "This works for me ✓" | Accepts allocation structure → proceeds to Phase 5 (Contribution Planning) |
| "Tell me more" | AI continues with deeper explanation in conversational stream. Card remains visible. Not flow-blocking |
| Layer detail row tap (Layer 1) | Level 2 expand: why instant access matters, when to use it (crossfade 250ms) |
| Layer detail row tap (Layer 2) | Level 2 expand: how liquid funds work, T+1 explanation (crossfade 250ms) |
| Layer detail row tap (Layer 3) | Level 2 expand: role of stable reserve, yield context (crossfade 250ms) |
| Layer detail row second tap | Collapses (crossfade 250ms) |
| Strip zone tap | Maps to the corresponding layer detail row expand (same as row tap) |
| DICGC footnote tap (literacy 3) | Expands from "Note about deposit insurance" to full text |
| "Show details" (overwhelmed state) | Expands hidden layer detail rows |

---

## Re-entry Scenario Notes

> **Agent context — not additional component states.** The following notes inform the composition agent's rendering decisions for returning users. The Allocation Card uses its existing layout and states with contextually appropriate data and verbal framing.

| Scenario | Agent behaviour |
|:---|:---|
| **B (existing savings)** | Agent accounts for existing allocation in its verbal framing — e.g., "You already have savings. Here's how to structure the rest." Card layout is unchanged — the ₹ amounts in the gradient strip and layer detail rows reflect the new/remaining allocation, not the full target. The strip proportions are computed from the remaining amounts |
| **E (existing EF, review)** | Same principle as Scenario B. If the user's existing corpus already covers some layers, the agent frames the card as a rebalancing recommendation — "Your Instant Access layer is already covered. Here's how to distribute the rest." Amounts reflect the gap, not the total |

---

## D3 Confirmed Card — Scope Note

D3 Confirmed Card is specced in **CP03 State 4** (Target Card + Attribution Strip). It is a Phase 3 artifact — it replaces the D3 Prompt Card within the Phase 3 surface after the user confirms their social obligation buffer.

The Allocation Card does NOT display D3 buffer amounts. If a D3 buffer was confirmed, the AI mentions it conversationally after the Allocation Card as a separate sub-fund reference.

---
