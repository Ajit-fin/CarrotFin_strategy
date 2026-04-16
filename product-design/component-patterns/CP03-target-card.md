# CP03: Target Card + Attribution Strip

> **Type:** Card (phase-gate, multi-state)  
> **Used on:** Hybrid (Phase 3 only)  
> **M3 base:** `Card` (elevated), `ListTile` (attribution rows), `ElevatedButton` / `OutlinedButton` (CTAs)  
> **Date:** 2026-04-16  
> **Status:** Complete

---

> [!NOTE]
> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

---

## Purpose

Phase 3 phase-gate card. Reveals the user's personalised emergency fund target with full dimension attribution. Four tightly coupled states form one Phase 3 surface: Target Card, Attribution Strip (DD07), D3 Prompt Card, Starter Shield Overlay.

> **LLM agentic layer note:** The composition agent selects which *state* to render based on runtime user context. Only Starter Shield is DETERMINISTIC. All other states are agent-decided. The agent also decides D3 timing, editorial "key insight" content, and which scenario variant (B/C/E) to render. Sequence is not prescribed.

---

## Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| Card container | `Card` (M3) — `surfaceContainer`, Large radius 16dp, elevation Level 3 | None |
| Headline ₹ amount | `Text` with Display Medium (45sp/400) | None |
| Headline months label | `Text` with Title Large (22sp/400) | None |
| Attribution sub-header | `Text` "Why this number" + `Divider` | Minimal — styled divider |
| Attribution row | `ListTile` — leading: factor label, title: description, trailing: +/− value | Minimal — tap expansion via `ExpansionTile` or custom wrapper |
| Row expand (Level 2/3) | `AnimatedCrossFade` or `ExpansionTile` content slot | Minimal — crossfade 250ms `easeInOut` |
| "Show all N factors" | `TextButton` (M3) | None |
| "Why is this different?" | `TextButton` with expand/collapse toggle | None |
| CTA "Looks right ✓" | `FilledButton` (M3) — `primary` | None |
| CTA "Adjust an answer" | `OutlinedButton` (M3) — `secondary` | None |
| D3 Prompt Card container | `Container` with `BoxDecoration` — dashed border | **Yes — dashed border not native in M3. `BoxDecoration` + `dashPattern [6, 3]` per DS01 §3.** |
| D3 icon | `Text` (emoji 🎁) | None |
| D3 CTA "Add a buffer" | `FilledButton` | None |
| D3 CTA "Skip — not now" | `TextButton` (lower visual weight) | None |
| Starter Shield milestone rows | `ListTile` — leading: ★ / plain marker, title: label, trailing: ₹ amount + months | Minimal — opacity 40% on inactive rows |
| ★ warm glow | `Container` with `warmPositive` background at ~15% opacity | Minimal |
| Number morph animation | `TweenAnimationBuilder<double>` with spring curve | **Yes — same as CP02** |
| Card materialisation | `AnimatedSlide` + `AnimatedOpacity` | Minimal — spring stiffness 300, damping 0.8 |
| Row stagger | `AnimatedList` or manual stagger controller with 60ms delay | Minimal |

---

## Structural Layouts

### State 1: Target Card (default)

```
┌──────────────────────────────────────────────────────────┐
│  🛡️  Your Emergency Fund Target                           │
│        ₹4.2L  ·  8 months of essential expenses          │
│                                                          │
│  ── Why this number ──────────────────────────────────── │
│  Employment     Salaried, large private    +1.5 months   │
│  Dependents     Infant + uninsured parent  +1.0 months   │
│  Health cover   ₹5L–10L                   +0.5 months    │
│  Fixed costs    50% of income             +1.0 months    │
│  City           Tier 1                    +0.5 months    │
│  Age            30s                        0 months      │
│  Household      Dual income               −1.0 months    │
│  Health profile Factored in               +0.5 months    │
│  ─────────────────────────────────────────────────────── │
│  D9 (if triggered): [Label]               +X.X months    │
│                                                          │
│  ┌──────────────────┐  ┌─────────────────────┐          │
│  │  Looks right  ✓  │  │  Adjust an answer   │          │
│  └──────────────────┘  └─────────────────────┘          │
└──────────────────────────────────────────────────────────┘
```

### State 2: D3 Prompt Card (separate card below Target Card)

```
┌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┐
╎  🎁  Family Obligation Buffer (Optional)        ╎
╎                                                  ╎
╎  A small separate fund for weddings,             ╎
╎  ceremonies, and family support — so your        ╎
╎  emergency fund stays untouched.                 ╎
╎                                                  ╎
╎  ┌──────────────┐  ┌───────────────────┐        ╎
╎  │  Add a buffer │  │  Skip — not now    │       ╎
╎  └──────────────┘  └───────────────────┘        ╎
└╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┘
```
Dashed border = `conditionalBorder`. Fill = `conditionalSurface`. Shape = Large radius (16dp).

### State 3: Starter Shield Overlay (replaces attribution strip within Target Card)

```
┌──────────────────────────────────────────────────┐
│  🛡️  Your Emergency Fund Target                   │
│        ₹11.5L  ·  12 months                      │
│                                                   │
│  ── Your milestones ──────────────────────────── │
│  ★ Starter Shield     ₹96K   (1 month)  ←──     │
│    3 Months Secure    ₹2.9L  (3 months)          │
│    Half-Year Shield   ₹5.8L  (6 months)          │
│    Fully Funded       ₹11.5L (12 months)         │
│                                                   │
│  [Got it — let's plan →]                          │
└──────────────────────────────────────────────────┘
```

### State 4: D3 Confirmed Card (replaces D3 Prompt Card in-place)

```
┌──────────────────────────────────────────────────┐
│  🎁  Family Obligation Buffer                     │
│                                                   │
│  ₹60,000 / year                                   │
│  Based on: ₹30K–50K events × 1–2 per year         │
│                                                   │
│  Separate from your ₹4.2L safety net.             │
│                                                   │
│  ┌──────────────┐  ┌─────────────────┐           │
│  │  Looks right ✓│  │  Remove buffer  │           │
│  └──────────────┘  └─────────────────┘           │
└──────────────────────────────────────────────────┘
```

Standard fill = `surfaceContainer`. Medium radius (12dp). Elevation Level 1. Confirmed treatment (no dashed border — resolution signalled by solid fill).

---

## Visual Design

| Property | Value | Reference |
|:---|:---|:---|
| Card shape | Large radius (16dp) | DS01 §3 |
| Card elevation | Level 3 (6dp) | DS01 §4 |
| Card fill | `surfaceContainer` | DS01 §1 |
| Card content padding | 16dp | DS01 §5 |
| Headline ₹ | Display Medium (45sp/400) | DS01 §2 |
| Headline months label | Title Large (22sp/400), `onSurface` | DS01 §2 |
| "Why this number" sub-header | Title Medium (16sp/500), `onSurface` | DS01 §2 |
| Sub-header divider | `outlineVariant`, full-width | DS01 §1 |
| Attribution row gap | 8dp | DS01 §5 |
| Attribution row — factor label | Body Medium (14sp/400), `onSurface` | DS01 §2 |
| Attribution row — description | Body Small (12sp/400), `onSurface` at 60% opacity | DS01 §2 |
| Attribution row — positive value | Body Medium (14sp/500), `warmPositive` | DS01 §1 |
| Attribution row — negative value | Body Medium (14sp/500), `coolNegative` | DS01 §1 |
| Attribution row — zero value | Body Medium (14sp/500), `onSurface` at 60% opacity | DS01 §1 |
| Attribution rows elevation | Level 0 (cards-within-cards rule) | DS01 §4 |
| D3 Prompt Card fill | `conditionalSurface` | DS01 §1 |
| D3 Prompt Card border | Dashed `conditionalBorder`, `dashPattern [6, 3]` | DS01 §3 |
| D3 Prompt Card shape | Large radius (16dp) | DS01 §3 |
| Starter Shield ★ row | `warmPositive` background at ~15% opacity (warm glow) | DS01 §1 |
| Starter Shield inactive rows | `onSurface` at 40% opacity | DS01 §1 |

---

## Motion

| Animation | Config | Reference |
|:---|:---|:---|
| Card materialisation (slide-up + fade) | Spring: stiffness 300, damping 0.8 | DS01 §6 |
| Headline renders | Instant (no delay) | — |
| Attribution row stagger | 60ms delay between rows, spring: stiffness 300, damping 0.8 | DS01 §6 |
| Row expand/collapse (Level 2/3) | Crossfade 250ms `easeInOut` | DS01 §6 |
| Number morph (recalculation) | Spring: stiffness 250, damping 0.85 | DS01 §6 |
| Attribution row re-stagger (after §C4 re-edit) | Same as initial stagger | DS01 §6 |
| D3 Prompt Card slide-up | Spring: stiffness 300, damping 0.8 | DS01 §6 |
| D3 skip fade-out | Opacity 0, 200ms `easeOut` | DS01 §6 |
| D3 Confirmed Card crossfade | 250ms `easeInOut` | DS01 §6 |
| Starter Shield overlay (attribution collapse) | Crossfade 250ms `easeInOut` | DS01 §6 |

> Row stagger delays vary by emotional state — see §Adaptive Behaviour — Emotional State.

---

## State Machine

> **Agent autonomy:** States below are *available options* the composition agent may select based on runtime user context. Only Starter Shield is DETERMINISTIC (hard business rule). All other state transitions are agent-decided. The composition agent determines scenario variant, editorial framing, and D3 timing.

| State | Trigger | Attribution Strip | Headline | CTAs |
|:---|:---|:---|:---|:---|
| **Default** | Standard Phase 3 entry | Visible, staggered, collapsible | ₹ amount + months | "Looks right ✓" / "Adjust an answer" |
| **Recalculated** | Agent selects after §C4 re-edit | Re-renders. Number morph (stiffness 250, damping 0.85). Rows re-stagger | ₹ amount updates with number morph | Same |
| **Scenario B** (existing savings) | Agent detects existing corpus | Attribution strip + gap analysis BELOW strip: "You have ₹X.XL — you're N% there" | Same ₹ target | Same |
| **Scenario C** (validation path) | Agent detects user had own target | Attribution strip + comparison row below strip: user target vs. CarrotFin target | Modified: "Go with ₹X.XL" / "Stick with my ₹X.XL" | Modified CTAs |
| **Scenario E** (existing EF review) | Agent detects existing EF corpus | Attribution strip + gap/surplus framing below | Same + gap/surplus annotation | Same or "I'm fully funded" |
| **Starter Shield** [DETERMINISTIC] | target ≥ ₹5L AND (age ≤ early 30s OR income ≤ ₹80K OR target/income > 6×) | REPLACED by milestone list (4 rows). Attribution strip collapses | Same ₹ target + months | Single CTA: "Got it — let's plan →" |

> **D3 Confirmed Card (State 4):** Separate card — see §D3 Confirmed Card — Detailed Spec below. Not a Target Card state — it replaces the D3 Prompt Card (State 2) after user confirms the social obligation buffer.

### Starter Shield Trigger (DETERMINISTIC)

Fires when ALL of:
- Target ≥ ₹5L

AND at least one of:
- Age bracket: 20s or early 30s
- Monthly income ≤ ₹80K
- Target ÷ monthly income > 6×

On trigger: attribution strip collapses (crossfade 250ms), milestone overlay renders. Only ★ Starter Shield row is active — others at 40% opacity. Warm glow on ★ row (`warmPositive` background ~15% opacity).

> **LLM agentic layer note:** The Starter Shield trigger is evaluated by the computation agent, not the LLM. When the trigger fires, the composition agent MUST render the milestone overlay — no discretion. The LLM narrates only the Starter Shield amount; it does not read all milestones aloud.

---

## Attribution Strip (DD07) — Row Behaviour

### Row anatomy

`[Factor label]   [Description]   [+/− N.N months]`

- 5–9 rows (D1–D8 + optional D9)
- D9 row appended at bottom, labelled with LLM-assigned context
- Visual treatment of D9 is identical to D1–D8

### Row tap behaviour

| Tap | Action | Motion |
|:---|:---|:---|
| First tap | Level 2: one-sentence explanation | Crossfade 250ms `easeInOut` |
| Second tap | Level 3: formula + data source. Third tap collapses. | Crossfade 250ms `easeInOut` |

### Collapsed state

Default collapsed shows **top 3 factor rows** + affordance: "Show all N factors" (`TextButton`, Body Small, `secondary` colour). Tap expands to full row list.

### "Why is this different?"

| Condition | Behaviour |
|:---|:---|
| Target diverges from 3–6 months generic range | Auto-expands |
| Target within 3–6 months | Tappable only — does not auto-expand |

> **LLM agentic layer note:** The composition agent decides the editorial "key insight" — the single most surprising/high-impact factor to narrate. The spec does not prescribe which factor is the key insight.

---

## D3 Prompt Card

| Property | Value |
|:---|:---|
| Timing | Available after user taps "Looks right ✓". Agent decides exact timing |
| Position | Separate card, immediately BELOW Target Card in stream |
| Fill | `conditionalSurface` (DS01 §1) |
| Border | Dashed `conditionalBorder`, `dashPattern [6, 3]`, Large radius (DS01 §3) |
| Icon | 🎁 (emoji, not icon token) |
| CTA 1 | "Add a buffer" — `FilledButton` (primary) |
| CTA 2 | "Skip — not now" — `TextButton` (lower weight) |
| Skip action | Card fades out: opacity 0, 200ms `easeOut` |
| "Add a buffer" action | Range chips (§C1) + frequency selector appear inline within card. Computation agent calculates. D3 Confirmed Card replaces D3 Prompt Card (crossfade 250ms `easeInOut`) |
| D3 Confirmed Card | See §D3 Confirmed Card — Detailed Spec (State 4) below. References: J01 §3-D3, CG01 Phase 3 mandate. |

> **LLM agentic layer note:** Agent may defer D3 if emotional state is Anxious (CG01 §4 — Adaptive Composition wins: defer to next session or end of Phase 4). Agent logs deferral for re-surfacing.

---

## D3 Confirmed Card — Detailed Spec (State 4)

### Purpose

Replaces the D3 Prompt Card (State 2) in-place after the user taps "Add a buffer" → provides range/frequency inputs → computation agent calculates → D3 Confirmed Card renders. See §Structural Layouts → State 4 for the wireframe.

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| Card container | `Card` (M3) — `surfaceContainer`, Medium radius 12dp, elevation Level 1 | None |
| Card icon | 🎁 (emoji) | None |
| Title "Family Obligation Buffer" | Title Medium (16sp/500) | None |
| Annual amount | Body Medium (14sp/500), `warmPositive` | None |
| Calculation breakdown | Body Small (12sp/400), `onSurface` at 60% opacity | None |
| "Separate from" label | Body Small (12sp/400), `onSurface` at 60% opacity — **bold** "separate from" | None |
| CTA "Looks right ✓" | `FilledButton` — `primary` (smaller variant) | None |
| CTA "Remove buffer" | `TextButton` — lower visual weight | None |
| Crossfade from D3 Prompt | `AnimatedCrossFade` | Minimal — 250ms `easeInOut` |

### Visual Design

| Property | Value | Reference |
|:---|:---|:---|
| Card shape | Medium radius (12dp) — stream card, not phase-gate card | DS01 §3 |
| Card elevation | Level 1 (1dp) | DS01 §4 |
| Card fill | `surfaceContainer` (NOT `conditionalSurface` — dashed-border conditional treatment was for the *prompt* card; confirmed card uses standard fill to signal resolution) | DS01 §1 |
| Annual amount colour | `warmPositive` (confirmed = positive) | DS01 §1 |

### Interaction

| Action | Behaviour |
|:---|:---|
| "Looks right ✓" | Buffer confirmed. D3 data flows into Phase 4 allocation and Phase 5 contribution. Card fades to compact summary (one-line: "🎁 Buffer: ₹60K/yr — separate from EF") and scrolls with stream |
| "Remove buffer" | Buffer removed. Card fades out (200ms `easeOut`). D3 data cleared. No downstream impact on EF target |

### Composition

| Rule | Detail |
|:---|:---|
| Position | Replaces D3 Prompt Card in-place (crossfade 250ms `easeInOut`) |
| Density | Counts as 1 card (same slot as D3 Prompt — no net increase) |
| Phase ownership | Phase 3. Renders within the Phase 3 surface, below Target Card |

---

## Adaptive Behaviour — Literacy Fallback

| Literacy | Attribution Strip | Headline | Row expand depth |
|:---|:---|:---|:---|
| 1–2 | **REPLACED** by plain-English conversational breakdown. "Show me the breakdown" affordance persists to recover visual strip | Months only. No ₹ amount. Recovery affordance persists | Level 2 max (no formula) |
| 3 | Rendered. Collapsible. Default **collapsed** | Standard (₹ amount + months) | Level 2 default, Level 3 on second tap |
| 4–5 | Rendered. Auto-**expanded** by default. Level 3 on first tap | ₹ amount throughout | Level 3 on first tap |

---

## Adaptive Behaviour — Trust Level (CG01 §2.2)

| Trust Level | Attribution Strip Default | "Why different?" |
|:---|:---|:---|
| New / Warming | **Expanded** | Auto-expands on divergence |
| Trusting | **Collapsed** (headline sufficient) | Tappable only |
| Dependent | Headline + 1 key insight only | Suppressed unless divergence |

---

## Adaptive Behaviour — Emotional State (CG01 §2.3)

| State | Rendering | D3 Prompt Card | Row stagger |
|:---|:---|:---|:---|
| Calm | Standard | Standard timing | 60ms |
| Anxious | Standard card | **Deferred** (CG01 §4 conflict) | 100ms |
| Overwhelmed | **Headline-only:** ₹ + months + single CTA. Attribution hidden. "Show me why" affordance | Deferred | N/A |
| Excited | Attribution auto-expanded. Level 2 rows auto-open | Standard | 40ms |

---

## Adaptive Behaviour — Engagement Mode (CG01 §2.4)

| Mode | Rendering |
|:---|:---|
| Voice-heavy | CTA buttons full-width. Narration carries headline, screen carries strip. |
| Visual | All affordances prominent. Expand chevrons visible. |

---

## Composition Rules

| Rule | Detail |
|:---|:---|
| Appears | Once in Phase 3. Re-renders only on §C4 re-edit |
| D3 Prompt Card position | Always immediately BELOW Target Card in stream |
| D3 Confirmed Card | Replaces D3 Prompt Card in-place (crossfade 250ms `easeInOut`) |
| Starter Shield overlay | Replaces attribution strip within same card container |
| Density accounting | Counts as 1 card toward viewport limit (CG01 §3). D3 Prompt Card counts as +1 |
| Phase gate behaviour | Flow WAITS for explicit CTA ("Looks right ✓", "Adjust an answer", or "Got it — let's plan →") |

> User-state rendering rules: see §Adaptive Behaviour — Literacy / Trust Level / Emotional State / Engagement Mode above, and CG01 §2.

---

## Interaction Affordances

| Affordance | Action |
|:---|:---|
| "Looks right ✓" | Accepts target → surfaces D3 Prompt Card (or Starter Shield overlay if triggered first) |
| "Adjust an answer" | §C4 flow: re-renders §C3 Summary Card (CP01). On edit → number morph, attribution rows re-stagger |
| "Got it — let's plan →" | Starter Shield state only → proceeds to Phase 4 |
| Attribution row tap | Level 2 expand (crossfade 250ms) |
| Attribution row second tap | Level 3 expand (formula + data source) |
| "Show all N factors" | Expands collapsed rows |
| "Why is this different?" | Expand/collapse per trigger state |
| "Show me why" | Overwhelmed state: expands attribution from headline-only |
| "Show me the breakdown" | Literacy 1–2: recovers visual attribution strip from conversational fallback |
| D3 "Add a buffer" | Inline range chips + frequency selector → computation → D3 Confirmed Card |
| D3 "Skip — not now" | Card fades out (200ms `easeOut`) |

---
