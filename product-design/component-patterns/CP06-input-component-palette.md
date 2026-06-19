# CP06: Input Component Palette

> **Date:** 2026-06-18  
> **Type:** Input · Data Collection  
> **Used on:** Generative / Hybrid screens  
> **M3 base:** `FilterChip` (optionSelector), `Slider`/`TextField` (valueInput), `Card` (tieredSelector, reviewCard), `BottomSheet` (fieldEditor)  
> **Cross-journey:** Yes — these components serve ALL journeys, not just EF  
> **Status:** Complete — extends Flash §4 componentPalette  
> **Supersedes:** CP01 interaction patterns are preserved but CP06 is the Flash-facing palette layer

---

> [!NOTE]
> **Relationship to CP01:** CP01 defines confirmation *interaction patterns* (C1–C4) — the design logic for how confirmations behave. CP06 defines the *generic input components* Flash selects from. CP01's patterns map onto CP06 components:
> - C1 (Light Confirm) → `optionSelector` + `tieredSelector`
> - C2 (Active Confirm) → `valueInput`
> - C3 (Summary Confirm) → `reviewCard`
> - C4 (Edit-in-Place) → `fieldEditor` (FE-internal, triggered by `reviewCard`)

---

## 0. Architecture

### 0.1 How the System Works

```
Flash prompt receives: user message + context + planObject
Flash decides:         "I need [type] of input for [fieldKey]"
Flash emits:           componentId + data payload in uiDirective

BE passes:             componentId + data to FE

FE renders:            the component using data contract
FE handles:            state transitions, animations, formatting, edit mechanics
FE calls back:         confirmed value to BE on completion
```

### 0.2 Separation of Concerns

| Concern | Flash decides | FE handles |
|:---|:---|:---|
| Which component type | ✓ (`componentId`) | — |
| What data to display | ✓ (`data` payload) | — |
| When to show it | ✓ (per-turn `uiDirective`) | — |
| State transitions | — | ✓ (default → selected → collapsed → callback) |
| Animations | — | ✓ (spring params per DS01 §6) |
| Edit mechanics | — | ✓ (bottom sheet, field type routing) |
| Value formatting | — | ✓ (₹ notation, period labels, Indian commas) |
| Confirm behavior | — | ✓ (auto-confirm vs CTA-confirm per component type) |

### 0.3 Flash Decision Guide

| Flash needs… | Emit | Why not the others |
|:---|:---|:---|
| Pick from 3–6 self-explanatory labels | `optionSelector` | Options need no description |
| A numeric/currency value | `valueInput` | Slider or text entry, not a selection |
| Pick from levels that need explanation | `tieredSelector` | Options need title + description |
| Confirm all collected data at phase gate | `reviewCard` | Multi-field review + edit access |
| A free-text response | — (chat `responseText`) | No input component needed |

---

## 1. optionSelector

### Purpose

Single-select from a set of mutually exclusive categorical options. User taps one → auto-confirms → collapses to answer bubble → flow proceeds. The lightest input interaction.

### When Flash Uses This

- Fixed categorical choices (3–6 options)
- Options are self-explanatory from label alone (no description needed)
- Examples: employment type, city tier, yes/no, age bracket, household structure

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Chip row | `Wrap` of `FilterChip` | Minimal — add ✓ selected state, primary fill |
| Selection feedback | Chip state change (M3 built-in) | None |
| Collapse to bubble | Custom animation | Yes — 200ms fade + replace with `Container` bubble |

### Data Contract (what Flash emits)

```json
{
  "componentId": "optionSelector",
  "data": {
    "fieldKey": "string — field identifier for BE storage",
    "options": [
      {
        "label": "string — display text",
        "value": "string — stored value",
        "icon": "string? — optional emoji or icon key"
      }
    ]
  }
}
```

### States (FE-managed)

| State | Visual | Trigger | Duration |
|:---|:---|:---|:---|
| DEFAULT | All chips outlined, unselected. 8dp radius, 40dp height. Icon (if present) + label. | Flash emits component | — |
| SELECTED | Tapped chip fills primary + trailing ✓. Others fade to 30% opacity. | User taps a chip | 200ms |
| COLLAPSED | Chips removed. Right-aligned answer bubble shows selected label. | Auto after SELECTED | 200ms fade + bubble appear |

### Interaction

| Action | Behavior |
|:---|:---|
| Tap chip | Select → auto-confirm → collapse → BE callback with `{fieldKey, value}` |
| Tap again (before collapse) | Deselect, return to DEFAULT |
| No separate confirm button | The tap IS the confirmation |

### Adaptive Behavior

| Condition | Rendering |
|:---|:---|
| 3–4 options | Single row, horizontal |
| 5–6 options | Wrap to two rows |
| >6 options | Not recommended — use `tieredSelector` or restructure |
| Icons present in data | Show icon + label |
| Icons absent | Text-only chips |
| Low literacy (1–2) | Larger chips (48dp height), simplified labels |

### Composition Rules

- Appears inline in chat stream, below AI message bubble
- One chip set per turn (never stacked)
- Chips positioned in bottom 60% of viewport (thumb zone)
- After collapse, chip set is replaced by answer bubble — never re-shown

---

## 2. valueInput

### Purpose

Collect a single numeric or currency value via slider or standard text field. Flow blocks until user confirms via a single CTA button. The CTA shows the current value and updates live (slider) or on input (text).

### When Flash Uses This

- A single currency amount (income, savings, contribution)
- A single numeric value (percentage, count, months)
- Data where precision matters and affects downstream calculations
- Examples: monthly income, total fixed obligations, savings amount, SIP amount
- **Not for multiple related fields** — use `formGroup` instead (§4)

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Slider | `Slider` with `divisions` | Minimal — custom tick labels, primary thumb |
| Text field | `TextField` with `InputDecoration` | Minimal — prefix/suffix, `inputFormatters` for Indian comma grouping |
| Live value display | `Text` with `AnimatedSwitcher` | Minimal — number morph animation (slider mode only) |
| CTA button | `FilledButton` | Minimal — dynamic label text |

### Data Contract (what Flash emits)

```json
{
  "componentId": "valueInput",
  "data": {
    "fieldKey": "string",
    "inputType": "SLIDER | TEXT",
    "config": {
      "unit": "string? — e.g., '₹', '%'",
      "period": "string? — e.g., 'MONTHLY', 'YEARLY', null",
      "min": "number? — slider only",
      "max": "number? — slider only",
      "step": "number? — slider only",
      "smartDefault": "number? — slider pre-position. If null, centered.",
      "placeholder": "string? — text mode only, e.g., 'e.g., 120000'"
    }
  }
}
```

### States (FE-managed)

| State | Visual | Trigger | Duration |
|:---|:---|:---|:---|
| AWAITING | Input widget visible. CTA disabled (30% opacity) for TEXT; shows smart default value for SLIDER. | Flash emits component | — |
| VALUE_SET | Slider: live value above slider, CTA text = `"[value] — that's right ✓"`. Text: field auto-formats input (120000 → 1,20,000), CTA text = `"[value] — continue →"`. | User interacts with input | Real-time (slider) / on input (text) |
| CONFIRMED | Input + CTA collapse to answer bubble. | User taps CTA | 200ms |

### Interaction

| Action | Behavior |
|:---|:---|
| Drag slider | Live value updates above slider. CTA text updates. |
| Type in text field | Numeric keyboard. FE auto-formats with Indian comma grouping as user types (standard `TextInputFormatter`). CTA enables when valid number entered. |
| Tap CTA | Confirm → collapse → BE callback with `{fieldKey, rawValue, formattedValue}` |
| No ✓/✏ chip | CTA is the ONLY confirm action. No separate confirm chip. |

> [!IMPORTANT]
> **No FE parsing of natural language numbers.** TEXT mode uses a standard numeric input with numeric keyboard. The field auto-formats using Indian comma grouping (`TextInputFormatter`) — this is locale formatting, not NLP parsing. Free-text expressions like "1.2 lakhs" are parsed by the extraction prompt when users type in chat, not by this component.

### Key UX Decisions

- **Smart default (slider):** AI pre-positions thumb based on context (e.g., income + city → estimated expenses). Most users confirm without adjusting = **1 tap**.
- **Standard numeric input (text):** Numeric keyboard, auto-format on input. No "real-time parsing" — the text field itself shows formatted value via `TextInputFormatter`.
- **No redundant confirm chip:** The CTA button IS the confirmation. Slider/text field are self-sufficient input widgets — adding a separate chip over them adds a tap with zero information gain.

### Adaptive Behavior

| Condition | Rendering |
|:---|:---|
| Currency input | ₹ prefix, Indian comma notation (₹1,20,000), period suffix |
| Percentage input | % suffix, 0–100 range |
| Smart default available | Slider pre-positioned, CTA shows default value immediately |
| No smart default | Slider centered, CTA disabled until user adjusts |
| Low literacy (1–2) | Larger thumb (28dp), fewer ticks, simplified labels |

### Composition Rules

- Appears inline in chat stream, below AI message
- Input + CTA in bottom 60% of viewport (thumb zone)
- Flow-blocking: nothing renders below until CTA tapped
- Only one valueInput active at a time

---

## 3. tieredSelector

> **Note on section numbering:** §4 (formGroup) was added 2026-06-18. Subsequent sections renumbered.

### Purpose

Single-select from ordinal/leveled options where each option needs a title AND a brief description for the user to make an informed choice. Tapping a card auto-confirms.

### When Flash Uses This

- Options represent levels/tiers (low → medium → high)
- Options are NOT self-explanatory from a label alone — need context
- Sensitive topics where a tier abstracts away specifics (privacy-by-design)
- Examples: health risk level, risk tolerance, experience level, urgency level, comfort with volatility

### Key UX Decision: Privacy-by-Design

For sensitive inputs (health, debt distress, etc.), `tieredSelector` is preferred over `valueInput` (text). The user picks a tier — the app never collects the underlying specifics. The AI gets sufficient signal for its calculations from the tier level.

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Tier card | `Card` with `ListTile` (leading icon, title, subtitle) | Minimal — add selection state |
| Card stack | `Column` with `SizedBox` gap | None |
| Skip option card | Same as tier card | None — identical visual weight |
| Selection feedback | Card fill + others fade (M3 built-in tint) | Minimal |

### Data Contract (what Flash emits)

```json
{
  "componentId": "tieredSelector",
  "data": {
    "fieldKey": "string",
    "tiers": [
      {
        "label": "string — tier title",
        "description": "string — one-line explanation",
        "value": "string — stored value",
        "icon": "string? — emoji or icon, or auto-assigned by ordinal"
      }
    ],
    "skipOption": {
      "label": "string — skip title",
      "description": "string — what happens if skipped",
      "value": "string — stored value (e.g., 'skip')"
    }
  }
}
```

> `skipOption` is optional. When present, rendered as a card with identical size and visual weight — never smaller or visually deprioritized.

### States (FE-managed)

| State | Visual | Trigger | Duration |
|:---|:---|:---|:---|
| DEFAULT | All cards outlined, equal prominence. 12dp radius, ~64dp min-height, 8dp gap. Tier dot + title + description. | Flash emits component | — |
| SELECTED | Tapped card fills primary. Others fade to 30%. | User taps a card | 200ms |
| COLLAPSED | Cards removed. Answer bubble shows selected tier label. | Auto after SELECTED | 200ms + bubble |

### Interaction

| Action | Behavior |
|:---|:---|
| Tap card | Select → auto-confirm → collapse → BE callback with `{fieldKey, value}` |
| No separate confirm button | Same as `optionSelector` — tap IS confirm |

### Adaptive Behavior

| Condition | Rendering |
|:---|:---|
| 2 tiers | 2 cards, no skip |
| 3–4 tiers + skip | 4–5 cards, vertical stack |
| >5 tiers | Not recommended — restructure into groups or use `optionSelector` |
| Tier dots | Color auto-assigned by ordinal (green → amber → deeper amber) OR from data icon |
| Skip option present | Rendered with 🔒 or muted icon, same card size as tiers |
| Low literacy (1–2) | Larger cards (72dp), simpler description text |

### Composition Rules

- Appears inline in chat stream, below AI message
- Vertical card stack — never horizontal
- Cards in bottom portion of viewport
- After collapse, cards replaced by answer bubble

---

## 4. formGroup

### Purpose

Collect multiple related input fields in a single form card with ONE confirm CTA. Avoids requiring separate confirm interactions for each individual field.

### When Flash Uses This

- Multiple related numeric values needed together (expense breakdown, income sources)
- Mixed-type grouped inputs (e.g., city + rent amount, asset type + value)
- Any case where asking each field individually would be too slow
- Examples: monthly expense categories, income source breakdown, dependent details, goal parameters

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Form card | `Card` with `Column` | None |
| Input rows | `Row` with `Text` label + `TextField` or compact selector | Minimal — compact field sizing |
| Numeric fields | `TextField` with `InputDecoration` + `inputFormatters` | Minimal — ₹ prefix, Indian formatting |
| Categorical fields | `DropdownButton` or compact `ChoiceChip` | Minimal — inline sizing |
| Total row | `Row` with `Text` label + computed `Text` value | Yes — live sum calculation |
| CTA button | `FilledButton` | None |

### Data Contract (what Flash emits)

```json
{
  "componentId": "formGroup",
  "data": {
    "title": "string — card header (e.g., 'Monthly Expenses')",
    "ctaLabel": "string — confirm button text (e.g., 'Continue')",
    "showTotal": "boolean — whether to show auto-calculated total row",
    "totalLabel": "string? — label for total row (e.g., 'Total monthly expenses')",
    "fields": [
      {
        "fieldKey": "string — field identifier",
        "label": "string — display label",
        "inputType": "NUMERIC | CATEGORICAL",
        "required": "boolean — whether field must be filled before CTA enables",
        "config": {
          "unit": "string? — e.g., '₹'",
          "period": "string? — e.g., 'MONTHLY'",
          "placeholder": "string? — e.g., '0'",
          "options": "array? — for CATEGORICAL: [{label, value}]"
        }
      }
    ]
  }
}
```

### Visual Design

| Property | Value |
|:---|:---|
| Shape | Large (16dp radius) — DS01 §3 |
| Elevation | Level 3 (6dp) — DS01 §4 |
| Title | Title Large (22sp/400) |
| Row height | 48dp |
| Row layout | Label left (14sp, 60% opacity) + compact input field right (120dp width, 40dp height) |
| Divider | Subtle, `outlineVariant` at 10% opacity between rows |
| Total row | Visually distinct: bold label + bold value in primary color. No input field. |
| CTA | FilledButton, primary, full card width, 48dp height |

### States (FE-managed)

| State | Visual | Trigger |
|:---|:---|:---|
| FILLING | Card with empty/default fields. CTA disabled if any required field is empty. Total shows sum of entered values (live). | Flash emits component |
| READY | All required fields filled. CTA enabled. Total row shows final sum. | User fills required fields |
| CONFIRMED | Card seals. All values committed. | User taps CTA |

### Interaction

| Action | Behavior |
|:---|:---|
| Tap a numeric field | Numeric keyboard opens for that field. Auto-format on input. |
| Tap a categorical field | Compact dropdown or inline chip selection. |
| Total row | Read-only, auto-calculated live as fields change. |
| Tap CTA | Confirm all → BE callback with `{fields: [{fieldKey, value}, ...]}` |
| Tab/next field | Standard field-to-field navigation. |

### Key UX Decisions

- **ONE CTA for all fields:** No per-field confirm chips or buttons. User fills N fields, taps one button.
- **Live total:** When `showTotal: true`, the total row updates as fields are filled — gives immediate feedback.
- **Compact inputs:** Fields are right-aligned, ledger-style. Labels left, inputs right. Clean, scannable.
- **Optional fields:** Unfilled optional fields default to 0 or empty. Only required fields gate the CTA.

### Adaptive Behavior

| Condition | Rendering |
|:---|:---|
| 2–6 fields | All visible in card, no scroll |
| >6 fields | Card becomes scrollable internally |
| Mixed types (numeric + categorical) | Categorical renders as compact dropdown in the row |
| Low literacy (1–2) | Larger fields, fewer fields per card, simplified labels |

### Composition Rules

- Appears inline in chat stream, below AI message
- Card takes ~50–70% of viewport depending on field count
- Flow-blocking: AI waits for CTA
- Only one formGroup active at a time

---

## 5. reviewCard

### Purpose

Phase-gate summary that displays all previously-collected fields for review before proceeding. Single CTA to confirm. Rows are tappable to trigger `fieldEditor` (bottom sheet) for corrections.

### When Flash Uses This

- Phase boundaries (end of assessment, end of setup)
- Any moment where the user should see and approve collected data before computation
- Examples: EF assessment summary, goal setup review, portfolio input review

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Card container | `Card` with `Padding` + `Column` | None |
| Data rows | `ListTile` (leading label, trailing value) | Minimal — add press highlight |
| Dividers | `Divider` | None |
| CTA | `FilledButton` | None |
| Bottom sheet | `showModalBottomSheet` (M3) | None — standard M3 pattern |

### Data Contract (what Flash emits)

```json
{
  "componentId": "reviewCard",
  "data": {
    "title": "string — card header (e.g., 'Your Profile', 'Goal Summary')",
    "ctaLabel": "string — confirm button text (e.g., 'Looks right ✓')",
    "rows": [
      {
        "fieldKey": "string — field identifier",
        "label": "string — display label",
        "displayValue": "string — formatted value to show",
        "editable": "boolean — whether row triggers editor on tap",
        "fieldType": "CATEGORICAL | NUMERIC | TIERED — determines editor widget",
        "editConfig": "object? — options[] or slider config or tiers[], for the editor"
      }
    ]
  }
}
```

> `editConfig` provides the field editor with enough context to render the correct input widget. For CATEGORICAL: `{options: [...]}`. For NUMERIC: `{inputType, min, max, step, ...}`. For TIERED: `{tiers: [...], skipOption?}`.

### Visual Design

| Property | Value |
|:---|:---|
| Shape | Large (16dp radius) — DS01 §3 |
| Elevation | Level 3 (6dp) — DS01 §4 |
| Title | Title Large (22sp/400) |
| Row label | Body Medium (14sp/400), 60% opacity, left |
| Row value | Body Medium (14sp/400), white, right |
| Row height | 48dp |
| Dividers | `outlineVariant` at 10% opacity |
| CTA | FilledButton, primary, full card width, 48dp height |

### States (FE-managed)

| State | Visual | Trigger |
|:---|:---|:---|
| REVIEW | Clean card: title + rows + CTA. No edit icons at rest. Rows show press-highlight (primaryContainer 8%) on touch. | Flash emits component |
| EDITING | Scrim overlay (40% black). Bottom sheet open with field editor. | User taps an editable row |
| ROW_UPDATED | Sheet dismissed. Row shows new value + "Updated" label (primary color, 2s fade). | User saves in editor |
| CONFIRMED | Card seals (400ms animation). Flow proceeds to next phase. | User taps CTA |

### Key UX Decisions

- **No edit icons at rest:** Rows are clean. The press-highlight on touch is the only affordance — discoverable on interaction, not cluttering the resting state.
- **Single CTA:** No "Let me adjust" secondary button. Tapping a row IS the adjust action. One button reduces decision load.
- **Bottom sheet for edits:** Standard M3 `BottomSheet` — universally understood, doesn't disrupt card layout. NOT inline expansion (which breaks spatial layout on mobile and requires custom dimming).

### Composition Rules

- Appears inline in chat stream (not a modal)
- Card takes ~65% of viewport
- Only one reviewCard active at a time
- Previous reviewCards (from earlier gates) collapse/fade
- Flow-blocking: AI waits for CTA tap

---

## 6. fieldEditor (FE-Internal)

### Purpose

Bottom sheet for editing a single field from `reviewCard`. Renders the appropriate input widget based on `fieldType`. **Flash never emits this component** — it is triggered by FE when the user taps a `reviewCard` row.

### Flutter Widget Mapping

| Sub-element | M3 Widget | Custom? |
|:---|:---|:---|
| Bottom sheet | `showModalBottomSheet` | None — standard M3 |
| Drag handle | M3 built-in | None |
| Field label | `Text` (Title Medium) | None |
| Current value | `Text` (Body Medium, muted) | None |
| Input widget | Reuses `optionSelector` chips / `valueInput` slider+field / `tieredSelector` cards | None — same widgets |
| Save CTA | `FilledButton` (numeric only) | None |

### Behavior by Field Type

| fieldType | Widget rendered | Confirm behavior |
|:---|:---|:---|
| CATEGORICAL | Chip set (same as `optionSelector`) | Tap chip → auto-save → auto-dismiss |
| NUMERIC | Slider or text field (same as `valueInput`) | Explicit "Save" CTA |
| TIERED | Tier cards (same as `tieredSelector`, compact: 56dp) | Tap card → auto-save → auto-dismiss |

### Sheet Structure (all types)

```
┌─────────────────────────────────────┐
│           ── drag handle ──         │  ← 4dp × 32dp, muted, centered
│                                     │
│  Field Label               18sp bold│
│  Currently: [value]      14sp muted │
│  ─────────────────────────────────  │  ← divider
│                                     │
│  [input widget based on fieldType]  │
│                                     │
│  [Save CTA — numeric only]         │
└─────────────────────────────────────┘
```

### Dismiss Behavior

| Action | Result |
|:---|:---|
| Save/select | Value saved → sheet dismisses → `reviewCard` row updates |
| Swipe down | Cancel — no changes saved |
| Tap scrim | Cancel — no changes saved |

### Sheet Sizing

| Content | Peek height |
|:---|:---|
| Categorical (chips) | ~200dp |
| Numeric (slider) | ~280dp |
| Tiered (cards) | ~320dp |

---

## 7. Cross-Component Rules

### Confirm Escalation (from CP01)

The general principle: simpler inputs get lighter confirmation.

| Input type | Confirm pattern | Friction level |
|:---|:---|:---|
| Categorical (optionSelector) | Auto-confirm on tap | Zero — lightest |
| Tiered (tieredSelector) | Auto-confirm on tap | Zero — lightest |
| Single numeric (valueInput) | CTA button required | Low — one explicit tap |
| Grouped inputs (formGroup) | Single CTA for all fields | Low — one explicit tap for N fields |
| Phase gate (reviewCard) | CTA button required | Medium — deliberate review |
| Correction (fieldEditor) | Auto or CTA by type | Matches original input type |

### Flow Control

- `optionSelector` and `tieredSelector` are **non-blocking** — tap auto-proceeds.
- `valueInput`, `formGroup`, and `reviewCard` are **flow-blocking** — AI waits for CTA.
- `fieldEditor` is **blocking within review** — reviewCard CTA disabled while editor is open.

### Animation Cadence (DS01 §6)

```
optionSelector:  tap → 200ms chip fill → 100ms others fade → 200ms collapse → next turn (300ms total)
valueInput:      [adjust] → tap CTA → 200ms collapse → next turn
tieredSelector:  tap → 200ms card fill → 100ms others fade → 200ms collapse → next turn (300ms total)
formGroup:       fill fields → tap CTA → 200ms card seal → next turn
reviewCard:      card materialise (spring 300/0.8) → [review] → CTA tap → 400ms seal → phase transition
fieldEditor:     row tap → 250ms sheet up → [edit] → save → 200ms sheet down → row update → 2s "Updated" fade
```

---

## 8. Palette Description for Flash §4

Add these entries to the `componentPalette` in [flash_conversation_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/flash_conversation_v1.xml#L118-L133):

```
EXISTING PALETTE (content/visualization):
  emergencyFundTarget       EF target with risk-level breakdown
  sipProjectionChart        SIP growth line chart over time
  goalGapAnalysis           Current savings vs goal target bar
  ...
  goalCascadeCard           Resource reallocation proposal

INPUT COMPONENTS (new — CP06):
  optionSelector            Single-select from categorical options (3–6 chips).
                            data: { fieldKey, options: [{label, value, icon?}] }

  valueInput                Single numeric/currency entry via slider or standard text field.
                            data: { fieldKey, inputType: SLIDER|TEXT,
                                    config: {unit?, period?, min?, max?,
                                             step?, smartDefault?, placeholder?} }

  tieredSelector            Ordinal selection from descriptive tier cards (2–5 levels).
                            data: { fieldKey, tiers: [{label, description, value, icon?}],
                                    skipOption?: {label, description, value} }

  formGroup                 Multiple related fields in one card with single confirm CTA.
                            data: { title, ctaLabel, showTotal?, totalLabel?,
                                    fields: [{fieldKey, label, inputType: NUMERIC|CATEGORICAL,
                                              required, config: {unit?, period?, options?}}] }

  reviewCard                Phase-gate summary with scannable rows and single confirm CTA.
                            data: { title, ctaLabel, rows: [{fieldKey, label,
                                    displayValue, editable, fieldType, editConfig?}] }
```

---

## 9. MVP Scope Boundaries

### In scope

- All 5 input components + fieldEditor
- Tap interaction only
- Smart defaults on sliders
- Standard numeric input (no FE parsing of free-text numbers)
- Bottom sheet editing
- Single-select options
- Grouped multi-field forms

### Deferred (not MVP)

| Feature | Component affected | Revisit when |
|:---|:---|:---|
| Voice confirmation | All | Voice mode launches |
| Multi-select options | optionSelector | Journey needs "select all that apply" |
| Cluster gates (mid-phase summaries) | reviewCard | User testing shows assessment feels too long |
| Qualifier badges (~approx, range) | valueInput | Users report confusion with imprecise inputs |
| Inline row expansion (original C4) | reviewCard/fieldEditor | Bottom sheet proves insufficient |
| FE natural language number parsing | valueInput | FE team builds a parser; currently extraction-prompt territory |

---

*Last updated: 2026-06-18. Cross-journey spec — used by all journeys, not just EF.*
