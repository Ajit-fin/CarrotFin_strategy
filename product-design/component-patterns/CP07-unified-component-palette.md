# CarrotFin Component Palette — Stitch Prompts (v2 Unified)
## 14 Components · Input + Display + Composite · Flash/Pro-Compatible · Journey-Agnostic

> **Merged from:** display-component-stitch-prompts.md (v4) + confirmation-widget-stitch-prompts.md (v6)
> **New components:** `interactiveSorter`, `inlineInsight`, `contextualFootnote` (added to componentPalette.json)
> **Design system:** DS01 — M3 seed `#3CDDC7`, Financial Sanctuary aesthetic
> **Runtime reference:** [componentPalette.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/componentPalette.json)

---

## System Architecture

```
Flash / Pro decides:   componentId + data payload for current turn
Flash / Pro emits:     uiDirective { componentId, data } alongside responseText

INPUT components:      LLM populates full data payload (options, config, fields)
                       FE renders, handles confirm, sends callback to BE

DISPLAY components:    LLM populates framing only (icon, title, ctaLabel)
                       BE fills computed values (headline, factors, allocations, dates)
                       FE renders, handles callbacks

BE always:             injects componentKey after LLM output, before FE delivery
FE always:             sends {componentKey, action, payload} on user interaction
```

### Who Populates What?

| Responsibility | Owner | Notes |
|---|---|---|
| Component selection (`componentId`) | Flash / Pro | Per-turn, based on injected palette |
| Input data payload (options, config, fields) | Flash / Pro | Full config known at emit time |
| Display framing (`icon`, `title`, CTA labels) | Flash / Pro | Conversational choices |
| Computed values (headline, factors, layers, dates) | Computation agent via BE | After DCE runs |
| Resolved state (conditionalPrompt confirmedState) | BE — RESOLVE event | After user input + computation |
| `componentKey` | **BE — injected after LLM output** | Never set by LLM |
| State transitions, animations, formatting | FE | Spring-in, morph, stagger |
| Callback routing | FE → BE | `{componentKey, action, payload}` |

> [!IMPORTANT]
> **uiDirective is VALUE-ADD.** `responseText` renders on every turn by default. Only emit a uiDirective when the visual component genuinely adds beyond what the text already communicates.

---

## Palette Summary

### Input Components (LLM populates full data)

| Component | Use when | Confirm model |
|---|---|---|
| `optionSelector` | Pick 1 from 2–6 self-explanatory labels | Tap = confirm (auto) |
| `valueInput` | Single numeric/currency value | CTA button |
| `tieredSelector` | Pick 1 from levels needing title + description | Tap = confirm (auto) |
| `interactiveSorter` | Classify 4–10 items into 2–4 labelled buckets | Auto when all items sorted |
| `formGroup` | Multiple related fields in one go | Single CTA for all fields |
| `reviewCard` | Phase-gate — review all collected data | Single CTA to proceed |
| `fieldEditor` | Edit one row from reviewCard (FE-internal) | Auto or CTA by type |

### Display Components (LLM sets framing; BE fills computed values)

| Component | Use when | Confirm model |
|---|---|---|
| `resultReveal` | Present computed target/score + factor attribution | CTA (phase-gate) |
| `inlineInsight` | Mid-flow directional or partial result, non-blocking | None (read-only) |
| `allocationBar` | Proportional distribution across N categories | CTA (lighter confirm) |
| `milestonePlan` | Commitment headline + milestone timeline | CTA (phase-gate) |
| `actionChecklist` | Numbered post-plan action steps | Optional reminder + dismiss |
| `contextualFootnote` | Explanatory prose below a preceding component | None (read-only) |
| `conditionalPrompt` | Optional add-on with accept/skip | Dual CTA (accept/decline) |

### Composite Components (LLM populates full structure; user interacts before proceeding)

| Component | Use when | Confirm model |
|---|---|---|
| `planPreviewCard` | Plan preview for user review/approval before execution | Approve CTA (phase-gate) |

---

# INPUT COMPONENTS

---

## INPUT PROMPT 0 — Overview (Give first)

```
Design a reference sheet showing 7 generic INPUT components for "CarrotFin" — an AI personal
finance app. These appear INLINE in a conversational chat stream. Dark mode, M3 Material Design,
premium calm. Financial Sanctuary aesthetic.

Design system: Deep blue-grey background (M3 seed #3CDDC7). Cards use surfaceContainer (#1A1F2E).
Primary accent: teal #57F1DB. Warm amber #EE9800. No visible borders — tonal elevation only.
Inter font. Generous whitespace.

The 7 components:

1. OPTION SELECTOR — Horizontal chip row. Tap one → fills → auto-collapses to answer bubble.
   For categorical choices (2–6 options). No confirm button.

2. VALUE INPUT — Single numeric input: slider mode OR standard text field mode. One CTA button
   confirms. For currency amounts, percentages, durations, counts.

3. TIERED SELECTOR — Vertical stack of 3–4 cards with title + description each. Tap one →
   fills → auto-collapses. For leveled choices needing explanation. Optional skip card.

4. INTERACTIVE SORTER — A set of labelled category buckets + a pool of unsorted items below.
   User taps each item to assign it to a category (or cycles through categories by repeat-tapping).
   Auto-confirms when all items are sorted. For classification tasks (need vs. want, essential vs.
   discretionary, emergency vs. not).

5. FORM GROUP — A card containing multiple labelled input fields with ONE CTA at the bottom to
   confirm ALL fields. For grouped data like expense breakdown, income sources. Optional
   auto-totalling row.

6. REVIEW CARD — Summary card showing previously-collected fields as label:value rows. Single CTA
   to proceed. Tap a row → bottom sheet for editing. For phase-gate confirmations.

7. FIELD EDITOR (FE-internal) — Bottom sheet editor invoked by reviewCard row tap. Three variants:
   chips (categorical), numeric field, compact tier cards.

Show all 7 on one reference sheet with brief data examples. Financial Sanctuary dark aesthetic.
```

---

## INPUT PROMPT 1 — optionSelector (All States)

```
Design the "optionSelector" component for CarrotFin — categorical single-select.

Show TWO EXAMPLES side by side to prove extensibility:

── EXAMPLE A: 4 options with icons ──
Chip row: 🏛️ Salaried   🏢 Self-employed   🚀 Business owner   💻 Freelancer
3 states: DEFAULT (all outlined) → SELECTED (Salaried fills primary + ✓, others fade 30%)
          → COLLAPSED (answer bubble: "Salaried")

── EXAMPLE B: 3 options, no icons ──
Chip row: Low   Medium   High
3 states: same transitions

INVARIANTS:
- M3 FilterChip, 8dp radius, 40dp height, 12dp padding, 8dp gap
- Icons optional — from data, not hardcoded
- Tap = confirm = auto-proceed. No separate button.
- 300ms total: tap → fill → fade others → collapse to bubble → next turn
- Chips in bottom 60% of screen (thumb zone)
- 2–6 options. Horizontal scroll if overflow.
- All labels, icons from data — component is a rendering shell
```

---

## INPUT PROMPT 2 — valueInput (Slider + Standard Text Field)

```
Design the "valueInput" component for CarrotFin — single numeric input with TWO modes.

── MODE A: SLIDER ──

AI message: [placeholder question needing numeric range answer]

SLIDER:
• Full width, 16dp side padding
• Thin track (muted 30%). Active track: primary.
• Thumb: 24dp circle, primary, 2dp shadow.
• 5 labelled ticks below (values from data config — e.g., "10K | 30K | 50K | 70K | 90K")
• SMART DEFAULT: thumb pre-positioned from data.smartDefault (derived by LLM from user profile)
• LIVE VALUE above slider, centered: "₹50,000 / month" — 24sp bold. Updates as thumb moves.

CTA BUTTON (below slider, full width, 48dp):
• "₹50,000/mo — that's right ✓"
• Updates live with slider position
• Primary filled, 12dp radius

── MODE B: STANDARD TEXT FIELD ──

AI message: [placeholder question]

TEXT FIELD:
• Standard M3 TextField, 48dp height, 12dp radius
• NUMERIC KEYBOARD — not full text keyboard
• Left prefix: "₹" in primary
• Right suffix: "/ month" in muted
• Placeholder: "e.g., 120000" — numeric example only
• AUTO-FORMAT on input: as user types "120000", field displays "1,20,000" (Indian comma grouping).
  This is standard locale formatting, not NLP parsing.

CTA BUTTON (below text field, full width, 48dp):
• "₹1,20,000/mo — continue →"
• Disabled (30%) until valid number entered
• Shows formatted value

INVARIANTS:
- ONE CTA is the only confirm. No chips, no ✓/✏ pairs.
- Slider: CTA updates live. Text: CTA shows formatted value.
- FE handles: ₹ formatting, Indian comma notation, tick labels.
- Numeric keyboard enforced for text mode.
- Free-text number parsing ("1.2 lakhs") is handled by the extraction prompt, NOT by this component.
- Flow blocked until CTA tap.
```

---

## INPUT PROMPT 3 — tieredSelector (All States)

```
Design the "tieredSelector" component for CarrotFin — ordinal selection with descriptive cards.

── EXAMPLE A: 3 tiers + skip ──

4 CARDS stacked vertically (8dp gap):
  TIER 1: 🟢 dot · "Level one label" (16sp bold) · "One line description" (14sp, 60% opacity)
  TIER 2: 🟡 dot · "Level two label" · "One line description"
  TIER 3: 🟠 dot · "Level three label" · "One line description"
  SKIP:   🔒 icon · "Skip label" · "What happens if skipped"

Cards: full width, ~64dp min-height, 12dp radius, card surface, 1dp outline.
Skip card: SAME SIZE and visual weight — never smaller.

3 states: DEFAULT (all outlined, equal) → SELECTED (Tier 2 fills primary, others fade 30%)
          → COLLAPSED (bubble: "Tier 2 label")

── EXAMPLE B: 2 tiers, no skip ──
Just 2 cards, same transitions.

INVARIANTS:
- Auto-confirm on tap (same as optionSelector). No button.
- Tier dots: colour from data or auto by ordinal (green → amber → orange)
- Description wraps — height is content-driven
- All labels/descriptions from data — component is a rendering shell
- 300ms transition total
- skipOption: nullable. Only render skip card when data includes it.
```

---

## INPUT PROMPT 4 — interactiveSorter (Classification) — NEW

```
Design the "interactiveSorter" component for CarrotFin — item classification by tapping.

This component presents a pool of labelled items and asks the user to sort each item into one
of 2–4 named category buckets. Auto-confirms when ALL items are sorted — no CTA button.

── EXAMPLE A: Need vs. Want (4 items, 2 categories) ──

AI message: "Tap each expense to sort it — it helps me understand your priorities."

LAYOUT:
  Top section — CATEGORY BUCKETS (2 horizontal chips, full width):
    [ 🛡️ Essential ]     [ ✨ Discretionary ]
    Each bucket shows a live count badge: "3 items" as items are assigned.
    Selected bucket (most recently tapped) shows a subtle active state.

  Divider: "Sort each item below" — Label Small, 60% opacity

  Item pool — 4 ITEM CHIPS below (wrapping flex row, 8dp gap):
    [ 🏠 Housing ]   [ 🍕 Dining out ]   [ 💊 Medical ]   [ 🎬 Entertainment ]

  Each item chip: 40dp height, 12dp radius, outlined. Unassigned = neutral.
  On tap → item chip animates to the corresponding bucket zone (150ms easeOut).
  Assigned chip shows bucket colour tint (20% opacity) + category label in muted text beneath.
  Tap again → cycles to next category. Tap a third time → unassigns (back to neutral).

  PROGRESS INDICATOR: "3 of 4 sorted" — subtle pill at top right. Turns primary when 4/4.

  AUTO-CONFIRM: When all items assigned → brief 300ms "All sorted ✓" state → auto-collapses.
  Collapsed view: one-line summary "Sorted: Housing, Medical = Essential · Dining out,
  Entertainment = Discretionary"

── EXAMPLE B: Emergency vs. Non-emergency (6 items, 2 categories) ──
Same layout, 6 items, buckets: [ ⚡ Emergency-worthy ] [ 📦 Not emergency ].

── EXAMPLE C: Priority sort (4 categories, 8 items) ──
4 bucket chips across top (horizontal scroll if overflow).
8 items in wrapping pool.

INVARIANTS:
- Bucket chips at top, item pool below — always this layout
- Bucket count: 2–4 (from data). Item count: 4–10 (from data).
- Auto-confirm: triggers ONLY when all items assigned. No CTA button.
- Each item can only belong to one category at a time. Tap cycles: A → B → C → unassigned
- Drag-to-sort is optional FE enhancement — baseline is tap-to-assign
- Collapsed view shows a summary map: category → assigned items
- All bucket labels, item labels, icons from data — rendering shell
- AI may react adaptively after partial sorting (e.g., 5 of 8 assigned) — FE sends intermediate
  callbacks: {componentKey, action: "PARTIAL", sorted: {cat: [items], ...}, remaining: [items]}
- Final callback: {componentKey, action: "CONFIRM", result: {cat: [items], ...}}
```

---

## INPUT PROMPT 5 — formGroup (Multi-Field Input)

```
Design the "formGroup" component for CarrotFin — multiple related fields with one CTA.

This is for cases where asking each field individually would be too slow — e.g., expense
breakdown, income sources, goal parameters.

── EXAMPLE A: Expense breakdown (4 numeric fields + total) ──

AI message: "Let's break down your monthly expenses. Rough numbers are fine."

FORM CARD:
• M3 Card, 16dp radius, 6dp elevation, 16dp padding
• Header: "Monthly Expenses" — 22sp/400

• 4 INPUT ROWS, each:
  - Label (left, 14sp, 60% opacity): "Housing / Rent"
  - Text field (right, aligned): standard numeric, ₹ prefix, compact (40dp height, 120dp width)
  - Numeric keyboard on focus. Auto-format: Indian comma notation.

  Row 1: Housing / Rent        [₹ 35,000  ]
  Row 2: Groceries & household [₹ 12,000  ]
  Row 3: Transport             [₹ 5,000   ]
  Row 4: Education / childcare [₹ 8,000   ]

• TOTAL ROW (auto-calculated): "Total" · "₹60,000 / month" — updates live. Primary colour.
• SINGLE CTA (full width): "Continue →" — primary filled, 48dp

── EXAMPLE B: Mixed-type fields ──

Header: "Income Sources"
  Row 1: Primary income    [₹ 1,20,000 ] (numeric)
  Row 2: Type              [Salaried ▾  ] (compact inline dropdown)
  Row 3: Additional income [₹ 0         ] (numeric, optional)
• No total row. CTA: "Save"

INVARIANTS:
- ONE CTA confirms ALL fields — no per-field confirm
- Numeric fields: standard numeric keyboard, ₹ prefix, auto-format. NOT real-time NLP parsing.
- Categorical fields: compact inline dropdown (not full chip row)
- Total row: optional, auto-calculated, read-only. showTotal = true in data.
- CTA disabled if required fields empty
- Card scrollable if > 6 fields
- Fields feel like a clean ledger — labels left (muted), values right (prominent)
```

---

## INPUT PROMPT 6 — reviewCard (Summary + Bottom Sheet)

```
Design the "reviewCard" component — phase-gate summary with bottom sheet editing.

── STATE 1: REVIEW ──

REVIEW CARD (~65% viewport):
• M3 Card, 16dp radius, 6dp elevation, 16dp padding
• Header: [title from data] — 22sp/400

• N DATA ROWS (from data.rows[]):
  Field A         Value A
  Field B         Value B
  Field C         ₹1,20,000/mo
  Field D         Value D

  Rows: label 14sp 60% left, value 14sp white right, 48dp height, subtle divider.
  ON PRESS: brief highlight (primaryContainer 8%). No edit icons at rest.
  Rows with editable = false: no press response.

• SINGLE CTA (full width): [ctaLabel from data] — primary filled, 48dp

── STATE 2: BOTTOM SHEET EDIT ──

Card visible behind M3 scrim (40% black).
Sheet slides up with drag handle:
• Label: "Field Name" — 18sp bold
• Current: "Currently: [value]" — 14sp muted
• Divider
• Input widget (based on fieldType):
  CATEGORICAL → chip set · tap = auto-save + dismiss
  NUMERIC → standard numeric field + "Save" CTA
  TIERED → compact tier cards · tap = auto-save + dismiss
• After save: sheet dismisses, row updates, "Updated" label fades in 2s.

INVARIANTS:
- Clean rows at rest — press-highlight is the only edit affordance
- Single CTA, no secondary button
- Bottom sheet: M3 BottomSheet with drag handle + scrim. Swipe-down to cancel.
- FE constructs editor from row.fieldType + row.editConfig
- Flow blocked until CTA tap
```

---

## INPUT PROMPT 7 — Bottom Sheet Edit Variants (FE Reference)

```
Design 3 bottom-sheet editor variants for CarrotFin — showing how different field types render.

── VARIANT A: CATEGORICAL ──
Sheet: Label + current + divider + chip set.
Tap different chip → auto-saves → sheet dismisses. NO Save button.

── VARIANT B: NUMERIC ──
Sheet: Label + current + divider + standard numeric text field (₹ prefix, numeric keyboard,
auto-format) + "Save — ₹[value]" CTA.

── VARIANT C: TIERED ──
Sheet: Label + current + divider + compact tier cards (56dp).
Tap different card → auto-saves → sheet dismisses. NO Save button.

ALL share: drag handle, same layout structure, swipe-down to cancel.
Sheet height adapts: ~200dp chips, ~240dp numeric, ~320dp tier cards.
```

---

## Input State Matrix

| Component | State | Trigger | Blocks flow? | User effort |
|---|---|---|---|---|
| `optionSelector` | Default → Selected → Collapsed | Tap chip | No (auto) | **1 tap** |
| `valueInput` SLIDER | Awaiting → Value Set → Confirmed | Drag + CTA tap | **Yes** | 1–2 |
| `valueInput` TEXT | Awaiting → Typed → Confirmed | Type + CTA tap | **Yes** | type + 1 tap |
| `tieredSelector` | Default → Selected → Collapsed | Tap card | No (auto) | **1 tap** |
| `interactiveSorter` | Sorting → Partial → All sorted → Collapsed | Tap items | No (auto when done) | N taps |
| `formGroup` | Filling → Confirmed | Fill fields + CTA tap | **Yes** | N fields + **1 tap** |
| `reviewCard` | Review → Confirmed | CTA tap | **Yes** | **1 tap** |
| `fieldEditor` | Open → Saved | Select/enter + save | **Yes** (sheet) | **1 tap** |

---

## Input Data Contracts

> **LLM populates:** all data fields — `fieldKey`, `options`, `config`, `tiers`, `categories`, `items`, `fields`, `rows`.
> **BE injects:** `componentKey` after LLM output. FE uses it for callbacks: `{componentKey, action, payload}`.

```json
// ─────────────────────────────────────────────────────
// optionSelector
// ─────────────────────────────────────────────────────
{ "componentId": "optionSelector",
  "data": {
    "fieldKey": "string — maps to requiredInputs.fieldName",
    "options": [
      { "label": "string", "value": "string", "icon": "string?" }
    ]
  }
}
// FE callbacks:
//   Tap chip: {componentKey, action: "CONFIRM", fieldKey, value: "selected_value"}
//   Auto-confirms — no separate CTA.

// ─────────────────────────────────────────────────────
// valueInput — slider
// ─────────────────────────────────────────────────────
{ "componentId": "valueInput",
  "data": {
    "fieldKey": "string",
    "inputType": "SLIDER",
    "config": {
      "unit": "₹",
      "period": "MONTHLY",
      "min": 10000,
      "max": 200000,
      "step": 5000,
      "smartDefault": 60000
    }
  }
}

// valueInput — text
{ "componentId": "valueInput",
  "data": {
    "fieldKey": "string",
    "inputType": "TEXT",
    "config": {
      "unit": "₹",
      "period": "MONTHLY",
      "placeholder": "e.g., 120000"
    }
  }
}
// FE callbacks (both modes):
//   CTA tap: {componentKey, action: "CONFIRM", fieldKey, value: rawNumber, formattedValue: "string"}

// ─────────────────────────────────────────────────────
// tieredSelector
// ─────────────────────────────────────────────────────
{ "componentId": "tieredSelector",
  "data": {
    "fieldKey": "string",
    "tiers": [
      { "label": "string", "description": "string", "value": "string", "icon": "string?" }
    ],
    "skipOption": { "label": "string", "description": "string", "value": "string" }
  }
}
// skipOption: nullable. Absent = no skip card rendered.
// FE callbacks:
//   Tap card:  {componentKey, action: "CONFIRM", fieldKey, value: "selected_value"}
//   Tap skip:  {componentKey, action: "CONFIRM", fieldKey, value: "skip"}

// ─────────────────────────────────────────────────────
// interactiveSorter
// ─────────────────────────────────────────────────────
{ "componentId": "interactiveSorter",
  "data": {
    "fieldKey": "string — maps to requiredInputs.fieldName",
    "instruction": "string — task framing (e.g., 'Tap each expense to sort it')",
    "categories": [
      { "label": "string — bucket label", "value": "string — stored value", "icon": "string?" }
    ],
    "items": [
      { "label": "string — item text", "icon": "string?" }
    ]
  }
}
// limits: 2–4 categories, 4–10 items
// FE callbacks:
//   Partial sort: {componentKey, action: "PARTIAL", sorted: {catValue: [itemLabels]}, remaining: [itemLabels]}
//   All sorted:   {componentKey, action: "CONFIRM", result: {catValue: [itemLabels]}}

// ─────────────────────────────────────────────────────
// formGroup
// ─────────────────────────────────────────────────────
{ "componentId": "formGroup",
  "data": {
    "title": "string",
    "ctaLabel": "string",
    "showTotal": true,
    "totalLabel": "string?",
    "fields": [
      { "fieldKey": "string", "label": "string",
        "inputType": "NUMERIC | CATEGORICAL",
        "required": false,
        "config": { "unit": "₹", "period": "MONTHLY", "options": [] }
      }
    ]
  }
}
// FE callbacks:
//   CTA tap: {componentKey, action: "CONFIRM", fields: [{fieldKey, value}, ...]}

// ─────────────────────────────────────────────────────
// reviewCard
// ─────────────────────────────────────────────────────
{ "componentId": "reviewCard",
  "data": {
    "title": "string — card header (e.g., 'Your Profile', 'Goal Summary')",
    "ctaLabel": "string",
    "rows": [
      { "fieldKey": "string", "label": "string",
        "displayValue": "string", "editable": true,
        "fieldType": "CATEGORICAL | NUMERIC | TIERED",
        "editConfig": {} }
    ]
  }
}
// FE callbacks:
//   CTA tap:    {componentKey, action: "CONFIRM"}
//   Row tap:    {componentKey, action: "EDIT", fieldKey: "tapped_field"}
//               → FE opens fieldEditor using row.fieldType + row.editConfig
//   After edit: {componentKey, action: "ROW_UPDATED", fieldKey, value: newValue}

// ─────────────────────────────────────────────────────
// fieldEditor (FE-internal — LLM never emits this)
// FE constructs from reviewCard row data
// ─────────────────────────────────────────────────────
// FE callbacks:
//   Save:   {componentKey: parentReviewCard.componentKey, action: "ROW_UPDATED", fieldKey, value}
//   Cancel: no callback (swipe-down or tap scrim)
```

---

# DISPLAY COMPONENTS

---

## DISPLAY PROMPT 0 — Overview (Give first)

```
Design a reference sheet showing 6 generic DISPLAY components for "CarrotFin" — an AI personal
finance app. These appear INLINE in a conversational chat stream to present computed results,
plans, insights, and recommendations. Dark mode. Premium, editorial, "Financial Sanctuary" aesthetic.

Design system: Deep blue-grey background (M3 seed #3CDDC7 teal cyan). Cards use surfaceContainer
(#1A1F2E). Primary accent: teal #57F1DB. Warm amber #EE9800 for positive/momentum. No visible
borders — tonal elevation for separation. Inter font. Generous whitespace.

The 6 components:

1. RESULT REVEAL — Phase-gate card with a big headline result (₹ value + context label), an
   attribution strip of factor rows explaining "why this number" with progressive disclosure,
   and CTA buttons. For computed targets, scores, assessments.

2. INLINE INSIGHT — Non-blocking mid-flow card. Lighter than resultReveal. Title + optional
   subtitle + optional expandable data rows (label, impact, direction). No CTA. Conversation
   continues immediately below. For directional signals, partial results, value-after moments.

3. ALLOCATION BAR — Card with a proportional horizontal strip (N colour-coded zones) + detail
   rows below. Lighter confirm. For distribution breakdowns: allocation layers, budget splits.

4. MILESTONE PLAN — Card with an editable headline commitment (₹ amount + cadence) + timeline
   of N milestone rows with computed dates. Highlighted first milestone. Phase-gate CTA.

5. ACTION CHECKLIST — Card with numbered, priority-ordered action steps. Optional reminder CTA
   + dismiss. Persists beyond conversation. For post-plan setup instructions.

6. CONTEXTUAL FOOTNOTE — Visually subordinate explanatory prose (1–3 sentences) rendered
   immediately below the preceding component. No CTA. Smaller type, muted colour. For
   disclosures, regulatory notes, or "good to know" context.

7. CONDITIONAL PROMPT — Dashed-border card offering an optional add-on. Accept/decline CTAs.
   On accept: transitions to confirmed state. On decline: fades out.

Show all on one reference sheet with brief data examples. Financial Sanctuary dark aesthetic.

IMPORTANT: These are REUSABLE COMPONENT SHELLS, not specific screens. Same layout works with
any data plugged into the slots.
```

---

## DISPLAY PROMPT 1 — resultReveal (All States)

```
Design the "resultReveal" component for CarrotFin — computed result with factor attribution.

Design system: M3 seed #3CDDC7, dark mode, Financial Sanctuary. surfaceContainer (#1A1F2E).
Primary #57F1DB. Warm amber #EE9800. No visible card borders. Inter font.

ANATOMY (fixed structure — data in [BRACKETS] are slots):
┌────────────────────────────────────────┐
│ [ICON]  [TITLE]                          │
│                                          │
│ [HEADLINE.VALUE]    (Display Medium)      │
│ [HEADLINE.SUBTITLE] (60% opacity)         │
│                                          │
│ ── [ATTRIBUTION.HEADER] ──────────────   │
│ [FACTOR.LABEL] [FACTOR.DESC] [FACTOR.IMPACT] │
│ [FACTOR.LABEL] [FACTOR.DESC] [FACTOR.IMPACT] │
│ ...N factor rows (3–7)...                 │
│ "Show all" link (if > 5 rows)             │
│                                          │
│ [CTA_PRIMARY]          [CTA_SECONDARY]   │
└────────────────────────────────────────┘

Show this shell with TWO DIFFERENT data sets side by side:

── EXAMPLE A: Emergency fund target with 7 factors ──
  Icon + Title: "🛡️ Your Emergency Fund Target"
  HEADLINE: "₹4.2L" · "8 months of essential expenses" (60% opacity)
  Attribution: "Why this number" — 7 factor rows
    Positive in warm amber (#EE9800). Negative in cool slate (#5A8CA6).
    Top 3 visible; "Show all 7 factors" link. Tap row → expand explanation.
  CTAs: "Looks right ✓" (filled, primary, pill) / "Adjust an answer" (text)

── EXAMPLE B: Retirement readiness score with 4 factors ──
  Icon + Title: "📊 Your Retirement Readiness"
  HEADLINE: "72%" · "on track with adjustments"
  Attribution: "Score breakdown" — 4 rows (all visible, no collapse)
  CTAs: "Continue →" / "See details"

Show 4 STATES (vertical storyboard for Example A):
  STATE 1 — MATERIALISING: Card springs up. Headline first. Rows stagger 60ms each.
  STATE 2 — REVIEW: Top 3 rows, "Show all" link, CTAs active.
  STATE 3 — ROW EXPANDED: One row shows Level 2 explanation.
  STATE 4 — CONFIRMED: "Looks right ✓" tapped. Card seals (400ms).

INVARIANTS:
- Card: 16dp radius, surfaceContainer fill, NO border, tinted shadow (primary at 6%, 48dp blur)
- Headline: Display Medium (45sp/400)
- Attribution rows: Body Medium (14sp). Positive = amber. Negative = slate. Neutral = 60%.
- Progressive disclosure: tap → Level 2 → Level 3 → collapse
- ≤5 factors: all visible. >5: top 3 + "Show all" link
- Phase-gate: flow blocked until primary CTA tapped
- No-Divider Rule: 8dp whitespace between rows (attribution sub-header divider is the only line)
- All labels, factors, CTAs from data — rendering shell
```

---

## DISPLAY PROMPT 2 — inlineInsight (Non-blocking Mid-flow) — NEW

```
Design the "inlineInsight" component for CarrotFin — lightweight, non-blocking insight card.

This is lighter than resultReveal: it adds structured context mid-conversation without
gating the flow. The conversation continues immediately below the card. No primary CTA.

Design system: M3 seed #3CDDC7, dark mode, Financial Sanctuary. surfaceContainer (#1A1F2E).
Slightly lower visual prominence than resultReveal — smaller headline, no phase-gate border
treatment. Inter font.

ANATOMY (fixed structure):
┌────────────────────────────────────────┐
│ [ICON]  [TITLE]                          │
│ [SUBTITLE] (optional, 60% opacity)       │
│                                          │
│ [ROW.LABEL]   [ROW.IMPACT]  [▲/▼/—]     │  optional
│ [ROW.LABEL]   [ROW.IMPACT]  [▲/▼/—]     │  optional
│ ...N rows (0–5)...                       │
└────────────────────────────────────────┘
[conversation continues immediately below]

Show TWO EXAMPLES stacked (no CTAs — conversation text follows each):

── EXAMPLE A: Partial assessment signal (no data rows — LLM-only) ──
  Card: "📍 Initial read"
  Title: "Your EF target is likely in the ₹3–5L range"
  Subtitle: "Confirming a few more numbers to refine this"
  No data rows. A single-line card — minimal footprint.
  Below card: AI continues: "Let me ask about your monthly expenses next."

── EXAMPLE B: Income vs expense snapshot (3 data rows) ──
  Card: "📊 Quick snapshot"
  Title: "Based on what you've shared"
  Subtitle: "Before we calculate the final target"
  3 DATA ROWS:
    Monthly income     ₹1,20,000       —
    Essential expenses ₹52,000         —
    Savings rate       34%             ▲ healthy
  Tap row → expands with one-sentence explanation (crossfade 250ms, same as resultReveal)
  Below card: AI continues: "That savings rate is a good sign. Let me work out…"

STATES:
  STATE 1 — MATERIALISING: Card fades in + slides up (softer spring than resultReveal — shorter distance).
  STATE 2 — RESTING: Card inline in stream. Rows tappable for expand. No CTA.
  (Card persists in stream scroll — no dismiss.)

INVARIANTS:
- Non-blocking: no CTA, no flow gate. Conversation continues below.
- Card: 16dp radius, surfaceContainer, no border, tinted shadow (lighter than resultReveal)
- Title: Title Large (22sp/500) — smaller than resultReveal's Display Medium
- Subtitle: Body Medium (14sp), 60% opacity
- Data rows: optional (0–N). Card renders without rows — title + subtitle is valid.
- Row format: label (left) / impact (right) / direction indicator (▲▼—)
  ▲ = positive (amber), ▼ = negative (slate), — = neutral (60% opacity)
- Row tap → expand with one-sentence explanation. Same crossfade as resultReveal.
- No "Show all" collapse — all rows visible (0–5 max)
- Density: same card footprint as other display components. Does NOT take full viewport.
- All content from data (LLM sets icon, title, subtitle; BE fills rows[])
```

---

## DISPLAY PROMPT 3 — allocationBar (Proportional Strip + Details)

```
Design the "allocationBar" component for CarrotFin — proportional distribution with detail rows.

Design system: M3 seed #3CDDC7, dark mode, Financial Sanctuary. surfaceContainer (#1A1F2E).
Primary #57F1DB. No visible card borders. Inter font.

ANATOMY (fixed structure):
┌────────────────────────────────────────┐
│ [ICON]  [TITLE]                          │
│                                          │
│ ┌───────┐┌─────────────────┐┌───────────┐ │
│ │ ZONE1 ││     ZONE 2       ││  ZONE 3   │ │
│ └───────┘└─────────────────┘└───────────┘ │
│ (proportional widths, 2–5 zones)          │
│                                          │
│ [●] [LAYER.ICON] [LAYER.LABEL] · [VALUE]  │
│       [LAYER.DESCRIPTION]                 │
│ ...N detail rows matching zone count...   │
│                                          │
│ [CTA_PRIMARY]          [CTA_SECONDARY]   │
└────────────────────────────────────────┘
[FOOTNOTE] (optional, outside card)

Show TWO DIFFERENT data sets stacked:

── EXAMPLE A: 3-zone liquidity allocation ──
  Title: "🏦 Your Fund Structure"
  STRIP (56dp height, 12dp radius): 3 zones with 1dp gaps (card bg showing through):
    Zone 1 (17%): SOLID warm amber (#EE9800) — "₹96K · 1mo"
    Zone 2 (53%): SOLID muted teal (#2A8A7E) — "₹2.9L · 3mo"
    Zone 3 (30%): SOLID cool slate (#3D6B82) — "₹2.5L"
  Each zone: SOLID OPAQUE rectangle. No transparency, no gradients.
  3 DETAIL ROWS (colour dot matching zone + icon + label + vehicle + description):
    ⚡ Instant Access · ₹96K · Savings/Sweep-in FD
    ⏱ Quick Access · ₹2.9L · Liquid Mutual Fund
    🔒 Stable Reserve · ₹2.5L · Short-term FD
  CTAs: "This works for me ✓" / "Tell me more"
  FOOTNOTE (below card): "Deposit insurance covers ₹5L per bank — spread FDs across 2+ banks."

── EXAMPLE B: 4-zone portfolio ──
  Title: "📈 Your Investment Mix"
  4 zones. 4 detail rows. CTAs: "Continue →" / "Adjust mix". No footnote.

Show 3 STATES for Example A:
  STATE 1 — MATERIALISING: Zones expand L→R 80ms stagger. Rows stagger 60ms. Footnote fades last.
  STATE 2 — REVIEW: All visible. Row tap → expand (crossfade 250ms).
  STATE 3 — CONFIRMED: CTA tapped. Card seals.

INVARIANTS:
- Strip: 56dp height, 12dp ClipRRect radius. SOLID OPAQUE fills. 1dp gap between zones.
- Zone widths: proportional. Minimum 56dp. Zone text: white, Label Medium (12sp/500).
- Detail rows: colour dot (8dp) + icon + label + amount + vehicle / description
- No-Divider Rule: 8dp whitespace between rows
- Footnote: OUTSIDE card, Body Small 12sp, 60% opacity. Conditional — only when data.footnote present.
- Zone count from data (2–5). Component adapts.
- "Tell me more" is non-blocking — AI elaborates in stream while card persists.
- All labels, amounts, zone count from data — rendering shell
```

---

## DISPLAY PROMPT 4 — milestonePlan (Commitment + Timeline)

```
Design the "milestonePlan" component for CarrotFin — commitment headline + milestone timeline.

Design system: M3 seed #3CDDC7, dark mode, Financial Sanctuary. surfaceContainer (#1A1F2E).
Primary #57F1DB. Warm amber #EE9800. No visible card borders. Inter font.

ANATOMY (fixed structure):
┌────────────────────────────────────────┐
│ [ICON]  [TITLE]                          │
│                                          │
│ [HEADLINE.VALUE]  (Display Medium, tappable) │
│ [HEADLINE.CADENCE] (60% opacity)          │
│ "tap to edit"                             │
│                                          │
│ ── [MILESTONE_HEADER] ────────────────   │
│ ★ [LABEL] [₹ AMT] → [DATE]               │ highlighted
│ · [LABEL] [₹ AMT] → [DATE]               │
│ ...N milestones (2–6)...                  │
│                                          │
│ [SUMMARY_LINE] (optional, 60% opacity)    │
│ [CTA_PRIMARY] (full width)               │
└────────────────────────────────────────┘

Show TWO DIFFERENT data sets:

── EXAMPLE A: Monthly savings plan (4 milestones) ──
  Title: "📅 Your Contribution Plan"
  Headline: "₹6,000" / "/ month · on your salary date" / "tap to edit"
  Sub-header: "Your milestone path"
  4 rows: ★ Starter Shield ₹96K → Aug 2026
          · 3 Months Secure ₹2.9L → Oct 2027
          · Half-Year Shield ₹5.8L → Oct 2028
          · Fully Funded ₹11.5L → Apr 2031
  ★ row: warm amber background glow (15% opacity). Dates in "MMM YYYY" format.
  Summary: "Your self-EMI: ₹6,000 · every salary day · auto-transfer"
  CTA: "Confirm my plan →" (full width, primary, pill, 48dp)

── EXAMPLE B: SIP plan (3 milestones) ──
  Title: "📈 Your SIP Plan". Headline: "₹15,000" / "/ month · SIP on 5th"
  3 milestones. CTA: "Start my SIP →"

Show 4 STATES for Example A:
  STATE 1 — MATERIALISING: Title + headline first. Rows stagger 60ms. CTA last.
  STATE 2 — REVIEW: Headline ₹ tappable (subtle highlight). CTA active.
  STATE 3 — EDITING: Tap ₹6,000 → inline numeric editor replaces text (₹ prefix, numeric KB,
    auto-format). Dates recalculate live (number morph per date, spring 250/0.85).
    ✓ button or tap outside saves. NOT a bottom sheet — inline replacement.
  STATE 4 — CONFIRMED: CTA tapped. Card becomes read-only. Plan sealed.

INVARIANTS:
- Headline: Display Medium (45sp/400). Tappable when editable = true.
- Inline edit: FE seeds from rawValue; sends {componentKey, action: "EDIT", fieldKey, value}
  BE recomputes → returns updated milestones[] → FE re-renders with number morph.
- ★ highlighted row: warm amber glow 15%. At most ONE highlighted row.
- Dates: "MMM YYYY" format — real dates, not relative durations
- Milestone count from data (2–6). Component adapts.
- Phase-gate: flow blocked until CTA tapped
- No-Divider Rule: 8dp whitespace between milestones
- All labels, amounts, dates, CTA text from data — rendering shell
```

---

## DISPLAY PROMPT 5 — actionChecklist (Numbered Steps)

```
Design the "actionChecklist" component for CarrotFin — numbered action steps, non-blocking.

Design system: M3 seed #3CDDC7, dark mode, Financial Sanctuary. surfaceContainer (#1A1F2E).
Primary #57F1DB. No visible card borders. Inter font.

ANATOMY (fixed structure):
┌────────────────────────────────────────┐
│ [ICON]  [TITLE]                          │
│ [SUBTITLE] (60% opacity, optional)       │
│                                          │
│ ① [STEP.ICON] [STEP.LABEL] — [VALUE]    │
│   [STEP.INSTRUCTION]                     │
│ ② ...                                   │
│ ...N steps (2–5)...                      │
│                                          │
│ [REMINDER_CTA] (optional)  [DISMISS_CTA] │
└────────────────────────────────────────┘

Show TWO DIFFERENT data sets:

── EXAMPLE A: 3-step fund setup ──
  Title: "✅ Your Action Plan" · Subtitle: "Set these up on your next salary date."
  ① ⚡ Instant Access layer — ₹96K
     "Set up an auto-transfer to savings or sweep-in FD. Do this first."
  ② ⏱ Quick Access layer — ₹2.9L
     "Recurring amount to any liquid mutual fund. Money back next business day."
  ③ 🔒 Stable Reserve — ₹2.5L
     "FD or short-term debt fund. Do this after Layer 1 and 2 are set."
  CTAs: "Remind me on salary day" (outlined) / "Dismiss" (text)

── EXAMPLE B: 2-step SIP setup ──
  ① 📱 Open demat + trading account / ② 📊 Set up your SIP — ₹15,000/mo
  CTA: "Remind me on the 5th" / "Dismiss"

Show 3 STATES for Example A:
  STATE 1 — MATERIALISING: Card springs up 300ms after preceding AI message. Steps stagger 60ms.
  STATE 2 — RESTING: Tap step → expand brief explanation (crossfade 250ms). NOT a bottom sheet.
  STATE 3 — DISMISSED: "Dismiss" tapped. Card fades (opacity 0, 200ms easeOut).

INVARIANTS:
- Step badge: CircleAvatar 24dp, primary fill, Label Medium 12sp/500
- Step label: Body Medium (14sp/500) — icon + label + optional ₹ amount
- Step instruction: Body Small (12sp/400), 60% opacity
- Steps: 12dp whitespace between. No dividers.
- NOT a phase-gate — appears after preceding confirmation. Non-blocking.
- Reminder CTA: nullable. Dismiss: fades card from stream (persists on home surface).
- Step count from data (2–5). Component adapts.
- All labels, instructions, amounts from data — rendering shell
```

---

## DISPLAY PROMPT 6 — contextualFootnote (Subordinate Prose) — NEW

```
Design the "contextualFootnote" component for CarrotFin — explanatory prose below a parent component.

This is NOT a card. It is visually subordinate text that renders directly below the preceding
uiDirective component. No background, no border, no CTA. It provides factual context, disclosures,
or "good to know" notes that support the parent component without interrupting the flow.

Design system: M3 seed #3CDDC7, dark mode. Body Small (12sp/400). onSurface at 55% opacity
(more muted than standard 60% card text). 8dp margin above (gap from parent component edge).

ANATOMY:
[parent component above]
  [ICON?] [TEXT — 1–3 sentences of plain prose]
[conversation continues below]

Show TWO EXAMPLES (each shown immediately below a parent component):

── EXAMPLE A: Below an allocationBar ──
  Parent: allocationBar showing 3-zone EF structure
  Footnote (immediately below, 8dp margin):
    "⚠️ Deposit insurance covers ₹5L per bank. If your stable reserve exceeds ₹5L,
    spread it across two banks to stay fully insured."
  (No box, no border — just muted text)

── EXAMPLE B: Below a resultReveal ──
  Parent: resultReveal showing insurance needs assessment
  Footnote:
    "ℹ️ This estimate is based on the information you've shared. Your final premium
    will depend on medical underwriting at the time of application."

INVARIANTS:
- Body Small (12sp/400), onSurface at 55% opacity — visually subordinate
- NO card background, NO border, NO elevation. Plain text only.
- Optional leading emoji/icon (from data) — provides context signal without visual weight
- Always renders immediately below the preceding uiDirective component
- 8dp top margin from parent card edge. 16dp side padding (aligns with card content).
- 1–3 sentences max. Never a heading, never a bullet list.
- Never use for warnings, CTAs, or anything requiring user response — those stay in chat or a component
- Non-blocking. Conversation continues below.
- icon and text both from data — rendering shell
```

---

## DISPLAY PROMPT 7 — conditionalPrompt (Optional Add-On)

```
Design the "conditionalPrompt" component for CarrotFin — optional add-on with accept/decline.

Design system: M3 seed #3CDDC7, dark mode. One visual exception: ghost border
(outlineVariant #3C4A46 at 15% opacity) signals "optional / unresolved."
surfaceContainerLow (#141928) fill — slightly different from standard cards. Inter font.

ANATOMY — TWO STATES:

PROMPT STATE (ghost border = unresolved):
┌─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
│ [ICON]  [TITLE] (Optional)              │
│ [DESCRIPTION]                           │
│                                         │
│ [ACCEPT_CTA]           [DECLINE_CTA]   │
└─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘

RESOLVED STATE (solid fill = confirmed):
┌────────────────────────────────────────┐
│ [ICON]  [CONFIRMED_TITLE]               │
│ [CONFIRMED_DETAIL_1]                    │
│ [CONFIRMED_DETAIL_2]                    │
│ [CONFIRM_CTA]          [REMOVE_CTA]    │
└────────────────────────────────────────┘

Show TWO data sets + 5 states for Example A:

── EXAMPLE A: Social obligation buffer ──
  "🎁 Family Obligation Buffer (Optional)"
  "A small separate fund for weddings, ceremonies — so your emergency fund stays untouched."
  CTAs: "Add a buffer" / "Skip — not now"

── EXAMPLE B: Insurance rider ──
  "🛡️ Critical Illness Rider (Optional)"
  "Adds ₹10L critical illness cover for ~₹2,400/year. Covers the gap employer insurance doesn't."
  CTAs: "Add this rider" / "Skip for now"

5 STATES for Example A:
  STATE 1 — PROMPT: Ghost border card. Below preceding confirmed card in stream.
  STATE 2a — ACCEPTED (input needed): Input components render inline in card. Ghost border persists.
  STATE 2b — ACCEPTED (no input): Transitions directly to STATE 3.
  STATE 3 — RESOLVED (BE RESOLVE event arrives):
    Ghost border removed. Card fill → surfaceContainer. Content replaces:
    "🎁 Family Obligation Buffer" / "₹60,000 / year" / "Based on: ₹30K–50K events × 1–2/year"
    CTAs → "Looks right ✓" / "Remove buffer" (text)
  STATE 4 — CONFIRMED: Card fades to compact one-liner: "🎁 Buffer: ₹60K/yr — separate from EF"
  STATE 5 — DECLINED: Card fades out entirely (200ms easeOut).

INVARIANTS:
- Prompt state: ghost border (outlineVariant 15%), surfaceContainerLow fill.
  ONLY component with visible border — signals optional/unresolved.
- Resolved state: border removed, surfaceContainer fill. Visual shift = resolution.
- Ghost → solid transition: 200ms easeInOut
- TWO-EVENT FLOW: Flash emits prompt (initial). BE sends RESOLVE event (confirmed data).
  Flash CANNOT pre-populate confirmedState — it depends on computation not yet run.
- inputConfig: specifies which input palette component renders inline if requiresInput = true.
- Decline: always lower visual weight than accept (text vs filled button)
- Always placed immediately below the parent card it extends
- Agent may DEFER if user emotional state is Anxious
```

---

## Display State Matrix

| Component | State | Trigger | Blocks flow? | User effort |
|---|---|---|---|---|
| `resultReveal` | Materialise → Review → (Row expand) → Confirmed | Spring-in → CTA tap | **Yes** (phase-gate) | 1 tap |
| `inlineInsight` | Materialise → Resting | Spring-in | **No** | 0 (read-only) |
| `allocationBar` | Materialise → Review → Confirmed | Spring-in → CTA tap | **Yes** (lighter) | 1 tap |
| `milestonePlan` | Materialise → Review → (Inline edit) → Confirmed | Spring-in → CTA tap | **Yes** (phase-gate) | 1 tap |
| `actionChecklist` | Materialise → Resting → Dismissed | Spring-in → Dismiss tap | **No** | 0 |
| `contextualFootnote` | Renders inline | Always visible below parent | **No** | 0 |
| `conditionalPrompt` | Prompt → Accept → (Input) → RESOLVE → Confirmed | Accept CTA → BE event | Soft (optional) | 1–2 taps |
| `conditionalPrompt` (decline) | Prompt → Declined | Decline CTA | — | 1 tap |

---

## Display Data Contracts

> **LLM populates:** `icon`, `title`, `ctaLabel`, `secondaryCtaLabel`, `secondaryCtaAction` — framing fields.
> **BE populates:** all computed values (`headline`, `attribution.factors`, `layers`, `milestones`, `steps`, `rows`) after DCE runs.
> **BE injects:** `componentKey`. FE callbacks: `{componentKey, action, payload}`.

```json
// ─────────────────────────────────────────────────────
// resultReveal
// ─────────────────────────────────────────────────────
{ "componentId": "resultReveal",
  "data": {
    "icon": "string? — emoji  [LLM sets]",
    "title": "string — card header  [LLM sets]",
    "headline": {
      "value": "string — formatted result (e.g., ₹4.2L, 72%)",
      "subtitle": "string — context label"
    },
    "attribution": {
      "header": "string — e.g., Why this number",
      "factors": [
        { "label": "string", "description": "string",
          "impact": "string — e.g., +1.5 months",
          "sentiment": "POSITIVE | NEGATIVE | NEUTRAL",
          "detail": "string? — Level 2 explanation",
          "formula": "string? — Level 3 formula + source" }
      ],
      "collapsedCount": "number? — show top N, collapse rest"
    },
    "ctaLabel": "string  [LLM sets]",
    "secondaryCtaLabel": "string?  [LLM sets]",
    "secondaryCtaAction": "ADJUST | DETAILS  [LLM sets]"
  }
}
// FE callbacks:
//   Primary CTA:   {componentKey, action: "CONFIRM"}
//   Secondary ADJUST: {componentKey, action: "ADJUST"} → BE re-triggers edit flow
//   Secondary DETAILS: {componentKey, action: "DETAILS"} → AI elaborates in stream

// ─────────────────────────────────────────────────────
// inlineInsight
// ─────────────────────────────────────────────────────
{ "componentId": "inlineInsight",
  "data": {
    "icon": "string?  [LLM sets]",
    "title": "string  [LLM sets]",
    "subtitle": "string?  [LLM sets]",
    "rows": [
      { "label": "string",
        "impact": "string — formatted value",
        "direction": "POSITIVE | NEGATIVE | NEUTRAL",
        "detail": "string? — expand on tap" }
    ]
  }
}
// rows: nullable / empty array → card renders title + subtitle only (valid minimal state)
// Non-blocking: no CTA. No FE callback (rows have tap-to-expand but no confirm action).

// ─────────────────────────────────────────────────────
// allocationBar
// zones + details merged into layers[] — prevents mismatch
// ─────────────────────────────────────────────────────
{ "componentId": "allocationBar",
  "data": {
    "icon": "string?  [LLM sets]",
    "title": "string  [LLM sets]",
    "layers": [
      { "label": "string — layer/zone name",
        "amount": "number — raw for proportional strip width",
        "displayLabel": "string — formatted strip text (e.g., ₹96K · 1mo)",
        "color": "WARM | NEUTRAL | COOL | ACCENT",
        "icon": "string — emoji for detail row",
        "vehicle": "string — instrument type",
        "description": "string — access context",
        "detail": "string? — expand on tap" }
    ],
    "footnote": { "text": "string" },
    "ctaLabel": "string  [LLM sets]",
    "secondaryCtaLabel": "string?  [LLM sets]"
  }
}
// footnote: nullable. FE renders below card only if present.
// FE callbacks:
//   Primary CTA:    {componentKey, action: "CONFIRM"}
//   Secondary CTA:  {componentKey, action: "DETAILS"} → AI elaborates in stream

// ─────────────────────────────────────────────────────
// milestonePlan
// ─────────────────────────────────────────────────────
{ "componentId": "milestonePlan",
  "data": {
    "icon": "string?  [LLM sets]",
    "title": "string  [LLM sets]",
    "headline": {
      "fieldKey": "string — callback key (e.g., monthlyContribution)",
      "value": "string — formatted (e.g., ₹6,000)",
      "rawValue": "number — for edit seeding (e.g., 6000)",
      "cadence": "string — period label",
      "editable": "boolean",
      "editConfig": { "unit": "string", "min": "number?", "max": "number?", "step": "number?" }
    },
    "milestoneHeader": "string",
    "milestones": [
      { "marker": "HIGHLIGHT | NORMAL", "label": "string",
        "value": "string — formatted amount", "date": "string — MMM YYYY" }
    ],
    "summaryLine": "string?",
    "ctaLabel": "string  [LLM sets]"
  }
}
// FE callbacks:
//   CTA:  {componentKey, action: "CONFIRM"}
//   Edit: {componentKey, action: "EDIT", fieldKey, value: newRawNumber}
//   → BE recomputes milestones → returns updated milestones[] → FE re-renders with morph

// ─────────────────────────────────────────────────────
// actionChecklist
// ─────────────────────────────────────────────────────
{ "componentId": "actionChecklist",
  "data": {
    "icon": "string?  [LLM sets]",
    "title": "string  [LLM sets]",
    "subtitle": "string?",
    "steps": [
      { "number": "number", "icon": "string",
        "label": "string", "value": "string?",
        "instruction": "string", "detail": "string?" }
    ],
    "reminderCta": { "label": "string", "cadence": "string?" },
    "dismissCtaLabel": "string?"
  }
}
// reminderCta: nullable. If absent → dismiss only (or no CTAs).
// FE callbacks:
//   Reminder: {componentKey, action: "REMINDER"}
//   Dismiss:  {componentKey, action: "DISMISS"}

// ─────────────────────────────────────────────────────
// contextualFootnote
// ─────────────────────────────────────────────────────
{ "componentId": "contextualFootnote",
  "data": {
    "icon": "string? — optional emoji prefix  [LLM sets]",
    "text": "string — 1–3 sentences of plain prose  [LLM sets]"
  }
}
// Non-blocking. No CTA. No FE callback.
// Always renders immediately below the preceding uiDirective component.

// ─────────────────────────────────────────────────────
// conditionalPrompt — TWO-EVENT FLOW
// ─────────────────────────────────────────────────────

// EVENT 1: Initial emit (Flash / Pro)
{ "componentId": "conditionalPrompt",
  "data": {
    "icon": "string?  [LLM sets]",
    "title": "string — include '(Optional)' suffix  [LLM sets]",
    "description": "string — what this add-on is  [LLM sets]",
    "acceptLabel": "string  [LLM sets]",
    "declineLabel": "string  [LLM sets]",
    "requiresInput": "boolean",
    "inputConfig": {
      "componentId": "string — from input palette",
      "data": "object — standard input data contract"
    }
  }
}
// inputConfig: nullable. Only when requiresInput = true.

// EVENT 2: Resolve event (from BE, after user input + computation)
{ "componentKey": "string — matched by FE",
  "action": "RESOLVE",
  "confirmedState": {
    "title": "string", "details": ["string"],
    "ctaLabel": "string", "removeLabel": "string"
  }
}
// FE callbacks:
//   Accept:   {componentKey, action: "ACCEPT"} → input or immediate RESOLVE
//   Decline:  {componentKey, action: "DECLINE"} → card fades out
//   Confirm:  {componentKey, action: "CONFIRM"} → resolved state CTA
//   Remove:   {componentKey, action: "REMOVE"} → undo add-on
```

---

# COMPOSITE COMPONENTS

> Composite components combine multiple interaction patterns (view + feedback + confirm) in a single cohesive unit. They are typically populated from a dedicated Pro output object rather than through the generic `data` field.

---

## COMPOSITE PROMPT 1 — planPreviewCard (Plan Preview + Feedback + Approval)

> **Generalisation note:** This component implements a **structured outline with interactive feedback** pattern. While the primary use case is financial plan previews, the same shell supports any phased/grouped content requiring user review before proceeding — e.g., goal summaries, onboarding checklists, scenario outlines. The functional requirements below are written for this general pattern; the Stitch prompt uses plan-specific examples.

### Functional Requirements

**FR-1: Structured viewing**
- Display a header (goal/title + scope description) followed by N grouped sections (phases)
- Each section contains a brief description (1-2 sentences) of what happens in that phase
- User can see the full outline at a glance without scrolling through long descriptions
- Sections render in defined order — the component preserves sequence from data

**FR-2: Simplified Feedback (MVP)**
- No step-level negotiability signals or inline editing.
- User either approves the plan or taps an edit button to provide overall feedback.
- Edit feedback is free-text and sent as a plan-level callback to Pro.

**FR-3: Status awareness**
- Component displays current status: DRAFT (editable, awaiting approval) or APPROVED (locked)
- In DRAFT state: feedback affordances are active, approve CTA is visible
- In APPROVED state: component seals into a compact read-only summary (no feedback affordances)

**FR-4: Approval gate**
- A primary CTA (e.g., "Looks good — let's start") confirms the plan
- Tapping the CTA sends an APPROVE callback → Pro expands into full planObject → execution begins
- This is a **phase-gate**: the journey cannot proceed until the user either approves or provides feedback that results in a re-emission

**FR-5: Re-emission on edits**
- When the user taps the edit button and provides feedback, BE routes it to Pro MODE 2
- Pro processes the feedback and re-emits an updated planPreviewCard
- FE replaces the existing planPreviewCard with the updated version, highlighting what changed
- The cycle repeats until the user approves

### Stitch Prompt

```
Design the "planPreviewCard" component for CarrotFin — a structured plan preview that the user
reviews, optionally edits, and approves before execution begins.

This is a COMPOSITE component: it combines structured display + interactive feedback + 
phase-gate approval. It acts as a contract between the AI planner and the user.

── FUNCTIONAL REQUIREMENTS ──

The component must support these interactions:

1. STRUCTURED VIEWING
   - Header section: goal title + scope note (1–2 sentences explaining what the plan covers)
   - Status badge: "DRAFT" (editable) or "APPROVED" (locked)
   - N PHASE GROUPS, each with a name and a short description
   - User sees the entire outline at a glance

2. PLAN-LEVEL FEEDBACK (MVP)
   - A general "Edit plan" or "Suggest a change" affordance
   - Opens a text input for overall plan feedback
   - Examples: "Make it shorter", "Focus on savings first", "I'm in a hurry"
   - Callback: {componentKey, action: "PLAN_FEEDBACK", feedback: "user text"}

3. APPROVAL GATE
   - Primary CTA: [ctaLabel from data] — e.g., "Looks good — let's start"
   - Only active in DRAFT state
   - Callback: {componentKey, action: "APPROVE"}
   - After approval: component transitions to APPROVED state (compact, read-only)

4. RE-EMISSION FLOW
   - After feedback, BE re-invokes Pro → Pro returns updated planOverview data
   - FE replaces the card with updated content, visually indicating what changed
   - Cycle repeats until user approves

── EXAMPLE A: Emergency Fund plan (2 phases) ──

HEADER:
  Status: [DRAFT]
  Goal: "Build your emergency fund"
  Scope: "We'll figure out how much you need, then create a savings plan you can automate."

PHASE 1: "Understand your situation"
  "We'll review your monthly expenses, dependents, and existing savings to size the fund."

PHASE 2: "Calculate and plan"
  "We'll set your exact target amount and build a monthly savings plan to reach it."

[Suggest a change]                    [Looks good — let's start →]

── EXAMPLE B: Investment Goal plan (3 phases) ──

HEADER:
  Status: [DRAFT]
  Goal: "Save for your Europe trip"
  Scope: "We'll assess how much you need, pick the right instruments, and set up auto-transfers."

PHASE 1: "Assess"
  "We'll pin down your budget, timeline, and risk comfort level."

PHASE 2: "Plan"
  "We'll recommend an investment mix and calculate your required monthly SIP."

PHASE 3: "Execute"
  "We'll help you set up the accounts and automate the transfers."

[Edit plan]                    [This works — let's go →]

── STATES ──

STATE 1 — DRAFT (default):
  Full card visible. "Edit plan" active.
  Approve CTA active. Status badge: "DRAFT"

STATE 2 — FEEDBACK IN PROGRESS:
  User tapped "Edit plan" → plan-level input visible.
  Approve CTA remains active (user can approve without providing feedback).

STATE 3 — UPDATED (after Pro re-emission):
  Card content replaced with updated plan. Changed steps visually indicated
  (e.g., brief highlight, "Updated" label). Status: still DRAFT.

STATE 4 — APPROVED:
  Approve CTA tapped. Status badge transitions to "APPROVED".
  Card compacts into a read-only summary (goal + phase names + step count).
  Feedback affordances removed.

INVARIANTS:
- Phase-gate: journey does not proceed until APPROVE callback
- Phase count and labels from data
- Edit action is always plan-level (no stepIndex in callback)
- Component is a rendering shell — same layout works for any phased/grouped data
- Status badge from data.status (DRAFT | APPROVED)
- All text, labels, CTA text from data — no hardcoded strings
```

---

## Composite State Matrix

| Component | State | Trigger | Blocks flow? | User effort |
|---|---|---|---|---|
| `planPreviewCard` | Draft → (Feedback) → Updated → ... → Approved | CTA tap | **Yes** (phase-gate) | 1 tap (approve) or N taps (feedback cycle) |

---

## Composite Data Contract

> **Pro populates:** the entire `planOverview` object directly — no separate `data` wrapping needed. BE injects `componentKey`.
> **FE renders** from the Pro output object + BE-injected `componentKey`.

```json
// ─────────────────────────────────────────────────────
// planOverview
// Populated directly from Pro's planOverview response object
// ─────────────────────────────────────────────────────
{ "componentId": "planPreviewCard",
  "data": {
    "overviewId": "string — slug, e.g., 'ef-overview-v1'  [Pro sets]",
    "status": "DRAFT | APPROVED  [Pro sets]",
    "goal": "string — what we're planning for  [Pro sets]",
    "scopeNote": "string — what the plan covers and what the user gets at the end. 1–2 sentences.  [Pro sets]",
    "ctaLabel": "string — approve button text  [Pro sets]",
    "editLabel": "string — edit button text ('Suggest a change' or 'Edit plan')  [Pro sets]",
    "phases": [
      {
        "name": "string — phase title  [Pro sets]",
        "description": "string — 1-2 sentences: what happens in this phase  [Pro sets]"
      }
    ]
  }
}
// FE callbacks:
//   Plan feedback:  {componentKey, action: "PLAN_FEEDBACK", feedback: "user text"}
//   Approve:        {componentKey, action: "APPROVE"}
//
// RE-EMISSION: After any feedback callback, BE routes to Pro MODE 2.
//   Pro returns updated planOverview → BE re-emits uiDirective(planPreviewCard).
//   FE replaces the existing card. Changed items get a brief visual indicator.
//
// APPROVAL: After APPROVE callback, Pro expands planOverview into full planObject.
//   BE sends planOverview with status = APPROVED as final render.
//   FE compacts the card into read-only summary.
```

### Generalisation: structuredOutline Pattern

The `planPreviewCard` data contract is an instance of a **structuredOutline** pattern. The same shell supports other grouped/phased data by substituting field semantics:

| planPreviewCard field | General semantics | Other use cases |
|---|---|---|
| `goal` | Outline title | Goal summary title, scenario name, checklist name |
| `scopeNote` | Outline description | Goal health summary, scenario description |
| `phases[]` | Section groups | Goal categories, comparison dimensions, checklist groups |
| `phases[].description` | Section content | Category summary, dimension explanation |
| `ctaLabel` | Confirm action | "Approve", "Confirm", "Start", "Accept" |
| `APPROVE` callback | Proceed signal | Triggers next system state |
| `PLAN_FEEDBACK` | User modification input | Any structured feedback on grouped content |

---

## Unified Decision Guide

### Input — Flash / Pro selects when…

| Need | Component | Rule |
|---|---|---|
| Pick 1 from simple labels | `optionSelector` | 2–6 options, self-explanatory from label alone |
| Single numeric value | `valueInput` | SLIDER if min+max known; TEXT if open-ended |
| Pick 1 from levels with explanation | `tieredSelector` | Options need title + description to understand |
| Classify items into buckets | `interactiveSorter` | 4–10 items, 2–4 labelled categories |
| Multiple related fields at once | `formGroup` | Avoids N separate confirm taps |
| Phase-gate review before computing | `reviewCard` | CONFIRM_ACTION step — multi-field review + edit |
| Free-text answer | — (chat) | No component needed |

### Display — Flash / Pro selects when…

| Need | Component | Rule |
|---|---|---|
| Computed target/score + why breakdown | `resultReveal` | Phase-gate. Has attribution strip. |
| Mid-flow directional signal, non-blocking | `inlineInsight` | No phase-gate. Lighter than resultReveal. |
| Distribution across N categories | `allocationBar` | Has proportional strip + detail rows. |
| Savings plan with editable commitment | `milestonePlan` | Editable headline + dated milestones. |
| Post-plan action items | `actionChecklist` | Non-blocking. Persists on home surface. |
| Regulatory note / contextual disclosure | `contextualFootnote` | Always below a parent component. No CTA. |
| Optional add-on with accept/skip | `conditionalPrompt` | Ghost border = unresolved. Two-event flow. |
| Conversational explanation | — (responseText) | No display component needed |

### Composite — Pro selects when…

| Need | Component | Rule |
|---|---|---|
| Plan preview before execution begins | `planPreviewCard` | Always in Pro MODE 1. Re-emitted on MODE 2 edits. Phase-gate. |

---

## Animation Cadence

```
── INPUT ──
optionSelector:    tap → 200ms fill → 100ms fade others → 200ms collapse → next (300ms total)
valueInput:        [adjust] → tap CTA → 200ms collapse → next
tieredSelector:    tap → 200ms fill → 100ms fade → 200ms collapse → next (300ms total)
interactiveSorter: tap item → 150ms assign animation → progress badge updates →
                   all sorted → 300ms "All sorted ✓" → auto-collapse → summary bubble
formGroup:         fill fields → tap CTA → 200ms card seal → next
reviewCard:        spring-in 300/0.8 → [review] → CTA → 400ms seal → next phase
fieldEditor:       row tap → 250ms sheet up → [edit] → save → 200ms sheet down → 2s "Updated" fade

── DISPLAY ──
resultReveal:      spring-up → headline instant → rows stagger 60ms →
                   [review/expand] → CTA → 400ms seal → next phase
inlineInsight:     softer spring-up (shorter travel) → title+subtitle instant →
                   rows stagger 60ms → resting (no gate)
allocationBar:     spring-up → zones expand L→R 80ms stagger → rows 60ms stagger →
                   footnote 200ms fade → [review] → CTA → 400ms seal
milestonePlan:     spring-up → headline instant → rows 60ms stagger →
                   [review/inline edit: number morph 250/0.85] → CTA → sealed
actionChecklist:   300ms after preceding message → spring-up → steps 60ms stagger →
                   [read/expand] → dismiss → 200ms fade-out
contextualFootnote: fades in 150ms after parent card completes materialisation
conditionalPrompt: spring-up after parent → [prompt] →
                   accept: [input if needed] → BE RESOLVE → 200ms ghost→solid →
                   200ms content crossfade → confirm → compact one-liner
                   decline: 200ms fade-out

── COMPOSITE ──
planPreviewCard:   spring-up → header instant → phases stagger 100ms →
                   CTAs last →
                   [review / feedback] → edits: BE re-emits updated planPreviewCard
                   with crossfade on changed items → approve: 400ms seal
```

---

## Cross-Palette Relationships

| Display component | May trigger input | Callback |
|---|---|---|
| `resultReveal` "Adjust an answer" | Navigates to `reviewCard` for re-editing | `{componentKey, action: "ADJUST"}` → BE re-emits input flow |
| `allocationBar` "Tell me more" | None — AI elaborates in stream | `{componentKey, action: "DETAILS"}` → non-blocking |
| `milestonePlan` inline edit | Uses `valueInput` TEXT logic inline (not separate component) | `{componentKey, action: "EDIT", fieldKey, value}` → BE recomputes milestones[] |
| `actionChecklist` reminder | Triggers OS notification flow (FE-handled) | `{componentKey, action: "REMINDER"}` |
| `conditionalPrompt` accept (with input) | Renders input palette component inline per `inputConfig` | `{componentKey, action: "ACCEPT"}` → input renders → confirms → BE RESOLVE |
| `contextualFootnote` | None | No callback |
| `planPreviewCard` edit | User text routed to Pro MODE 2 | `{componentKey, action: "PLAN_FEEDBACK", feedback}` → Pro re-plans |
| `planPreviewCard` approve | Triggers Pro to expand into full planObject | `{componentKey, action: "APPROVE"}` → Pro MODE 2 approval flow |

---

## Design System Quick Reference (DS01 — Financial Sanctuary)

```
Seed:                  #3CDDC7 (Teal Cyan)
Surface (base):        #0E1321 (surfaceDim)
surfaceContainer:      #1A1F2E (standard card fill)
surfaceContainerLow:   #141928 (conditionalPrompt card fill)
Primary:               #57F1DB (CTAs, accents, tappable elements)
Warm amber:            #EE9800 (positive impacts, momentum, ★ highlight)
Cool slate:            #5A8CA6 (negative impacts, locked/stable)
onSurface:             #DEE2F7 (text — never pure white)
outlineVariant:        #3C4A46 (ghost borders at 15% opacity)
Card radius:           Large (16dp) — all components
Card border:           NONE (tonal elevation only) — except conditionalPrompt ghost border
Card shadow:           Tinted — primary (#3CDDC7) at 6% opacity, 48dp blur
contextualFootnote:    Body Small 12sp, onSurface at 55% opacity. No card, no border.
Font:                  Inter
```

---

*Generated: 2026-06-25 v2 Unified · 7 input + 6 display + 1 composite components (14 total) · Added: planPreviewCard composite with functional requirements*
