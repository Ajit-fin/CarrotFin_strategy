# DS01: CarrotFin Design System

> **Date:** 2026-04-16  
> **Status:** Foundation — referenced by all component specs  
> **Framework:** Flutter + Material Design 3 (standard, NOT M3 Expressive)

---

## 1. Colour System

### M3 Seed Palette

Seed: `#6B8F71` (Warm Sage — HSL 130°, 14%, 49%)

```dart
ColorScheme.fromSeed(seedColor: Color(0xFF6B8F71))
```

| Role | Light | Dark | Usage |
|:---|:---|:---|:---|
| `primary` | Generated | Generated | Primary CTAs, active indicators |
| `onPrimary` | Generated | Generated | Text/icons on primary surfaces |
| `primaryContainer` | Generated | Generated | Filled pill states (BYP indicator) |
| `secondary` | Generated | Generated | Secondary CTAs, supporting text |
| `secondaryContainer` | Generated | Generated | Chip backgrounds (§C1, §C2) |
| `tertiary` | Generated | Generated | Accent elements, D9 pill |
| `surface` | Generated | Generated | Card backgrounds |
| `surfaceContainerLowest` | Generated | Generated | Conversational stream background |
| `surfaceContainerLow` | Generated | Generated | Level 1 cards |
| `surfaceContainer` | Generated | Generated | Level 3 cards |
| `surfaceContainerHigh` | Generated | Generated | Pinned indicators (BYP) |
| `error` | Generated | Generated | Validation errors |
| `outline` | Generated | Generated | Card borders, dividers |
| `outlineVariant` | Generated | Generated | Subtle separators (attribution strip rows) |

### Semantic Colour Extensions

Custom `ThemeExtension<CarrotFinSemanticColors>` — overlays on M3, not replacements.

| Token | Light Value | Dark Value | Semantic Use |
|:---|:---|:---|:---|
| `warmPositive` | `#D4A25C` (warm amber) | `#E8BF80` | Positive attribution (DD07 +months), Instant Access layer (DD08 Layer 1), upward range changes, confirmed states |
| `onWarmPositive` | `#3E2A0A` | `#3E2A0A` | Text/icons on warmPositive surfaces |
| `coolNegative` | `#6B8FA6` (blue-slate) | `#8BAEC5` | Negative attribution (DD07 −months), Stable Reserve (DD08 Layer 3), downward range changes |
| `onCoolNegative` | `#0E2233` | `#0E2233` | Text/icons on coolNegative surfaces |
| `neutralMid` | `#A09880` (muted sand) | `#B8B0A0` | Quick Access layer (DD08 Layer 2) — between warm and cool |
| `onNeutralMid` | `#2A2518` | `#2A2518` | Text/icons on neutralMid |
| `conditionalSurface` | `#F5EDE0` (warm cream) | `#3A3228` | D3 Prompt Card background — distinct from standard card surfaces |
| `conditionalBorder` | `#C8B89A` | `#5A4F3E` | D3 Prompt Card dashed border colour |

**Dark mode:** M3 handles most roles automatically via `ColorScheme.fromSeed(brightness: Brightness.dark)`. Semantic extensions require explicit dark values (listed above). Directional colours (warm/cool) lighten in dark mode to maintain contrast.

---

## 2. Typography

### Font Family

**Primary:** Inter (Google Fonts) — excellent ₹ rendering, clean data display, variable font.  
**Devanagari fallback:** Noto Sans Devanagari (future localisation).

### Type Scale Mapping

| M3 Role | Size/Weight | J01 Usage |
|:---|:---|:---|
| Display Large | 57sp / 400 | — (reserved) |
| Display Medium | 45sp / 400 | Target Card ₹ headline, Contribution Plan ₹ headline |
| Headline Large | 32sp / 400 | — (reserved) |
| Headline Medium | 28sp / 400 | Section headers within phase-gate cards |
| Title Large | 22sp / 400 | Card titles (§C3, Allocation Card, Action Card) |
| Title Medium | 16sp / 500 | Phase labels, BYP indicator label |
| Body Large | 16sp / 400 | Conversational stream messages |
| Body Medium | 14sp / 400 | Detail rows (attribution, allocation layers, milestone rows) |
| Body Small | 12sp / 400 | DICGC footnote, explanatory prose |
| Label Large | 14sp / 500 | Chip labels (§C1, §C2), CTA text |
| Label Medium | 12sp / 500 | Pill labels (BYP indicator) |
| Label Small | 11sp / 500 | Footnotes, timestamps |

### Number Formatting

- ₹ symbol: prefix, no space: `₹1,20,000` (not `₹ 1,20,000`)
- Indian number system: `₹1,20,000` (not `₹120,000`)
- Monthly amounts: `₹6,000/mo`
- Ranges: `₹50K–80K` (en-dash, abbreviated)

---

## 3. Shape

| M3 Token | Radius | J01 Components |
|:---|:---|:---|
| Small | 8dp | Chips (§C1, §C2), FilterChip pills (BYP), range chips |
| Medium | 12dp | Stream cards (Myth Buster, Safety Net So Far, detail rows), D3 Confirm Card |
| Large | 16dp | Phase-gate cards (§C3 Summary, Target Card, Allocation Card, Contribution Plan Card, Action Card) |
| Extra-Large | 28dp | Reserved (home surface cards — not used in J01) |

**Special shapes:**
- D3 Prompt Card: `BoxDecoration` with dashed border (`BorderSide` + dashPattern `[6, 3]`), Large radius, `conditionalSurface` fill.
- BYP Progress Indicator: pill-shaped (`StadiumBorder`) for dimension pills.

---

## 4. Elevation

| M3 Level | dp | J01 Components |
|:---|:---|:---|
| 0 | 0 | Conversational stream messages, inline text, cards-within-cards (attribution strip rows inside Target Card) |
| 1 | 1 | Stream cards (Myth Buster, Safety Net So Far, detail rows, D3 Confirmed Card) |
| 3 | 6 | Phase-gate cards (§C3 Summary, Target Card, Allocation Card, Contribution Plan, Action Card) |
| 6 | — | Pinned indicators (BYP Progress Indicator) |

**Rule:** No nested elevation. Attribution strip rows within Target Card are Level 0. DICGC footnote below Allocation Card is Level 0.

---

## 5. Spacing

| Token | Value | Usage |
|:---|:---|:---|
| Grid baseline | 4dp | All spacing derives from 4dp grid |
| Card content padding | 16dp | Internal padding for all cards |
| Card vertical margin | 8dp | Between cards in conversational stream |
| Chip gap | 8dp | Between chips in a chip set |
| Section gap | 12dp | Between logical sections within a card |
| Attribution row gap | 8dp | Between rows in DD07 attribution strip |
| Detail row gap | 8dp | Between layer rows in Allocation Card |
| BYP indicator internal | 12dp horizontal, 8dp vertical | Pill spacing within the pinned strip |

---

## 6. Motion

### Spring-Based Spatial Motion (Flutter `SpringSimulation`)

| Animation | Stiffness | Damping | Overshoot | Notes |
|:---|:---|:---|:---|:---|
| Card materialisation (slide-up + fade) | 300 | 0.8 | Subtle | All phase-gate cards, stream cards |
| Pill fill (BYP ○ → ✓) | 400 | 0.75 | Snappy | Smaller element = snappier spring |
| Number morph (BYP range, ₹ amounts) | 250 | 0.85 | Smooth | Counter animation with spring easing |
| Staggered entry (attribution rows, milestone rows) | 300 | 0.8 | Subtle | 60ms delay between rows |
| D3 Prompt Card slide-up | 300 | 0.8 | Subtle | Separate card below Target Card |

### Effects Motion (Non-Spatial)

| Animation | Curve | Duration | Notes |
|:---|:---|:---|:---|
| Colour transitions (warm/cool pulse) | `Curves.easeInOut` | 300ms | BYP directional colour changes |
| Opacity fade (card dismiss, D3 skip) | `Curves.easeOut` | 200ms | Fade out on skip/dismiss |
| Content crossfade (§C4 edit transitions) | `Curves.easeInOut` | 250ms | In-place content swap |

### Guiding Principle

> "The UI breathes with the conversation." Animations respond to voice pacing — not robotic or canned. Spring motion gives life; effects motion stays subtle.
