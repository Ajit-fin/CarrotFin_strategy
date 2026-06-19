# DS01: CarrotFin Design System

> **Date:** 2026-06-18  
> **Status:** Foundation — referenced by all component specs  
> **Framework:** Flutter + Material Design 3 (standard, NOT M3 Expressive)  
> **Creative North Star:** "The Digital Private Bank" — editorial, high-end private banking aesthetic; quiet, expansive, infinitely intelligent

---

## 0. Design Philosophy

### The Financial Sanctuary

This design system moves away from the chaotic, high-energy "fintech" aesthetic typical of the Indian market. It adopts an editorial, high-end private banking feel — a **Financial Sanctuary**: a space that feels quiet, expansive, and infinitely intelligent.

### Intentional Asymmetry

To break the "template" look, we utilise **Intentional Asymmetry**. Left-weighted typography, generous varied white space, and overlapping "Glass" cards over deep tonal backgrounds create a sense of physical depth — making the AI feel less like a bot and more like a sophisticated concierge.

### Do's and Don'ts

| Do | Don't |
|:---|:---|
| Use asymmetrical layouts (2/3 + 1/3 splits create a high-end magazine feel) | Use sharp corners — everything must have at least `DEFAULT` (1rem/16dp) radius to maintain "Friendly Advisor" persona |
| Use `secondary` (Amber) sparingly — it is a "Momentum" colour; if everything is Amber, nothing is urgent | Use pure white (`#FFFFFF`) — always use `onSurface` (`#DEE2F7`) to reduce eye strain in dark mode |
| Embrace negative space — if a screen feels "empty," it's likely working | Use "Alert Red" for everything — use Amber for warnings and `error` (`#FFB4AB`) only for critical financial loss or system failure |
| Create left-weighted type hierarchies with varied whitespace | Centre everything on a perfectly symmetrical grid |
| Overlap Glass cards over deep tonal backgrounds for physical depth | Use flat, same-level card layouts with visible borders |

---

## 1. Colour System

### M3 Seed Palette

Seed: `#3CDDC7` (Teal Cyan — HSL 170°, 73%, 55%)

```dart
// Primary seed for M3 generation
ColorScheme.fromSeed(
  seedColor: Color(0xFF3CDDC7),
  brightness: Brightness.dark,
)
```

**Why teal?** The previous warm sage (`#6B8F71`) read as "garden app" in mockups. The teal seed produces a cooler, more authoritative palette that aligns with the "Digital Private Bank" north star while retaining the calming, non-aggressive character essential for a financial advisor.

### Dark Theme Colour Roles (Primary Mode)

CarrotFin is dark-mode-first. Light mode is secondary and uses M3-generated values from the same seed.

| Role | Dark Value | Usage |
|:---|:---|:---|
| `primary` | `#57F1DB` | Primary CTAs, active indicators, focused input borders |
| `onPrimary` | `#003731` | Text/icons on primary surfaces |
| `primaryContainer` | `#2DD4BF` | AI component gradient source, filled pill states (BYP indicator) |
| `onPrimaryContainer` | Generated | Text/icons on primaryContainer surfaces |
| `secondary` | Generated (amber-biased) | Secondary CTAs — "Momentum" colour |
| `secondaryContainer` | `#EE9800` | "Action Required" / "Invest Now" cues — use sparingly |
| `onSecondaryContainer` | Generated | Text/icons on secondaryContainer surfaces |
| `tertiary` | Generated | Accent elements, D9 pill |
| `error` | `#FFB4AB` | Critical financial loss, system failure only |
| `onError` | Generated | Text/icons on error surfaces |
| `surface` | Generated | Base surface (see Surface Hierarchy below) |
| `surfaceDim` | `#0E1321` | The Foundation — deepest background layer |
| `surfaceContainerLowest` | `#090E1C` | Inset surfaces (input fields, recessed areas) |
| `surfaceContainerLow` | `#141928` | Main body / workspace background |
| `surfaceContainer` | `#1A1F2E` | The Workspace — primary card background |
| `surfaceContainerHigh` | `#252A39` | Interactive cards, glass surfaces at 80% opacity |
| `surfaceContainerHighest` | `#2F3444` | Modals, elevated cards, bottom sheets |
| `surfaceBright` | `#3A3F4E` | AI advisor card backgrounds |
| `surfaceTint` | `#3CDDC7` | Tinted shadow source, ambient glow |
| `onSurface` | `#DEE2F7` | Primary text — **never use pure `#FFFFFF`** |
| `onSurfaceVariant` | `#DEE2F7` at 60% opacity | Secondary labels, descriptions (see §2 60% Rule) |
| `outline` | Generated | Card borders (discouraged — see No-Line Rule), dividers (discouraged — see No-Divider Rule) |
| `outlineVariant` | `#3C4A46` | Ghost Border fallback only (see §4) |

### Surface Hierarchy & Nesting

Treat the UI as nested sheets. Each inner container must be a **higher tier** than its parent to create natural upward lift toward the user.

```
surfaceDim (#0E1321)           ← The Foundation (deepest)
  └─ surfaceContainerLow (#141928)   ← Main body / workspace
       └─ surfaceContainer (#1A1F2E)       ← Cards
            └─ surfaceContainerHigh (#252A39)    ← Interactive elements
                 └─ surfaceContainerHighest (#2F3444) ← Modals, elevated cards
```

**The inset direction:** Use `surfaceContainerLowest` (#090E1C) *inside* a `surfaceContainer` to create an "inset" look (ideal for input fields). This is the only case where a child is a *lower* tier than its parent.

### The "No-Line" Rule

**Lines are a failure of hierarchy.** 1px solid borders are strictly prohibited for sectioning. Define boundaries exclusively through **Background Colour Shifts**:
- Use `surfaceContainerLow` for the main body
- Use `surfaceContainerHigh` for interactive cards
- The transition between these tokens creates a "soft edge" that feels premium and integrated

> **Migration note:** Existing component specs that reference `outline` or `outlineVariant` for dividers should prefer background colour shifts. Where accessibility requires a border, use the Ghost Border (§4).

### The "Glass & Gradient" Rule

To inject "soul" into the AI experience:

- **AI Components:** Use `primaryContainer` (`#2DD4BF`) background with **10% opacity** linear gradient fading into `surfaceContainer`. This creates a subtle teal wash that signals "AI-generated."
- **Glassmorphism:** For floating navigation or AI chat bubbles, use `surfaceContainerHigh` at **80% opacity** with `backdrop-filter: blur(20px)` (Flutter: `BackdropFilter` + `ImageFilter.blur(sigmaX: 20, sigmaY: 20)`). This prevents the UI from feeling "heavy."

### Semantic Colour Extensions

Custom `ThemeExtension<CarrotFinSemanticColors>` — overlays on M3, not replacements.

| Token | Light Value | Dark Value | Semantic Use |
|:---|:---|:---|:---|
| `warmPositive` | `#D4A25C` (warm amber) | `#E8BF80` | Positive attribution (DD07 +months), Instant Access layer (DD08 Layer 1), upward range changes, confirmed states |
| `onWarmPositive` | `#3E2A0A` | `#3E2A0A` | Text/icons on warmPositive surfaces |
| `coolNegative` | `#5A8CA6` (blue-slate) | `#8BAEC5` | Negative attribution (DD07 −months), Stable Reserve (DD08 Layer 3), downward range changes |
| `onCoolNegative` | `#0E2233` | `#0E2233` | Text/icons on coolNegative surfaces |
| `neutralMid` | `#A09880` (muted sand) | `#B8B0A0` | Quick Access layer (DD08 Layer 2) — between warm and cool |
| `onNeutralMid` | `#2A2518` | `#2A2518` | Text/icons on neutralMid |
| `conditionalSurface` | `#F5EDE0` (warm cream) | `#2A2520` | D3 Prompt Card background — distinct from standard card surfaces |
| `conditionalBorder` | `#C8B89A` | `#5A4F3E` | D3 Prompt Card dashed border colour |
| `aiGlow` | — | `#3CDDC7` at 15% opacity | Subtle primary glow at top-left of AI-generated cards |

**Dark mode:** M3 handles most roles automatically via `ColorScheme.fromSeed(brightness: Brightness.dark)`. Semantic extensions require explicit dark values (listed above). Directional colours (warm/cool) lighten in dark mode to maintain contrast. The `conditionalSurface` dark value has been deepened from `#3A3228` to `#2A2520` to better harmonise with the new blue-grey surface tones.

---

## 2. Typography

### Font Families

**Display & Headlines (Editorial Voice):** Manrope (Google Fonts) — geometric structure, tight kerning, modern authority. Used for portfolio totals, AI insight headlines, section headers.  
**Body & Labels (Utility Voice):** Inter (Google Fonts) — excellent ₹ rendering, clean data display, variable font.  
**Devanagari fallback:** Noto Sans Devanagari (future localisation).

```dart
// Theme setup
final textTheme = TextTheme(
  displayLarge:  GoogleFonts.manrope(/* ... */),
  displayMedium: GoogleFonts.manrope(/* ... */),
  headlineLarge: GoogleFonts.manrope(/* ... */),
  headlineMedium: GoogleFonts.manrope(/* ... */),
  titleLarge:    GoogleFonts.manrope(/* ... */),
  titleMedium:   GoogleFonts.inter(/* ... */),
  bodyLarge:     GoogleFonts.inter(/* ... */),
  bodyMedium:    GoogleFonts.inter(/* ... */),
  bodySmall:     GoogleFonts.inter(/* ... */),
  labelLarge:    GoogleFonts.inter(/* ... */),
  labelMedium:   GoogleFonts.inter(/* ... */),
  labelSmall:    GoogleFonts.inter(/* ... */),
);
```

### Type Scale Mapping

| M3 Role | Font | Size/Weight | J01 Usage |
|:---|:---|:---|:---|
| Display Large | Manrope | 57sp / 400 | — (reserved) |
| Display Medium | Manrope | 45sp / 400 | Target Card ₹ headline, Contribution Plan ₹ headline, portfolio totals |
| Headline Large | Manrope | 32sp / 400 | AI insight headlines |
| Headline Medium | Manrope | 28sp / 400 | Section headers within phase-gate cards |
| Title Large | Manrope | 22sp / 400 | Card titles (§C3, Allocation Card, Action Card) |
| Title Medium | Inter | 16sp / 500 | Phase labels, BYP indicator label |
| Body Large | Inter | 16sp / 400 | Conversational stream messages, general advice |
| Body Medium | Inter | 14sp / 400 | Detail rows (attribution, allocation layers, milestone rows) |
| Body Small | Inter | 12sp / 400 | DICGC footnote, explanatory prose |
| Label Large | Inter | 14sp / 500 | Chip labels (§C1, §C2), CTA text |
| Label Medium | Inter | 12sp / 500 | Pill labels (BYP indicator) |
| Label Small | Inter | 11sp / 500 | Footnotes, timestamps |

### The 60% Rule

Secondary labels (using `onSurfaceVariant`) must always sit at **60% opacity** to ensure primary financial data (at 100% opacity `onSurface`) remains the focal point. This creates a clear visual hierarchy between "Data" (Manrope, full opacity) and supporting context (Inter, 60%).

### Number Formatting

- ₹ symbol: prefix, no space: `₹1,20,000` (not `₹ 1,20,000`)
- Indian number system: `₹1,20,000` (not `₹120,000`)
- Monthly amounts: `₹6,000/mo`
- Ranges: `₹50K–80K` (en-dash, abbreviated)

---

## 3. Shape

### Corner Radius Scale

| M3 Token | Radius | J01 Components |
|:---|:---|:---|
| Default | 16dp (1rem) | **Minimum for all elements** — no sharp corners permitted |
| Small | 8dp | Chips (§C1, §C2), FilterChip pills (BYP), range chips |
| Medium | 12dp | Stream cards (Myth Buster, Safety Net So Far, detail rows), D3 Confirm Card |
| Large | 16dp | Phase-gate cards (§C3 Summary, Target Card, Allocation Card, Contribution Plan Card, Action Card) |
| Extra-Large | 28dp | Home surface cards |
| Wealth | 48dp (3rem) | Top-level wealth summary cards — soft and approachable |
| Full (pill) | `StadiumBorder` | Primary CTA buttons, BYP dimension pills |

> **Rule:** Everything must have at least `DEFAULT` (16dp) radius. The only exception is `Small` (8dp) for chips and pills where the element height makes 16dp impractical.

**Special shapes:**
- D3 Prompt Card: `BoxDecoration` with dashed border (`BorderSide` + dashPattern `[6, 3]`), Large radius, `conditionalSurface` fill.
- BYP Progress Indicator: pill-shaped (`StadiumBorder`) for dimension pills.
- Input fields: `surfaceContainerLowest` with Large radius (16dp). No bottom-line style. **On focus:** Ghost Border transitions to 100% opaque `primary`.

---

## 4. Elevation & Depth

### The Layering Principle

Depth is achieved through **Tonal Layering**, not shadows. The surface hierarchy (§1) IS the elevation system.

| Effect | Technique | Components |
|:---|:---|:---|
| Inset | `surfaceContainerLowest` inside `surfaceContainer` | Input fields, recessed data areas |
| Flat | Same surface tier as parent | Inline text, cards-within-cards (attribution strip rows) |
| Raised | `surfaceContainerHigh` inside `surfaceContainerLow` | Interactive cards, tappable list items |
| Floating | `surfaceContainerHighest` + ambient shadow | Bottom sheets, modals |

### M3 Elevation Mapping (Legacy Compatibility)

Component specs (CP01–CP06) reference M3 elevation levels. Map as follows:

| M3 Level | dp | Tonal Equivalent | J01 Components |
|:---|:---|:---|:---|
| 0 | 0 | Parent surface | Conversational stream messages, inline text, cards-within-cards |
| 1 | 1 | `surfaceContainerLow` | Stream cards (Myth Buster, Safety Net So Far, detail rows) |
| 3 | 6 | `surfaceContainer` | Phase-gate cards (§C3 Summary, Target Card, Allocation Card, Contribution Plan, Action Card) |
| 6 | — | `surfaceContainerHigh` | Pinned indicators (BYP Progress Indicator) |

**Rule:** No nested elevation. Attribution strip rows within Target Card are Level 0. DICGC footnote below Allocation Card is Level 0.

### Ambient Shadows

When a component must float (e.g., Bottom Sheet, modal):

| Property | Value | Notes |
|:---|:---|:---|
| Blur | 40dp – 60dp | Generous blur for soft glow |
| Opacity | 4% – 8% | Barely perceptible |
| Colour | `surfaceTint` (`#3CDDC7`) | **Tinted shadow** — creates bioluminescent glow, not muddy grey |

```dart
BoxShadow(
  color: Color(0xFF3CDDC7).withOpacity(0.06),
  blurRadius: 48,
  offset: Offset(0, 8),
)
```

### The "Ghost Border" Fallback

If contrast testing requires a border (accessibility), use the Ghost Border:
- Stroke: `outlineVariant` (`#3C4A46`) at **15% opacity**
- This provides a hint of structure without interrupting visual flow
- On focus/active state, Ghost Border may transition to 100% opacity `primary` (e.g., input fields)

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
| List item separation | 12dp vertical | Between list items — **no divider lines** (see No-Divider Rule) |

### The "No-Divider" Rule

**Forbid divider lines in lists.** Separate list items using:
1. `12dp` of vertical whitespace, or
2. Subtle background toggle between `surfaceContainer` and `surfaceContainerLow`

This is an extension of the No-Line Rule (§1) to list contexts.

---

## 6. Components

### Buttons

| Variant | Shape | Background | Text | Shadow | Usage |
|:---|:---|:---|:---|:---|:---|
| Primary | Pill (`StadiumBorder`) | `primary` (`#57F1DB`) | `onPrimary` (`#003731`) | None | Main CTAs |
| Secondary / Momentum | Pill (`StadiumBorder`) | `secondaryContainer` (`#EE9800`) | `onSecondaryContainer` | None | "Action Required", "Invest Now" — sparingly |
| Tertiary | Pill (`StadiumBorder`) | Transparent | `primary` (`#57F1DB`) | None | Low-priority navigation |

### Input Fields

- **Container:** `surfaceContainerLowest` (`#090E1C`) fill
- **Shape:** Large radius (16dp) — **no bottom-line style**
- **Border (resting):** None (or Ghost Border if contrast requires)
- **Border (focused):** `primary` (`#57F1DB`) at 100% opacity, 1.5dp stroke
- **Text:** `onSurface` (`#DEE2F7`)
- **Hint text:** `onSurfaceVariant` (60% opacity)

### Cards & Lists

- **No-Divider Rule:** See §5 — forbid divider lines between list items
- **Standard cards:** `surfaceContainer` background, Large radius (16dp)
- **Wealth cards:** `Wealth` radius (48dp / 3rem) for top-level wealth summaries — soft and approachable
- **Background toggle:** Alternate `surfaceContainer` / `surfaceContainerLow` for adjacent list items when whitespace alone is insufficient

### Adaptive Visual Cards (AI Interface)

- Background: `surfaceBright` (`#3A3F4E`)
- **AI Glow:** Subtle `primaryContainer` (`#2DD4BF`) radial gradient at **15% opacity** at top-left corner, fading to transparent. Indicates "generated by advisor."
- Glass effect for floating AI elements: `surfaceContainerHigh` at 80% opacity + `BackdropFilter` blur(20px)
- Apply the Glass & Gradient Rule (§1) for AI conversation bubbles

---

## 7. Motion

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
| Ghost Border focus transition | `Curves.easeInOut` | 200ms | 15% → 100% opacity on input focus |
| AI Glow pulse | `Curves.easeInOut` | 400ms | Subtle pulse on AI card appearance |

### Guiding Principle

> "The UI breathes with the conversation." Animations respond to voice pacing — not robotic or canned. Spring motion gives life; effects motion stays subtle.

---

## Appendix A: Seed Colour Migration Notes

**Previous seed:** `#6B8F71` (Warm Sage — HSL 130°, 14%, 49%)  
**New seed:** `#3CDDC7` (Teal Cyan — HSL 170°, 73%, 55%)

### What Changed and Why
- The warm sage produced M3 surfaces with green-grey tones that read "earthy/organic" — misaligned with the "Digital Private Bank" creative direction
- The teal seed produces cool blue-grey surfaces in dark mode that feel premium, editorial, and authoritative
- Amber/gold semantic colours (`warmPositive`, `secondaryContainer`) contrast more effectively against the cooler surface palette, creating clearer visual hierarchy

### Downstream Impact
- **CP01–CP06:** All semantic token names are preserved. Downstream specs require no changes to token *references*, only the resolved *values* change
- **Surface references:** `surfaceContainer`, `surfaceContainerHigh`, etc. remain valid M3 role names. Resolved hex values shift from green-grey to blue-grey
- **`conditionalSurface` dark value:** Adjusted from `#3A3228` to `#2A2520` to maintain warmth differential against cooler surfaces
- **`coolNegative` light value:** Adjusted from `#6B8FA6` to `#5A8CA6` for improved contrast against teal primary
- **New tokens:** `aiGlow` (semantic extension), `Wealth` radius (shape), `Full/pill` radius (shape)

### M3 Generation vs. Pinned Values
Values marked with explicit hex above are **pinned overrides** in the `ColorScheme` constructor. All other roles (marked "Generated") use M3's tonal palette generation from the seed. This ensures:
1. M3 accessibility contrast ratios are maintained for generated roles
2. The "Financial Sanctuary" aesthetic is preserved for key surface and primary roles
3. Future light-mode generation remains compatible

```dart
final darkScheme = ColorScheme.fromSeed(
  seedColor: Color(0xFF3CDDC7),
  brightness: Brightness.dark,
).copyWith(
  // Pinned overrides for Financial Sanctuary aesthetic
  primary: Color(0xFF57F1DB),
  onPrimary: Color(0xFF003731),
  primaryContainer: Color(0xFF2DD4BF),
  secondaryContainer: Color(0xFFEE9800),
  error: Color(0xFFFFB4AB),
  surfaceDim: Color(0xFF0E1321),
  surfaceContainerLowest: Color(0xFF090E1C),
  surfaceContainerLow: Color(0xFF141928),
  surfaceContainer: Color(0xFF1A1F2E),
  surfaceContainerHigh: Color(0xFF252A39),
  surfaceContainerHighest: Color(0xFF2F3444),
  surfaceBright: Color(0xFF3A3F4E),
  surfaceTint: Color(0xFF3CDDC7),
  onSurface: Color(0xFFDEE2F7),
  outlineVariant: Color(0xFF3C4A46),
);
```
