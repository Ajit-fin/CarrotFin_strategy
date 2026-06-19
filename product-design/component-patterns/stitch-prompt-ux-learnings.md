# Stitch Prompt Generation — UX/UI Learnings
## Reference for Google Stitch UI prompt generation in any thread

> **Source:** CarrotFin CP06 design session (2026-06-18)
> **Purpose:** Lessons learned from iterating Stitch prompts through UX audits. Read before writing any Stitch prompt for CarrotFin components.
> **Full spec:** [CP06-input-component-palette.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/component-patterns/CP06-input-component-palette.md)

---

## The One Principle

> **Components are rendering shells. Content is data.**

Never bake specific copy, field names, or journey values into a component prompt. A component that works for "Monthly Income" should work equally well for "Monthly Expenses", "SIP Amount", or "Goal Target". When you catch yourself writing "₹60,000" or "Employment Type" inside a component design, stop — that's instance data, not component design.

---

## What Good Stitch Prompts Do

**Show TWO DIFFERENT data examples per component.** This proves the component is truly generic — not secretly designed for one field. A prompt that only shows one example will produce a component that feels hard-coded.

**Show ALL states on one screen.** Don't create separate prompts for Default, Selected, and Collapsed. Show them as a stacked storyboard (STATE 1 → STATE 2 → STATE 3). Stitch produces better layouts when it sees the full state arc.

**Specify INVARIANTS explicitly.** Always close the prompt with a section called "INVARIANTS" or "COMPONENT INVARIANTS" that lists what must stay the same across all instances: dimensions, radii, animation timing, confirm behaviour. Without this, Stitch will invent different rules for each example.

**Demonstrate extensibility.** Two examples side by side — one with 4 options, one with 3. One with icons, one without. One with a total row, one without. This teaches Stitch (and future developers) that the component adapts to its data.

---

## What Kills Stitch Prompt Quality

### ❌ Fake FE capabilities
Don't ask Stitch to show interactions that FE cannot implement. The most common one from this session:

**"Real-time parsing" below a text field** — showing "= ₹1,20,000 per month" updating live as the user types "1.2 lakhs" — does NOT exist on FE. Form fields use numeric keyboards and standard `TextInputFormatter` for locale-aware comma grouping. NLP parsing ("1.2 lakhs" → 1,20,000) only happens in the extraction prompt when users type in chat, never inside a form field.

**Rule:** If the FE cannot build it in a sprint, don't put it in the Stitch prompt.

### ❌ Redundant confirm interactions
Every extra confirmation tap added to an input widget was cut during audit:
- **Slider + ✓ chip:** Cut. Slider already shows the value live. The CTA button IS the confirmation.
- **Text field + confirm chip:** Cut. Same reason.
- **Edit icon on list rows:** Cut. Press-highlight is the affordance. Edit triggers from the tap itself.

**Rule:** One input widget → one confirm action. Input widget + CTA = complete. Never add a chip, icon, or overlay confirm.

### ❌ Non-standard edit patterns
Inline row expansion (accordion) inside a summary card **does not work in practice** on mobile:
- Breaks the card's spatial layout
- Pushes other rows out of view
- Requires custom dimming (60% overlay) that users don't recognise

**Rule:** Use M3 `BottomSheet` for all field edits from a summary/review card. It is universally understood, maintains card context, and is zero custom work.

### ❌ Journey-specific copy baked into component prompts
Prompts that hard-code field labels (D1, D4, D8...) or specific values (₹60K, "Salaried", "Bangalore") produce components that can't be reused. The component only gets field labels from the `data` contract at runtime.

**Rule:** Use `[Field Name]`, `[Value A]`, `[Option 1]` as placeholders inside component prompts. EF-specific copy goes in journey scripts, not component specs.

---

## The 5 Input Components — Cheat Sheet

| Component | When to use | Confirm model | Stitch shorthand |
|---|---|---|---|
| `optionSelector` | Pick 1 from 3–6 self-explanatory labels | **Tap = auto-confirm** | "Horizontal chip row, tap fills primary, collapses to bubble" |
| `valueInput` | Single numeric value | **CTA button** (live-updating text) | "Slider/text + one CTA below, CTA text shows current value" |
| `tieredSelector` | Pick 1 from levelled options needing explanation | **Tap = auto-confirm** | "Vertical card stack with title + description, tap fills, collapses" |
| `formGroup` | Multiple related fields in one go | **Single CTA for all** | "Ledger-style card, label left / field right, optional live total, one CTA" |
| `reviewCard` | Phase-gate review of all collected data | **CTA to proceed**, rows tap → bottom sheet | "Clean rows no edit icons, press-highlight affordance, single CTA" |

**fieldEditor** is FE-internal. Flash never emits it. Stitch should show it as a standalone bottom sheet reference only.

---

## UX Decisions That Survived Audit (Keep These)

**Smart defaults on sliders.** AI pre-positions the thumb based on context. Most users confirm without adjusting = 1 tap. This is a trust signal ("the app already knows something about me") and a friction reducer.

**Skip option in tieredSelector at equal visual weight.** The privacy-safe skip card ("I'd rather not say") must be the same size as tier cards, never smaller or visually de-emphasised. Burying it signals the app is pressuring disclosure.

**No edit icons at rest on reviewCard.** Rows are clean. On press, a background highlight appears — this is the only affordance. Discovered on touch, not decorated at rest. Keeps the review card scannable.

**Single CTA on reviewCard.** No secondary "Let me adjust" button. Tapping a row IS the adjust action. Fewer buttons = lower decision load at a moment where the user is already doing cognitive work (reviewing their data).

**Live total row in formGroup.** When collecting grouped numeric inputs (expense breakdown), a live-updating total gives immediate feedback. Users see their data crystallising as they type. This is the "reward" for filling multiple fields.

---

## Stitch Prompt Structure Template

Use this structure for any CarrotFin input component prompt:

```
[Component name] for CarrotFin — [one-line purpose].

[DESIGN SYSTEM LINE: M3 seed #6B8F71, dark mode, Inter, primary/amber/muted colors]

Show TWO EXAMPLES [side by side / stacked] to prove extensibility:

── EXAMPLE A: [describe data set A] ──
[component rendering with Example A data]
States: DEFAULT → SELECTED/VALUE SET → COLLAPSED/CONFIRMED

── EXAMPLE B: [describe data set B] ──
[component rendering with Example B data]
Same states.

INVARIANTS:
- [dimension, radius, height specs]
- [confirm behavior — tap = auto OR requires CTA]
- [animation timing]
- [what content comes from data vs. what is hardcoded]
- [what FE handles, what is rendered from data contract]
```

---

## Flash + FE Responsibility Split (For Prompt Accuracy)

When writing a Stitch prompt, only ask for things in these two buckets:

**Flash decides → show in prompt:**
- Which component appears
- What labels, options, tiers, and values display
- What the CTA label says

**FE handles → describe as behaviour, not visual state:**
- State transitions (tap → fill → collapse)
- Animation timing
- Value formatting (₹, commas, periods)
- Bottom sheet open/close mechanics
- `TextInputFormatter` for numeric fields

**Never show in a Stitch prompt:**
- NLP parsing displays
- FE-side calculation logic
- Things that require a backend call to render

---

*Last updated: 2026-06-19 · Source thread: 59080ce1 · Distilled from CP06 design session*
