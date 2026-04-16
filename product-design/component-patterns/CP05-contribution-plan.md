# CP05: Contribution Plan Card + Action Card

> **Type:** Card (phase-gate, multi-component) + Card (persistent, post-confirmation)  
> **Used on:** Hybrid (Phase 5 only — Contribution Plan Card inline in stream; Action Card persists to home surface)  
> **M3 base:** `Card` (elevated), `ListTile` (milestone rows, action steps), `FilledButton` / `TextButton` (CTAs), `InputChip` (§C2 contribution confirm)  
> **Date:** 2026-04-16  
> **Status:** Complete

---

> [!NOTE]
> **Layout framing:** Structural layouts in this spec are illustrative compositions, not canonical pixel-perfect requirements. The composition agent may vary layout within the defined adaptive states. Only the adaptive state definitions, visual design tokens (DS01), and interaction affordances are binding constraints.

---

## Purpose

Phase 5 has two anchor cards with distinct lifecycles:

1. **Contribution Plan Card** — Phase-gate card. Shows the agreed monthly contribution, 4-milestone timeline with dates, and the self-EMI label. Anchors the "how do I get there?" conversation. Flow-blocking: the user must tap "Confirm my plan →" to proceed.

2. **Action Card (DD09)** — Post-confirmation persistent card. Fires immediately after plan confirmation. Three numbered setup steps (one per allocation layer, priority-ordered). Saved to the EF goal card on the home surface — accessible without re-entering the conversation.

Supporting elements: Salary-Day Reminder (opt-in commitment device), Exit Confirmation Message (§1A verbatim-locked copy), EF Goal Card (home surface static card).

> **LLM agentic layer note:** The composition agent controls the conversational framing ("self-EMI"), the contribution amount negotiation, and the pacing of the reveal. The milestone date computation is DETERMINISTIC (computation agent). The ★ Starter Shield row must visually match CP03/Phase 3 — the agent references it, does not re-introduce it. Exit copy is verbatim-locked — the agent has zero discretion on the §1A phrasing.

---

## Flutter Widget Mapping

| Sub-element | M3 Widget | Custom work needed? |
|:---|:---|:---|
| Contribution Plan Card container | `Card` (M3) — `surfaceContainer`, Large radius 16dp, elevation Level 3 | None |
| Card title "Your Contribution Plan" | `Text` with Title Large (22sp/400) | None |
| Card icon 📅 | `Text` (emoji) | None |
| Contribution headline (₹ amount + cadence) | `Text` with Display Medium (45sp/400) for ₹ amount; Title Medium (16sp/500) for "/ month · on your salary date" | None |
| Contribution amount tap target | `InkWell` wrapping the ₹ text — triggers §C4 | Minimal — tap highlight |
| Milestone sub-header | `Text` "Your milestone path" + `Divider` | Minimal — styled divider |
| Milestone row (each) | `ListTile` — leading: ★ or `·` marker, title: label + ₹ amount, trailing: date | Minimal — ★ row gets warm glow wrap |
| ★ Starter Shield warm glow | `Container` with `warmPositive` background at ~15% opacity | Minimal — same as CP03 |
| Self-EMI label | `Text` with Body Medium (14sp/400) | None — inline summary text |
| CTA "Confirm my plan →" | `FilledButton` (M3) — `primary`, full-width | None |
| Number morph (contribution edit) | `TweenAnimationBuilder<double>` with spring curve | **Yes — same as CP02, CP03** |
| Card materialisation | `AnimatedSlide` + `AnimatedOpacity` | Minimal — spring stiffness 300, damping 0.8 |
| Milestone row stagger | `AnimatedList` or manual stagger controller with 60ms delay | Minimal |
| Action Card container | `Card` (M3) — `surfaceContainer`, Large radius 16dp, elevation Level 3 | None |
| Action Card title "Your Action Plan" | `Text` with Title Large (22sp/400) | None |
| Action Card icon ✅ | `Text` (emoji) | None |
| Action step row (each) | `ListTile` — leading: numbered badge, title: layer label + ₹ amount, subtitle: instruction text | Minimal — numbered badge is `CircleAvatar` with `primary` fill |
| Action step layer icon | ⚡ / ⏱ / 🔒 (emoji, inline with label) | None |
| Reminder CTA "Remind me on salary day" | `OutlinedButton` (M3) — `secondary` | None |
| Dismiss CTA | `TextButton` (M3) | None |
| Exit Confirmation Message | `Text` within AI message bubble — standard stream rendering | None |

---

## Structural Layouts

### Contribution Plan Card

```
┌────────────────────────────────────────────────────────────┐
│  📅  Your Contribution Plan                                 │
│                                                            │
│  ₹6,000 / month  ·  on your salary date                   │
│  ↑ tap to edit                                             │
│                                                            │
│  ── Your milestone path ────────────────────────────────── │
│                                                            │
│  ★ Starter Shield  ₹96K   →  Aug 2026                     │
│    3 Months Secure ₹2.9L  →  Oct 2027                     │
│    Half-Year Shield ₹5.8L →  Oct 2028                     │
│    Fully Funded    ₹11.5L →  Apr 2031                     │
│                                                            │
│  Your self-EMI: ₹6,000 · every salary day · auto-transfer │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                  Confirm my plan  →                   │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

### Action Card (DD09)

```
┌────────────────────────────────────────────────────────────┐
│  ✅  Your Action Plan                                       │
│  Set these up on your next salary date.                    │
│                                                            │
│  ① ⚡ Instant Access layer — ₹[X]                          │
│     Set up an auto-transfer to your savings account        │
│     or a sweep-in FD. Do this first — it's your immediate  │
│     buffer.                                                │
│                                                            │
│  ② ⏱ Quick Access layer — ₹[Y]                             │
│     Set up a recurring amount to any liquid mutual fund.   │
│     Money is back next business day when you need it.      │
│                                                            │
│  ③ 🔒 Stable Reserve layer — ₹[Z]                          │
│     Set up a recurring FD or short-term debt fund.         │
│     Do this once you've built Layer 1 and 2 first.         │
│                                                            │
│  ┌─────────────────────────┐  ┌────────────┐              │
│  │  Remind me on salary day │  │  Dismiss    │             │
│  └─────────────────────────┘  └────────────┘              │
└────────────────────────────────────────────────────────────┘
```

### Exit Confirmation Message (§1A)

> **Verbatim-locked.** Standard AI message bubble in the conversational stream. See §Exit Confirmation Message — Canonical Copy below for the locked text and design intent.

---

## Visual Design

### Contribution Plan Card

| Property | Value | Reference |
|:---|:---|:---|
| Card shape | Large radius (16dp) | DS01 §3 |
| Card elevation | Level 3 (6dp) | DS01 §4 |
| Card fill | `surfaceContainer` | DS01 §1 |
| Card content padding | 16dp | DS01 §5 |
| Card title | Title Large (22sp/400), `onSurface` | DS01 §2 |
| Contribution ₹ amount | Display Medium (45sp/400), `onSurface` | DS01 §2 |
| Contribution cadence label | Title Medium (16sp/500), `onSurface` at 60% opacity | DS01 §2 |
| "tap to edit" hint | Label Small (11sp/500), `secondary` | DS01 §2 |
| Milestone sub-header | Title Medium (16sp/500), `onSurface` | DS01 §2 |
| Sub-header divider | `outlineVariant`, full-width | DS01 §1 |
| Milestone row gap | 8dp | DS01 §5 |
| Milestone row — label + ₹ amount | Body Medium (14sp/400), `onSurface` | DS01 §2 |
| Milestone row — date | Body Medium (14sp/400), `onSurface` at 60% opacity | DS01 §2 |
| Milestone row — arrow separator | `→` (text character, not icon) | — |
| ★ Starter Shield row marker | ★ character in `warmPositive` colour | DS01 §1 |
| ★ row background | `warmPositive` at ~15% opacity (warm glow) | DS01 §1 — same as CP03 |
| Inactive milestone markers | `·` or blank — `onSurface` at standard opacity | DS01 §1 |
| Self-EMI summary line | Body Medium (14sp/400), `onSurface` at 60% opacity | DS01 §2 |
| CTA "Confirm my plan →" | `FilledButton`, `primary`, **full-width** | DS01 §1 |
| Section gap (milestone sub-header to first row) | 12dp | DS01 §5 |

### Action Card (DD09)

| Property | Value | Reference |
|:---|:---|:---|
| Card shape | Large radius (16dp) | DS01 §3 |
| Card elevation | Level 3 (6dp) | DS01 §4 |
| Card fill | `surfaceContainer` | DS01 §1 |
| Card content padding | 16dp | DS01 §5 |
| Card title | Title Large (22sp/400), `onSurface` | DS01 §2 |
| Subtitle "Set these up on your next salary date." | Body Medium (14sp/400), `onSurface` at 60% opacity | DS01 §2 |
| Step number badge | `CircleAvatar` (24dp diameter), `primary` fill, `onPrimary` text (Label Medium 12sp/500) | DS01 §1 |
| Step layer icon | ⚡ / ⏱ / 🔒 (emoji, inline with label) | — |
| Step label + ₹ amount | Body Medium (14sp/500), `onSurface` | DS01 §2 |
| Step instruction text | Body Small (12sp/400), `onSurface` at 60% opacity | DS01 §2 |
| Step divider (between steps) | `outlineVariant` | DS01 §1 |
| Step row gap | 12dp (section gap — steps are logical sections) | DS01 §5 |
| Reminder CTA | `OutlinedButton`, `secondary` | DS01 §1 |
| Dismiss CTA | `TextButton`, lower visual weight | — |

### Exit Confirmation Message

| Property | Value | Reference |
|:---|:---|:---|
| Container | Standard AI message bubble in conversational stream | — |
| Typography | Body Large (16sp/400) | DS01 §2 |
| Elevation | Level 0 (stream message) | DS01 §4 |

---

## Motion

| Animation | Config | Reference |
|:---|:---|:---|
| Contribution Plan Card materialisation (slide-up + fade) | Spring: stiffness 300, damping 0.8 | DS01 §6 |
| Milestone row stagger | 60ms delay between rows, spring: stiffness 300, damping 0.8 | DS01 §6 |
| Number morph (§C4 contribution edit → date recalculation) | Spring: stiffness 250, damping 0.85 | DS01 §6 |
| Action Card materialisation (slide-up + fade) | Spring: stiffness 300, damping 0.8 | DS01 §6 |
| Action step row stagger | 60ms delay between steps, spring: stiffness 300, damping 0.8 | DS01 §6 |
| Exit message fade-in | Opacity 0→1, 200ms `easeOut` | DS01 §6 |

### Materialisation Sequences

**Contribution Plan Card:**

1. Card container slides up + fades in (spring)
2. Title + contribution ₹ headline render immediately
3. Milestone sub-header + divider render
4. Milestone rows stagger in (60ms per row); ★ Starter Shield row first
5. Self-EMI summary line renders
6. CTA renders

**Action Card:**

1. AI acknowledgement message renders in stream (voice + on-screen)
2. Action Card slides up + fades in (spring) — 300ms after acknowledgement
3. Title + subtitle render immediately
4. Action step rows stagger in (60ms per step: ① → ② → ③)
5. Reminder + Dismiss CTAs render

> Row stagger delays vary by emotional state — see §Adaptive Behaviour — Emotional State.

---

## Milestone Timeline Rows — Detailed Spec

### Row Anatomy

```
[★ or ·]  [Milestone Label]  [₹ Amount]  →  [Date]
```

### Row Content (4 rows, fixed)

| Row | Marker | Label | ₹ Amount | Date |
|:---|:---|:---|:---|:---|
| 1 | ★ | Starter Shield | 1 month of essential expenses | Computed: contribution start + (amount ÷ contribution/mo) |
| 2 | · | 3 Months Secure | 3 months of essential expenses | Computed |
| 3 | · | Half-Year Shield | 6 months of essential expenses | Computed |
| 4 | · | Fully Funded | Full EF target | Computed |

### Date Format

- **Dates, not month counts.** "Aug 2026" — not "16 months away."
- Exception: the ★ Starter Shield row may include a parenthetical duration for immediacy: "Aug 2026 (X months away)" — at agent discretion for motivational framing.
- Date format: `MMM YYYY` (e.g., "Aug 2026").
- Dates are computed by the computation agent from: journey start date + contribution amount + total amount per milestone.

### ★ Starter Shield Visual Continuity

All visual properties (marker, colour, background, typography, elevation) must exactly match CP03 §Starter Shield Overlay — see CP03 L130–131 and DS01 §1. Same `warmPositive` at ~15% opacity warm glow, same ★ in `warmPositive`, same Body Medium (14sp/400), Level 0 elevation.

> **Design intent:** The user recognises the same visual from Phase 3. The AI does NOT re-introduce the Starter Shield — it references it: "Your first milestone is the Starter Shield — ₹[X] — same one we talked about earlier."

### Recalculation on §C4 Edit

When the user edits the contribution amount (§C4), all 4 milestone dates recalculate live:

- Number morph animation on each date (spring: stiffness 250, damping 0.85)
- Dates update simultaneously (no stagger on recalculation — stagger is for initial render only)
- If the new amount makes the Starter Shield reachable within 1 month, the ★ row pulses warmly once (colour transition: 300ms `easeInOut`, `warmPositive` at 15% → 30% → 15%)

---

## §C4 Contribution Edit — Phase 5 Variant

### Trigger

User taps the contribution ₹ amount on the Contribution Plan Card.

### Behaviour

| Property | Value |
|:---|:---|
| Input method | Inline numeric editor — `TextField` with `₹` prefix, numeric keyboard. Replaces the Display Medium ₹ text |
| Scope | Single value only — contribution amount. No summary card needed |
| Flow control | NOT flow-blocking. Card remains live while editing. Dates update on each keystroke (debounced 300ms) or on submit |
| Save | ✓ button or tap outside the field |
| Cancel | ✏️ dismiss — original value restored |
| Downstream effect | All 4 milestone dates recalculate (number morph). Self-EMI summary line updates |
| Content crossfade | 250ms `easeInOut` (DS01 §6) |

### Adaptive (§C4 — same as CP01)

| Condition | Rendering |
|:---|:---|
| Literacy 1–2 | Larger edit field. Helper text: "Type how much you can set aside each month (in ₹)" |
| Engagement: voice | User can state new amount verbally → §C2 confirm cycle |
| Emotional: Anxious | Edit confirmation includes: "Updated — let me recalculate your milestones" |

---

## Action Card — Detailed Spec

### Step Content by Layer

| Step | Icon | Layer | ₹ Amount Source | Instruction | Priority Note |
|:---|:---|:---|:---|:---|:---|
| ① | ⚡ | Instant Access | Phase 4 Layer 1 allocation | "Set up an auto-transfer to your savings account or a sweep-in FD. Do this first — it's your immediate buffer." | Highest priority — do first |
| ② | ⏱ | Quick Access | Phase 4 Layer 2 allocation | "Set up a recurring amount to any liquid mutual fund. Money is back next business day when you need it." | Second priority |
| ③ | 🔒 | Stable Reserve | Phase 4 Layer 3 allocation | "Set up a recurring FD or short-term debt fund. Do this once you've built Layer 1 and 2 first." | Explicitly sequenced after 1 + 2 |

> **Advisory ceiling (DD02):** Instrument TYPE only. No specific fund names, AMC names, bank products, or platform names.



### Card Persistence

| Property | Detail |
|:---|:---|
| In-stream lifecycle | Materialises in conversational stream after plan confirmation. Remains visible in stream history |
| Home surface persistence | Saved to the EF goal card on the home surface. Accessible without re-entering conversational flow |
| Update trigger | Action Card content updates if user recalibrates their plan (V2). In V1, the card is static after creation |

---

## Salary-Day Reminder — Detailed Spec

### Trigger

User taps "Remind me on salary day" on the Action Card.

### Flow

1. System prompts for OS notification permission (standard iOS/Android flow)
2. **If granted:** Recurring monthly reminder set for user's stated or inferred salary date
3. **If denied:** AI responds in stream: "No worries — your action plan is saved right here whenever you're ready." Card remains unchanged; reminder CTA becomes disabled (greyed, `onSurface` at 40% opacity)

### Notification Content

> "Your ₹[X] self-EMI is due. Tap to see your action steps."

- Deep-links to the Action Card on the EF goal card (home surface)
- Monthly cadence, aligned to salary date
- The "self-EMI" label is used in the notification — consistent with in-app framing

### Engineering Notes

- Requires push notification infrastructure (APNs / FCM)
- Salary date: inferred from employment type (e.g., "salaried" → last or first of month) or explicitly asked by the agent during Phase 5 contribution negotiation
- Opt-in only (§0B Rule 5 — privacy-aware activation)

---

## EF Goal Card (Home Surface) — Outline Spec

> **Note:** The EF Goal Card lives outside the conversational flow. It is a static, persistent card on the home surface. Full design spec is deferred to the Home Surface component library (future). This section defines only the data contract from Phase 5.

### Data Written at Phase 5 Completion

| Field | Source |
|:---|:---|
| Fund target (₹ amount + months) | Phase 3 confirmed target |
| Current amount | ₹0 (new user) or existing corpus (Scenario B/E) |
| Next milestone | ★ Starter Shield (label + ₹ + date) — or next incomplete milestone |
| Contribution plan summary | ₹[X]/mo on [salary date] |
| Action Card entry point | Tap to view action steps |
| Progress bar | Current amount ÷ target. At Phase 5 completion: 0% (new) or pre-existing % (Scenario B/E) |

---

## Adaptive Behaviour — Literacy

| Literacy | Contribution Plan Card | Milestone Rows | Action Card |
|:---|:---|:---|:---|
| 1–2 | ₹ amount only — no "self-EMI" label (unfamiliar concept at low literacy). Cadence label: "every month on payday." "tap to edit" hint: larger, prominent | Dates in "Month Year" format. No parenthetical durations | Simplified vehicle descriptions: "savings account" not "sweep-in FD." Omit "recurring FD" — use "fixed deposit." Step instructions in plain English |
| 3 | Standard rendering. Self-EMI label present | Standard. Date format: `MMM YYYY` | Standard vehicle descriptions |
| 4–5 | Full rendering. Self-EMI label prominent. Additional context line: "Adjust anytime — your milestones recalculate" | Date format: `MMM YYYY`. All rows show ₹ amounts with full Indian formatting | Full vocabulary: "sweep-in FD," "liquid mutual fund," "short-term debt fund." Layer yields mentioned in step subtitles |

---

## Adaptive Behaviour — Trust Level (CG01 §2.2)

| Trust Level | Contribution Plan Card | Action Card |
|:---|:---|:---|
| New / Warming | All 4 milestone rows visible. "tap to edit" hint visible. Self-EMI concept explained by AI in voice before card materialises | All 3 steps expanded with full instruction text. Reminder CTA prominent |
| Trusting | Milestone rows visible. "tap to edit" hint suppressed (user knows) | Steps show labels + amounts; instruction text collapsed behind tap. Reminder CTA standard |
| Dependent | Milestone headline only (★ Starter Shield + Fully Funded). Middle milestones collapsed behind "Show all milestones" | Steps compressed: layer icon + amount only. "Set it up" as single-line instruction |

---

## Adaptive Behaviour — Emotional State (CG01 §2.3)

| State | Contribution Plan Card | Action Card | Row stagger |
|:---|:---|:---|:---|
| Calm | Standard | Standard | 60ms |
| Anxious | Standard card. AI softens framing: "Take your time — you can always adjust" | Standard. Step ③ qualifier emphasised ("do this once 1 and 2 are done — no rush") | 100ms |
| Overwhelmed | **Headline-only:** ₹ amount + "Confirm" CTA. Milestones collapsed behind "See your milestones" tap. Self-EMI line hidden | **Step 1 only.** Steps 2 and 3 collapsed behind "See all steps." Reduces cognitive load to a single action | N/A |
| Excited | All milestones visible. AI can add motivational framing ("You'll hit Starter Shield before summer") | All steps expanded. Additional detail on vehicle types if literacy supports | 40ms |

---

## Adaptive Behaviour — Engagement Mode (CG01 §2.4)

| Mode | Rendering |
|:---|:---|
| Voice-heavy | CTA buttons full-width. AI narrates the self-EMI concept; screen renders the Plan Card simultaneously (§0B Rule 3). "Confirm my plan →" accepts voice equivalent ("confirm" / "let's do it") |
| Visual | All affordances prominent. "tap to edit" hint visible. Milestone rows show expand chevrons for future detail (V2) |
| Hybrid | Standard rendering per literacy level |

---

## Adaptive Behaviour — Variable Income

For users with variable income (detected from D1: freelance, gig, own business). LLM handles copy framing per J01 Phase 5 LLM prompt (L689–697). Component-level structural changes only:

| Element | Structural Adaptation |
|:---|:---|
| Contribution headline | Appends "(your floor)" suffix to ₹ amount |
| Salary-Day Reminder | Replaces salary-date inference with an explicit date-picker; default: 1st of month |

---

## Composition Rules

| Rule | Detail |
|:---|:---|
| Contribution Plan Card appears | Once in Phase 5. Re-renders only on §C4 contribution edit (dates update, card persists) |
| Action Card appears | Once, immediately after "Confirm my plan →" tap |
| Exit message appears | Once, after Action Card + Reminder flow completes |
| Position | Both cards inline in conversational stream. Action Card persists to home surface |
| Density accounting | Contribution Plan Card counts as 1 card toward viewport limit (CG01 §3). Action Card counts as +1 |
| Phase gate behaviour | Flow WAITS for "Confirm my plan →" before Action Card fires |
| D3 buffer in Phase 5 | If a D3 social obligation buffer was confirmed in Phase 3, the contribution amount MAY include a separate D3 line. The AI mentions this conversationally; the Contribution Plan Card shows total monthly amount (EF + D3 combined). Action Card does NOT have a separate D3 step — the D3 buffer allocation is folded into the appropriate layer |

---

## Interaction Affordances

| Affordance | Action |
|:---|:---|
| Tap ₹ contribution amount | §C4 inline edit. All milestone dates recalculate (number morph). Self-EMI line updates |
| "Confirm my plan →" | Plan locked. AI acknowledgement + Action Card materialises. Contribution Plan Card becomes read-only |
| Action Card "Remind me on salary day" | OS notification permission flow → recurring reminder OR graceful degradation |
| Action Card "Dismiss" | Card fades from stream (opacity 0, 200ms `easeOut`). Card remains accessible on home surface EF goal card |
| Action step row tap | Level 2 expand: brief contextual explanation of that layer's vehicle (crossfade 250ms `easeInOut`). Agent-decided depth |
| Exit message | Not interactive. Marks the end of V1 conversational flow. Monitoring state begins |

---

## Re-entry Scenario Notes

> **Agent context — not additional component states.** The following notes inform the composition agent's rendering decisions for returning users. The Contribution Plan Card and Action Card use their existing layouts and states with contextually appropriate data.

| Scenario | Component | Agent behaviour |
|:---|:---|:---|
| **B/E (existing corpus)** | Contribution Plan Card | Milestone dates account for existing progress. If the user already has ₹2L toward a ₹11.5L target, the Starter Shield milestone date reflects only the remaining gap (₹96K − existing Layer 1 allocation). The computation agent adjusts all dates accordingly. The agent frames progress positively: "You're already 17% there — here's the plan for the rest" |
| **B/E (existing corpus)** | Action Card | Steps reference only the remaining setup needed. If Layer 1 (Instant Access) is already adequately funded from existing savings, the agent may mark Step ① as "✓ Already covered" and emphasise Steps ② and ③. The card layout is unchanged — the step content adapts |

---

## Exit Confirmation Message — Canonical Copy (§1A)

> **Verbatim-locked. This copy must never be paraphrased, improvised, or truncated by the LLM.**

**On-screen text AND AI voice (simultaneous):**

> "Your plan is set. I'll keep an eye on things and reach out when something meaningful happens — a milestone hit, a life change, or a chance to optimise. You won't hear from me just to check in."

**Design intent:**
- Tone: proactive, non-intrusive, purposeful. The CarrotFin monitoring-state voice.
- "You won't hear from me just to check in" is the differentiation claim — distinguishes CarrotFin from notification-spam apps.
- Delivered after the Action Card + Reminder flow. This is the last thing the user sees/hears before the conversational flow ends.

**Source:** [J01-emergency-fund-setup.md §1A](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/journey-catalog/J01-emergency-fund-setup.md)

---

