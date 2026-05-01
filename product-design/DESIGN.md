---
version: "1.0.0"
name: "CarrotFin"
description: "Warm futurism — a trusted AI companion for personal finance. Forward-looking in precision, warm in feeling. Not a bank lobby, not a neon startup page. A sounding board you can trust."

colors:
  # Primary palette (amber-orange seed — "the carrot")
  primary: "#9B4500"
  on-primary: "#FFFFFF"
  primary-container: "#FFDBC9"
  on-primary-container: "#341100"

  # Secondary palette (warm brown)
  secondary: "#765848"
  on-secondary: "#FFFFFF"
  secondary-container: "#FFDBC9"
  on-secondary-container: "#2B160A"

  # Tertiary palette (warm olive — for positive/growth signals)
  tertiary: "#606134"
  on-tertiary: "#FFFFFF"
  tertiary-container: "#E6E6AD"
  on-tertiary-container: "#1C1D00"

  # Surface palette (warm off-white)
  surface: "#FFF8F5"
  on-surface: "#211A16"
  on-surface-variant: "#52443C"
  surface-container-low: "#FEF1EB"
  surface-container: "#F8EBE5"
  surface-container-high: "#F2E5DF"

  # Outline
  outline: "#85736A"
  outline-variant: "#D7C2B8"

  # Inverse surface (dark sections — waitlist, footer)
  inverse-surface: "#372F2B"
  inverse-on-surface: "#FCEEEA"

  # Semantic aliases
  positive: "#E6E6AD"        # Tertiary container — savings progress, "on track"
  attention: "#9B4500"       # Primary — CTAs, key metrics
  info: "#FFDBC9"            # Secondary container — tool cards, informational
  emphasis-bg: "#FEF1EB"     # Surface container low — alternating sections

  # Error
  error: "#BA1A1A"

typography:
  # Display / Headlines — Plus Jakarta Sans
  display-large:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "57px"
    fontWeight: 700
    lineHeight: "64px"
    letterSpacing: "-0.25px"
  display-medium:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "45px"
    fontWeight: 700
    lineHeight: "52px"
  display-small:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "36px"
    fontWeight: 600
    lineHeight: "44px"
  headline-large:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "32px"
    fontWeight: 600
    lineHeight: "40px"
  headline-medium:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "28px"
    fontWeight: 600
    lineHeight: "36px"
  headline-small:
    fontFamily: "Plus Jakarta Sans"
    fontSize: "24px"
    fontWeight: 600
    lineHeight: "32px"

  # Body / Labels — Inter
  title-large:
    fontFamily: "Inter"
    fontSize: "22px"
    fontWeight: 600
    lineHeight: "28px"
  title-medium:
    fontFamily: "Inter"
    fontSize: "16px"
    fontWeight: 600
    lineHeight: "24px"
  body-large:
    fontFamily: "Inter"
    fontSize: "18px"
    fontWeight: 400
    lineHeight: "28px"
  body-medium:
    fontFamily: "Inter"
    fontSize: "16px"
    fontWeight: 400
    lineHeight: "24px"
  body-small:
    fontFamily: "Inter"
    fontSize: "14px"
    fontWeight: 400
    lineHeight: "20px"
  label-large:
    fontFamily: "Inter"
    fontSize: "14px"
    fontWeight: 500
    lineHeight: "20px"
  label-medium:
    fontFamily: "Inter"
    fontSize: "12px"
    fontWeight: 500
    lineHeight: "16px"

  # Special — Lora italic for empathy/question cards
  serif-accent:
    fontFamily: "Lora"
    fontSize: "18px"
    fontWeight: 400
    fontStyle: "italic"
    lineHeight: "28px"

spacing:
  xs: "4px"
  sm: "8px"
  md: "16px"
  lg: "24px"
  xl: "32px"
  2xl: "48px"
  3xl: "64px"
  4xl: "96px"

shape:
  # M3 corner radius scale
  none: "0px"
  extra-small: "4px"
  small: "8px"
  medium: "12px"
  large: "16px"
  extra-large: "28px"
  full: "9999px"

components:
  button-primary:
    backgroundColor: "{colors.primary}"
    color: "{colors.on-primary}"
    borderRadius: "{shape.extra-large}"
    height: "48px"
    fontFamily: "Inter"
    fontSize: "14px"
    fontWeight: 500
  button-tonal:
    backgroundColor: "{colors.secondary-container}"
    color: "{colors.on-secondary-container}"
    borderRadius: "{shape.large}"
    height: "44px"
  button-outlined:
    backgroundColor: "transparent"
    color: "{colors.primary}"
    border: "1px solid {colors.outline}"
    borderRadius: "{shape.large}"
    height: "44px"
  card-elevated:
    backgroundColor: "{colors.surface-container-low}"
    borderRadius: "{shape.medium}"
    boxShadow: "0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.06)"
    padding: "{spacing.lg}"
  card-filled:
    backgroundColor: "{colors.secondary-container}"
    borderRadius: "{shape.large}"
    borderLeft: "4px solid {colors.primary}"
    padding: "{spacing.lg}"
  chip-unselected:
    backgroundColor: "{colors.surface-container}"
    color: "{colors.on-surface}"
    border: "1px solid {colors.outline}"
    borderRadius: "{shape.full}"
    height: "32px"
    padding: "0 {spacing.md}"
  chip-selected:
    backgroundColor: "{colors.primary-container}"
    color: "{colors.on-primary-container}"
    borderRadius: "{shape.full}"
    height: "32px"
  text-field:
    borderRadius: "{shape.small}"
    height: "56px"
    border: "1px solid {colors.outline}"
    focusBorder: "2px solid {colors.primary}"
  nav-bar:
    backgroundColor: "{colors.surface}"
    height: "64px"
    position: "sticky"
  progress-bar:
    track: "{colors.surface-container}"
    fill: "{colors.primary}"
    height: "8px"
    borderRadius: "{shape.full}"
  result-card:
    backgroundColor: "{colors.primary-container}"
    borderRadius: "{shape.extra-large}"
    padding: "{spacing.xl}"

layout:
  max-width: "1240px"
  content-width: "1080px"
  tool-widget-width: "580px"
  reading-width: "680px"
  breakpoint-mobile: "600px"
  breakpoint-tablet: "904px"
  margin-mobile: "{spacing.md}"
  margin-desktop: "{spacing.lg}"
---

## Overview

CarrotFin is a **warm-futuristic** fintech companion. The design must feel like a trusted, intelligent advisor — forward-looking in its precision, warm in its color palette and interactions. It should never feel like a bank lobby, a generic dashboard, or a neon startup page.

**Three pillars:**
- **Futuristic** — clean lines, ample whitespace, purposeful motion
- **Trustworthy** — consistent hierarchy, stable palette, no gimmicks
- **Companion** — warm amber accents, rounded shapes, conversational tone

This is a **mobile-first web app** (not a native app). Most Indian users access via mobile browsers. All interactions must work on both touch and pointer devices.

## Colors

Use `{colors.primary}` (`#9B4500`, dark amber) only for primary CTAs, key metrics, and interactive accents. Never use it as a background for large areas.

Use `{colors.primary-container}` (`#FFDBC9`, soft peach) for hero gradients, result card backgrounds, and prominent highlight areas. It is warm and inviting without being aggressive.

Use `{colors.surface}` (`#FFF8F5`) as the default page background. Alternate sections use `{colors.surface-container-low}` (`#FEF1EB`) to create gentle visual rhythm without hard borders.

Use `{colors.inverse-surface}` (`#372F2B`) only for the Waitlist section and Footer. This creates a deliberate dark "weight" at the end of the page that signals importance and urgency.

Use `{colors.tertiary-container}` (`#E6E6AD`, warm olive/yellow-green) for positive signals — savings on-track, financial health indicators, "good" states in the Fitness Check.

**Do not** use pure blacks, pure whites, or generic blues. Every color should feel warm.

## Typography

All headlines and display text use **Plus Jakarta Sans** (600–700 weight). It is geometric but warm — modern without being cold.

All body text, labels, and UI elements use **Inter**. It has excellent readability at small sizes and renders the ₹ (rupee sign) correctly.

For the "Questions Like Yours" empathy cards on the landing page, use **Lora italic** (`{typography.serif-accent}`). This serif accent creates a personal, reflective contrast against the geometric system — the question should feel like it came from a real person, not a UI.

On mobile:
- Display Large scales down from 57px → 36px
- Display Medium scales down from 45px → 28px
- Headline Large scales down from 32px → 24px

## Shape

Shapes follow M3 conventions. Rounder = more personal and inviting. More angular = more informational.
- Primary CTAs (filled buttons): `{shape.extra-large}` (28px) — most prominent, most rounded
- Question cards, result cards: `{shape.extra-large}` (28px) — feel like a "held" artifact
- Tool teaser cards, How It Works tiles: `{shape.medium}` (12px) — content containers
- Chips (answer options in tools): `{shape.full}` (pill) — M3 standard for selection
- Text input fields: `{shape.small}` (8px) — minimal, focused

## Components

### Navigation Bar
Sticky top bar. Gains subtle elevation (`box-shadow`) after scroll. Logo on left, links + CTA on right. On mobile: hamburger → slide-out drawer. Active link: underline in `{colors.primary}`.

### Buttons
- **Primary** (`{components.button-primary}`): Use for the one main action on any screen — "See where you stand", "Join the Waitlist", "Submit". One per screen maximum.
- **Tonal** (`{components.button-tonal}`): Secondary actions — "Try the Calculator →", "Explore tools".
- **Outlined** (`{components.button-outlined}`): Tertiary / neutral — "Download result", "Copy link".
- **Text buttons**: Inline navigation only — "Read →", "Skip".

### Cards
- **Elevated** (`{components.card-elevated}`): Tool teasers (Section C), How It Works tiles (Section D). Hover → lift slightly on desktop.
- **Filled** (`{components.card-filled}`): "Questions Like Yours" cards (Section B). Left orange border accent is essential — it signals emotional resonance, not just information.

### Chips
All tool questionnaire answer options use chips. Unselected: `{components.chip-unselected}`. Selected: `{components.chip-selected}` — warm peach fill, no border, checkmark. Chips should feel like gentle, confident choices, not binary checkboxes.

### Result Card
The Financial Fitness Score and FI Calculator result use `{components.result-card}`. This is a "held artifact" — it should feel like something valuable, not just a section. Extra-large corners. Peach-to-white gradient. Score displayed in `{typography.display-small}` in `{colors.primary}`.

### Dark Sections (Waitlist + Footer)
Background: `{colors.inverse-surface}` (`#372F2B`). Text: `{colors.inverse-on-surface}`. These are the only dark sections on the site. The transition from the light page to dark signals "this is where you take the action." Do not add more dark sections.

## Motion

All animations use `transform` and `opacity` only — never layout properties. Must respect `prefers-reduced-motion`.

- **Section reveals**: Fade-in + slide-up 20px, 300ms, staggered 100ms per element. Triggered by scroll (IntersectionObserver).
- **Card hover (desktop only)**: Box-shadow elevation lift, 200ms ease.
- **Chip selection**: Background color transition, 150ms.
- **Tool step transitions**: Slide-left (outgoing) + slide-right (incoming), 500ms.
- **Result reveal**: Scale from 0.9 → 1.0 + fade-in, 500ms.
- **Waitlist position counter**: Count-up animation from 0 → actual number, 1000ms. This is the one "moment of delight" — give it weight.

## Layout

The page uses a warm, airy layout. Maximum content width is `{layout.content-width}` (1080px), centered. Tool widgets (Fitness Check, FI Calculator) are constrained to `{layout.tool-widget-width}` (580px) to feel conversational, not sprawling.

Section rhythm: light background (`{colors.surface}`) → slightly tinted (`{colors.surface-container-low}`) → light → dark (waitlist). This alternation creates visual pacing without hard dividers.

Mobile margins: `{layout.margin-mobile}` (16px). Desktop margins: `{layout.margin-desktop}` (24px) side margins, content auto-centered.

## Do's and Don'ts

**Do:**
- Use warm amber (`{colors.primary}`) sparingly — it must feel like emphasis, not ambient color
- Give the waitlist position number a count-up animation — it signals real community size
- Use `{typography.serif-accent}` (Lora italic) exclusively for empathy/question content
- Show the disclaimer ("Educational tool, not financial advice") on every result and in the footer
- Use `{colors.tertiary-container}` for any "positive financial health" indicator

**Don't:**
- Use pure black text — use `{colors.on-surface}` (`#211A16`) for warmth
- Add more than one primary CTA per screen
- Use the dark section treatment (`{colors.inverse-surface}`) anywhere except the waitlist signup and footer
- Use stock photos or photos of people smiling at phones — abstract geometric illustration only
- Use bottom navigation or FABs — this is a web app, not a native mobile app
- Show raw income or financial figures in downloadable cards — only scores and aggregated insights
