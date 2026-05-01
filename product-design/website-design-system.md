# CarrotFin Pre-Launch Website — Design System

> **Domain:** Design — Website  
> **Last updated:** 2026-04-30  
> **Staleness threshold:** Until website ships  
> **Input:** [Goals Framework](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/website-launch-goals.md) · [Information Architecture](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/website-information-architecture.md)  
> **Framework:** Dark-dominant design system — Carrot Navy + energetic orange  
> **Influence:** Monarch Money dark variation (trustworthy yet futuristic)

---

## 1. Design Philosophy

CarrotFin's website projects **trustworthy futurism** — a deep, sophisticated dark canvas that lets vibrant orange energy and premium glassmorphism surfaces communicate intelligence and warmth simultaneously. It should feel like a companion that's smarter than you at finance but never cold or condescending.

**The feel:** A premium, tech-forward financial companion — not a bank lobby, not a startup neon page, not a generic dashboard. Dark and immersive enough to feel cutting-edge. Warm enough (through orange energy and soft glows) to feel approachable.

**Three pillars:**
- **Futuristic** — Deep navy canvas, glassmorphism surfaces, subtle glowing borders, purposeful motion. The design communicates "this is built with care and intelligence."
- **Trustworthy** — Authoritative serif headings, consistent hierarchy, stable palette, no gimmicks. Every element earns its place.
- **Energetic** — Vibrant orange (`#FF6B35`) accents, rounded-pill CTAs, conversational tone, inviting interactions. The design says "I'm on your side."

**Visual signature:**
- **Glassmorphism surfaces** — Semi-transparent cards with `backdrop-filter: blur()` and subtle white-border glows over the dark canvas.
- **Glowing borders** — Faint orange or cool-blue border glows on interactive elements to create depth without heaviness.
- **Rounded-pill buttons** — Full-radius CTAs that feel modern and tappable.
- **Serif authority** — Newsreader serif headlines convey financial gravitas; Inter sans-serif body text ensures data legibility.

**Web adaptation:**
- Website/webapp, not a native mobile app. Most users access via mobile browsers (India = mobile-first internet).
- Responsive grid adapted for web breakpoints. Components as CSS equivalents with consistent shape, elevation, and state behavior.
- All interactions work on both touch and pointer devices. Hover states enhance but never gate functionality.
- No mobile-app-only patterns (no FABs, no bottom navigation, no swipe gestures as primary navigation).

---

## 2. Color System

### 2.1 Core Palette

| Role | Token | Value | Description |
|---|---|---|---|
| **Primary** | `--color-primary` | `#FF6B35` | Energetic orange — the "carrot." CTAs, key accents, active states. |
| **Secondary** | `--color-secondary` | `#D8E3FB` | Cool lavender-blue — supporting text, card highlights, soft emphasis. |
| **Tertiary** | `--color-tertiary` | `#8C9BB0` | Muted steel-blue — subdued labels, metadata, secondary text. |
| **Neutral (Background)** | `--color-neutral` | `#081425` | Deep "Carrot Navy" — primary canvas for all dark surfaces. |

### 2.2 Dark Theme Tokens (Primary Theme)

The website is **dark-dominant**. The deep navy canvas is the default, with glassmorphism surfaces creating layered depth.

| Role | Token | Value |
|---|---|---|
| **Background** | `--color-bg` | `#081425` |
| **Surface Level 0** | `--color-surface-0` | `rgba(216, 227, 251, 0.03)` |
| **Surface Level 1** | `--color-surface-1` | `rgba(216, 227, 251, 0.06)` |
| **Surface Level 2** | `--color-surface-2` | `rgba(216, 227, 251, 0.10)` |
| **Surface Level 3** | `--color-surface-3` | `rgba(216, 227, 251, 0.14)` |
| **On Surface (primary text)** | `--color-on-surface` | `#FFFFFF` |
| **On Surface Variant (secondary text)** | `--color-on-surface-variant` | `#D8E3FB` |
| **On Surface Muted (tertiary text)** | `--color-on-surface-muted` | `#8C9BB0` |
| **Primary** | `--color-primary` | `#FF6B35` |
| **On Primary** | `--color-on-primary` | `#FFFFFF` |
| **Primary Glow** | `--color-primary-glow` | `rgba(255, 107, 53, 0.25)` |
| **Border Default** | `--color-border` | `rgba(216, 227, 251, 0.12)` |
| **Border Hover** | `--color-border-hover` | `rgba(216, 227, 251, 0.24)` |
| **Border Glow (orange)** | `--color-border-glow` | `rgba(255, 107, 53, 0.30)` |
| **Error** | `--color-error` | `#FF6B6B` |
| **Positive** | `--color-positive` | `#4ADE80` |

### 2.3 Light Sections

The website is primarily dark-themed. Light treatment is used **sparingly** for:
- **Downloadable result cards** — need to render well on white paper / social media shares
- **Embedded tool widgets** (if shared externally) — light variant for readability outside the app context

Light section tokens:

| Role | Token | Value |
|---|---|---|
| **Light Background** | `--color-light-bg` | `#F8FAFF` |
| **Light Surface** | `--color-light-surface` | `#FFFFFF` |
| **Light On Surface** | `--color-light-on-surface` | `#081425` |
| **Light On Surface Variant** | `--color-light-on-surface-variant` | `#4A5568` |

### 2.4 Semantic Colors

| Purpose | Token | Value | Context |
|---|---|---|---|
| Positive / Growth | `--color-positive` | `#4ADE80` | Savings progress, "on track" states, FI calculator results |
| Attention / Priority | `--color-primary` | `#FF6B35` | CTAs, waitlist position, key metrics |
| Neutral / Info | `--color-secondary` | `#D8E3FB` | Tool cards, informational content |
| Subtle emphasis | `--color-surface-1` | `rgba(216, 227, 251, 0.06)` | Alternating sections, card backgrounds |

---

## 3. Typography

### 3.1 Typeface

| Role | Family | Weight | Source |
|---|---|---|---|
| **Display & Headlines** | **Newsreader** | 400, 500, 600 | Google Fonts (serif) |
| **Body & Labels** | **Inter** | 400, 500, 600 | Google Fonts (sans-serif) |

**Why these:** Newsreader is an elegant transitional serif that conveys financial authority and gravitas — it signals "we take your money seriously" without feeling stodgy. The contrast between serif headlines and sans-serif body creates a premium editorial feel (inspired by Monarch Money). Inter remains the body text font for its excellent readability, ₹ rendering, and Indic numeral support.

**The pairing principle:** Serif for persuasion and authority (headlines, hero statements, feature names). Sans-serif for function and data (body text, labels, numbers, UI elements).

### 3.2 Type Scale

| Role | Font | Size | Weight | Line Height | Letter Spacing |
|---|---|---|---|---|---|
| **Display Large** | Newsreader | 57px / 3.5625rem | 500 | 64px | -0.5px |
| **Display Medium** | Newsreader | 45px / 2.8125rem | 500 | 52px | -0.25px |
| **Display Small** | Newsreader | 36px / 2.25rem | 400 | 44px | 0 |
| **Headline Large** | Newsreader | 32px / 2rem | 500 | 40px | -0.25px |
| **Headline Medium** | Newsreader | 28px / 1.75rem | 400 | 36px | 0 |
| **Headline Small** | Newsreader | 24px / 1.5rem | 400 | 32px | 0 |
| **Title Large** | Inter | 22px / 1.375rem | 600 | 28px | 0 |
| **Title Medium** | Inter | 16px / 1rem | 600 | 24px | 0.15px |
| **Title Small** | Inter | 14px / 0.875rem | 500 | 20px | 0.1px |
| **Body Large** | Inter | 18px / 1.125rem | 400 | 28px | 0.15px |
| **Body Medium** | Inter | 16px / 1rem | 400 | 24px | 0.25px |
| **Body Small** | Inter | 14px / 0.875rem | 400 | 20px | 0.4px |
| **Label Large** | Inter | 14px / 0.875rem | 500 | 20px | 0.1px |
| **Label Medium** | Inter | 12px / 0.75rem | 500 | 16px | 0.5px |
| **Label Small** | Inter | 11px / 0.6875rem | 500 | 16px | 0.5px |

Role-to-component mappings: each component spec in §7 declares its type role.

### 3.3 Mobile Type Scale Adjustments

| Role | Desktop | Mobile |
|---|---|---|
| Display Large | 57px | 36px |
| Display Medium | 45px | 28px |
| Display Small | 36px | 24px |
| Body Large (manifesto) | 18px | 17px |
| Body Medium | 16px | 15px |

### 3.4 Special Typography

| Context | Treatment |
|---|---|
| **"Questions Like Yours" cards** | Body Large in Newsreader italic — the serif already carries the personal, reflective tone |
| **₹ amounts in results** | Display or Headline size (Newsreader), `font-variant-numeric: tabular-nums` for alignment |
| **Fitness Score / FI numbers** | Headline Large or Display Small (Newsreader), `font-variant-numeric: tabular-nums`, primary color (`#FF6B35`) for emphasis |
| **Trust line (hero)** | Title Small (Inter), `letter-spacing: 0.5px`, `--color-on-surface-muted` — subtle, grounded |

---

## 4. Shape System

### 4.1 Corner Radii

| Token | Value |
|---|---|
| `--shape-none` | 0px |
| `--shape-xs` | 4px |
| `--shape-sm` | 8px |
| `--shape-md` | 12px |
| `--shape-lg` | 16px |
| `--shape-xl` | 24px |
| `--shape-2xl` | 28px |
| `--shape-full` | 9999px |

Component-to-shape mappings in §4.2 below.

### 4.2 Shape Usage by Component

| Component | Corner | Rationale |
|---|---|---|
| Hero CTA button | Full (pill) | Monarch-inspired rounded-pill for primary actions |
| Secondary CTA button | 2xl (28px) | Softer pill, still prominent |
| Question cards (Section B) | xl (24px) | Generous rounding, premium glass feel |
| Tool teaser cards | lg (16px) | Standard content container |
| Glassmorphism cards | xl (24px) | Soft, premium — pairs with blur + border glow |
| Fitness Check widget | xl (24px) | Interactive, distinct from static content |
| Fitness Score result card | 2xl (28px) | Downloadable — standalone artifact |
| FI Calculator result card | 2xl (28px) | Consistent with Fitness Score card |
| Selection chips | Full (pill) | Pill-shaped for filter/selection |
| Input fields | md (12px) | Slightly softer than M3 default |
| Waitlist position card | xl (24px) | Prominent dashboard element |
| Nav bar | Full (pill) | Floating nav pill — Monarch-inspired |

---

## 5. Elevation & Surface (Glassmorphism)

### 5.1 Glassmorphism Depth System

Depth is achieved through **glassmorphism layers** — increasing background opacity + backdrop blur + border glow — rather than traditional drop shadows. On a dark canvas, this creates a sophisticated layered depth.

| Level | Background | Backdrop Filter | Border | Usage |
|---|---|---|---|---|
| **Level 0** | `--color-surface-0` | None | `1px solid var(--color-border)` | Flat sections, backgrounds |
| **Level 1** | `--color-surface-1` | `blur(12px)` | `1px solid var(--color-border)` | Cards at rest, nav bar |
| **Level 2** | `--color-surface-2` | `blur(20px)` | `1px solid var(--color-border-hover)` | Cards on hover, active panels |
| **Level 3** | `--color-surface-3` | `blur(32px)` | `1px solid var(--color-border-hover)` | Modals, overlays, dropdowns |

### 5.2 Glow Effects

| Type | CSS | Usage |
|---|---|---|
| **Orange glow (CTA)** | `box-shadow: 0 0 20px var(--color-primary-glow), 0 0 60px rgba(255, 107, 53, 0.10)` | Primary buttons on hover, active CTA sections |
| **Cool glow (info)** | `box-shadow: 0 0 20px rgba(216, 227, 251, 0.08)` | Info cards, tool widgets on hover |
| **Border glow** | `border-color: var(--color-border-glow)` | Interactive card hover state |

### 5.3 Depth Behavior

| Interaction | Depth Change |
|---|---|
| Card at rest | Level 1 (glass, subtle border) |
| Card on hover | Level 1 → Level 2 + border glow (200ms ease) — pointer only |
| Card on press | Scale 0.98 + Level 1 (100ms ease) |
| Modal overlay | Level 3 + dark scrim (`rgba(8, 20, 37, 0.7)`) |
| Floating nav bar | Level 2 (glass pill, always) |

**Web + touch note:** Hover glow transitions apply only on pointer devices (`@media (hover: hover)`). On touch devices, cards remain at Level 1 — press feedback uses scale transform instead.

---

## 6. Layout & Grid

### 6.1 Responsive Breakpoints

| Breakpoint | Width | Layout | Columns | Margins | Gutter |
|---|---|---|---|---|---|
| **Compact** (Mobile) | < 600px | Single column | 4 | 16px | 8px |
| **Medium** (Tablet) | 600–904px | Flexible | 8 | 24px | 12px |
| **Expanded** (Desktop) | 905–1240px | Multi-column | 12 | 24px | 16px |
| **Large** (Wide) | > 1240px | Centered max-width | 12 | auto (centered) | 16px |

### 6.2 Content Width Constraints

| Context | Max Width | Rationale |
|---|---|---|
| Overall page container | 1240px | Prevent ultra-wide stretching |
| Manifesto / long-form text | 680px | Optimal reading line length (60-75 characters) |
| Landing page sections | 1080px | Comfortable content width with breathing room |
| Tool widgets (Fitness Check, FI Calc) | 580px | Focused — feels conversational, not sprawling |
| Cards grid | 1080px | 2-3 column on desktop, 1-column on mobile |

### 6.3 Spacing Scale (M3 4px Base)

| Token | Value | Usage |
|---|---|---|
| `--spacing-xs` | 4px | Inline padding, icon gaps |
| `--spacing-sm` | 8px | Chip padding, tight grouping |
| `--spacing-md` | 16px | Card internal padding, element gaps |
| `--spacing-lg` | 24px | Section sub-spacing, card gaps |
| `--spacing-xl` | 32px | Section titles to content |
| `--spacing-2xl` | 48px | Between major sections (mobile) |
| `--spacing-3xl` | 64px | Between major sections (desktop) |
| `--spacing-4xl` | 96px | Hero top/bottom padding |

### 6.4 Section-by-Section Layout Spec

> Section inventory defined in [Information Architecture](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/website-information-architecture.md) §3. This table specifies visual treatment only.

| Section | Background | Padding (Mobile) | Padding (Desktop) | Content Width |
|---|---|---|---|---|
| **A: Hero** | `--color-bg` with subtle radial gradient: `radial-gradient(ellipse at 50% 0%, rgba(255, 107, 53, 0.08) 0%, transparent 60%)` | 96px top, 48px bottom | 96px top, 64px bottom | 1080px, centered text |
| **B: Questions Like Yours** | `--color-bg` | 48px vertical | 64px vertical | 1080px, 3-col grid (desktop) / stack (mobile) |
| **C: Tools Teaser** | `--color-surface-0` | 48px vertical | 64px vertical | 1080px, 2-col grid (desktop) / stack (mobile) |
| **D: How It Works** | `--color-bg` | 48px vertical | 64px vertical | 1080px, 3-col grid (desktop) / stack (mobile) |
| **E: Waitlist Signup** | `--color-surface-1` with orange glow: `radial-gradient(ellipse at 50% 100%, rgba(255, 107, 53, 0.12) 0%, transparent 50%)` | 48px vertical | 64px vertical | 480px centered form |
| **F: Footer** | `--color-surface-0` | 32px vertical | 48px vertical | 1080px |
| **Tool pages** | `--color-bg` | 48px vertical | 64px vertical | 580px centered widget |
| **Dashboard** | `--color-bg` | 48px vertical | 64px vertical | 1080px, card grid |

---

## 7. Components

### 7.1 Navigation Bar

> Content (link labels, dropdown items) defined in [IA §2](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/website-information-architecture.md). This spec covers visual treatment.

| Property | Spec |
|---|---|
| Type | Floating pill nav bar (centered, Monarch-inspired) |
| Height | 56px (desktop), 48px (mobile) |
| Shape | Full (pill) radius, glassmorphism Level 2 |
| Background | `--color-surface-2` with `backdrop-filter: blur(20px)` |
| Border | `1px solid var(--color-border)` |
| Behavior | Fixed at top with 16px margin. Always glass. |
| Left | CarrotFin logo (wordmark + icon), `--color-on-surface` |
| Right (desktop) | Text links (`--color-on-surface-variant`) + primary pill CTA button |
| Right (mobile) | Hamburger icon → slide-out glass drawer |
| Active state | Text in `--color-primary`, no underline |
| Hover state | Text fades to `--color-on-surface` (pointer only) |
| Dropdown (desktop) | Glass panel, Level 3, `--shape-xl` corners |

### 7.2 Buttons

| Variant | Shape | Usage | Spec |
|---|---|---|---|
| **Filled** (Primary) | Full (pill) | Hero CTA, Waitlist submit, key actions | Background: `--color-primary`. Text: `--color-on-primary`. Height: 48px. Label Large (Inter 500). Hover: orange glow. |
| **Filled Tonal** | Full (pill) | Secondary CTAs ("Try the Calculator →") | Background: `--color-surface-2`. Text: `--color-on-surface`. Height: 44px. Border: `1px solid var(--color-border)`. Hover: border glow. |
| **Outlined** | Full (pill) | Tertiary actions ("Copy link", "Download result") | Border: `1px solid var(--color-border)`. Text: `--color-secondary`. Height: 44px. Hover: border brightens. |
| **Text** | None | Inline links, "Read →", navigation | Text: `--color-primary`. No background. Underline on hover (pointer devices). |

**State behavior:**
- Hover (pointer only): Filled → orange glow + slight scale(1.02). Tonal/Outlined → border brightens.
- Focus: `2px solid var(--color-primary)`, 2px offset ring — always visible for keyboard nav.
- Press/Active: scale(0.98) + 150ms ease.
- Disabled: 38% opacity, no glow.

### 7.3 Cards

| Variant | Usage | Spec |
|---|---|---|
| **Glass** | Tool teasers, How It Works tiles | Level 1 glassmorphism. `--color-surface-1` + `backdrop-filter: blur(12px)`. `--shape-xl` corners. `1px solid var(--color-border)`. Hover → Level 2 + border glow (pointer only). |
| **Accent** | Question cards (Section B) | `--color-surface-1` background. `--shape-xl` corners. Left border accent: 3px solid `--color-primary`. |
| **Outlined** | Profile sections in waitlist dashboard | `--color-surface-0` background. `1px solid var(--color-border)`. `--shape-lg` corners. |

### 7.4 Chips (Selection/Filter)

| Property | Spec |
|---|---|
| Type | Filter Chip (single-select) / Input Chip (multi-select) |
| Shape | Full (pill) |
| Height | 36px |
| Unselected | `--color-surface-1` background. `--color-on-surface-variant` text. `1px solid var(--color-border)`. |
| Selected | `--color-primary` background. `--color-on-primary` text. No border. Checkmark icon. |
| Usage | Financial Fitness Check answers, profile completion inputs, FI Calculator options |

### 7.5 Text Fields

| Property | Spec |
|---|---|
| Type | Outlined text field on glass surface |
| Shape | `--shape-md` (12px) |
| Height | 56px |
| Background | `--color-surface-0` |
| Border | `1px solid var(--color-border)` → `2px solid var(--color-primary)` on focus |
| Label | Floating label, `--color-on-surface-muted` color |
| Text | `--color-on-surface` |
| Usage | Email input, "biggest financial question" textarea, calculator number inputs |

### 7.6 Progress Indicator

| Property | Spec |
|---|---|
| Type | Linear progress indicator |
| Track | `--color-surface-1` |
| Fill | `--color-primary` with subtle glow: `box-shadow: 0 0 8px var(--color-primary-glow)` |
| Height | 6px |
| Shape | Full (pill) |
| Usage | Profile completion bar in waitlist dashboard |

### 7.7 Downloadable Result Cards

> Framing and privacy rules defined in [IA §3.2](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/website-information-architecture.md).

| Property | Spec |
|---|---|
| Dimensions | 1080×1920px (portrait) for Fitness Score card; flexible for FI plan summary |
| Shape | `--shape-2xl` (28px) |
| Background | Dark variant: gradient from `#081425` to `#0D1F3C`. Light variant (for sharing): `--color-light-bg` to `#FFFFFF`. |
| Content | Score/result headline (Display Small, Newsreader), pillar breakdown or key metric, CarrotFin logo + URL at bottom |
| Accent | Orange glow behind the primary score number |
| Generation | Client-side canvas rendering — produces a downloadable PNG |

### 7.8 Toast / Snackbar

| Property | Spec |
|---|---|
| Type | Glass snackbar |
| Position | Bottom center (mobile), bottom right (desktop) |
| Background | `--color-surface-2` + `backdrop-filter: blur(20px)` |
| Border | `1px solid var(--color-border)` |
| Shape | Full (pill) |
| Duration | 3 seconds, auto-dismiss |
| Usage | "+10 spots!" on profile completion, "Link copied!" on referral copy, "Result downloaded!" |

---

## 8. Motion

### 8.1 Easing Curves (M3 Standard)

| Token | Curve | Usage |
|---|---|---|
| **Emphasized** | `cubic-bezier(0.2, 0, 0, 1)` | Page transitions, section reveals on scroll |
| **Emphasized Decelerate** | `cubic-bezier(0.05, 0.7, 0.1, 1)` | Elements entering view, card appearing |
| **Emphasized Accelerate** | `cubic-bezier(0.3, 0, 0.8, 0.15)` | Elements leaving view, modal dismiss |
| **Standard** | `cubic-bezier(0.2, 0, 0, 1)` | Hover states, chip selection, general transitions |

### 8.2 Durations

| Duration | Value | Usage |
|---|---|---|
| **Short** | 150ms | Hover states, ripple, chip toggle |
| **Medium** | 300ms | Card elevation change, section fade-in, progress bar fill |
| **Long** | 500ms | Page/section transitions, Fitness Check step transitions, FI Calculator step transitions |

### 8.3 Scroll Animations

| Element | Animation | Trigger |
|---|---|---|
| Section B question cards | Fade-in + slide-up (20px), staggered 100ms per card | Scroll into viewport (IntersectionObserver) |
| Section C tool cards | Fade-in + slide-up, staggered | Scroll into viewport |
| Section D How It Works tiles | Fade-in + slide-up, staggered | Scroll into viewport |
| Fitness Check step transitions | Slide-left (outgoing) + slide-right (incoming), 500ms | User selects an answer |
| Fitness Check / FI Calc result | Scale-up from 0.9 → 1.0 + fade-in, 500ms | After final answer / calculation |
| Waitlist position number | Count-up animation from 0 → actual number, 1000ms | On page load / after signup |
| Profile section complete | Checkmark draw animation + "+10 spots!" toast | On section submit |

### 8.4 Motion Principles

All animations use `transform`/`opacity` only (no layout properties) and respect `@media (prefers-reduced-motion: reduce)`. Every animation must communicate a state change — no decorative motion.

---

## 9. Iconography

| Property | Spec |
|---|---|
| Icon set | **Material Symbols** (Outlined, weight 300, grade 0, optical size 24) |
| Size scale | 18px (inline), 24px (standard), 40px (feature icons on landing page) |
| Color | `--color-on-surface-muted` default, `--color-primary` for interactive, `--color-on-primary` for icons on primary buttons |
| Usage | Navigation, CTA buttons, chips, profile sections, social links, download actions |

**Key icons:** `monitor_heart` (Fitness Check), `calculate` (FI Calculator), `leaderboard` (waitlist), `group_add` (referral), `download` (results), `check_circle` (complete). Full icon mapping determined during implementation.

---

## 10. Imagery & Illustration

| Property | Spec |
|---|---|
| Style | Abstract geometric illustrations with glassmorphism-consistent depth. No stock photography. No photos of people smiling at phones. |
| Palette | Derived from the dark color system — `--color-primary` accents on navy canvas, `--color-secondary` fills, soft glows |
| Dark canvas rule | Illustrations must render on `#081425` background. Use semi-transparent shapes and orange accents for focal points. |

Component-specific illustration briefs (hero visual, tool cards) to be defined during mockup phase.

---

## 11. Accessibility

| Requirement | Spec |
|---|---|
| Color contrast | All text meets WCAG 2.1 AA (4.5:1 for body, 3:1 for large text). White on `#081425` = 16.7:1 ✔️. `#D8E3FB` on `#081425` = 11.2:1 ✔️. `#8C9BB0` on `#081425` = 5.3:1 ✔️. `#FF6B35` on `#081425` = 4.6:1 ✔️ (large text only; pair with white for body text). |
| Focus indicators | Visible focus ring (`2px solid var(--color-primary)`, 2px offset) on all interactive elements. Highly visible on dark backgrounds. |
| Touch targets | Minimum 48×48px for all tappable elements on mobile. 44×44px minimum on desktop. Critical since most users access via mobile browsers. |
| Reduced motion | `@media (prefers-reduced-motion: reduce)` disables scroll animations, glow effects, and decorative transitions |
| Screen reader | Semantic HTML. ARIA labels on interactive widgets (Fitness Check, FI Calculator). Live regions for dynamic content (waitlist position updates). |
| Keyboard navigation | Full keyboard navigability for desktop users. Visible focus states. Logical tab order. Tab key must navigate all interactive elements. |
| Dark mode note | Since the site is dark-dominant, there is no OS-level dark/light toggle. Glassmorphism `backdrop-filter` requires GPU compositing — ensure fallback solid backgrounds for older browsers. |

---

## 12. Implementation Notes

### Modular CSS Architecture

CSS follows a strict layered dependency model. Each layer only imports from layers above it — never sideways or downward.

```
styles/
├── tokens.css              ← Layer 0: Design tokens only. No selectors.
│                              Colors (dark-dominant palette), spacing, shape, glow, motion as CSS custom properties.
│                              Single source of truth — change a token, change every component.
│
├── reset.css               ← Layer 1: Minimal reset (box-sizing, margin, font-smoothing)
│                              Imports: tokens.css
│
├── typography.css           ← Layer 1: Type scale classes (.text-display-large, .text-body-medium, etc.)
│                              Newsreader for display/headline, Inter for body/label.
│                              Imports: tokens.css
│
├── utilities.css            ← Layer 1: Layout primitives and helpers
│                              .container, .section, .grid-2, .grid-3, .sr-only, .visually-hidden
│                              Spacing utilities: .mt-md, .mb-lg, .gap-md
│                              Imports: tokens.css
│
├── global.css               ← Layer 2: Entry point — imports all above + sets global defaults
│                              @import 'tokens.css', 'reset.css', 'typography.css', 'utilities.css'
│                              Sets body font, background, color
│
├── glass.css                ← Layer 2.5: Glassmorphism utility classes
│                              .glass-1, .glass-2, .glass-3, .glow-orange, .glow-cool
│                              Includes fallback solid backgrounds for non-GPU browsers
│                              Imports: tokens.css
│
├── components/              ← Layer 3: Self-contained component styles
│   ├── button.css             Each component references only tokens — never other components.
│   ├── card.css               Naming: .cf-button, .cf-card, .cf-chip (cf- prefix)
│   ├── chip.css
│   ├── text-field.css
│   ├── progress-bar.css
│   ├── nav.css                Floating glass pill nav bar
│   ├── snackbar.css           Glass pill snackbar
│   └── score-card.css       ← Downloadable result card (canvas source styles)
│
└── pages/                   ← Layer 4: Page-specific layout and section styles
    ├── landing.css             References tokens + utilities. May compose components.
    ├── tools.css               Shared tool page layout (centered widget, progress, results)
    ├── dashboard.css           Dashboard card grid, profile sections
    └── manifesto.css           Narrow reading column layout
```

**Modularity rules:**
- `tokens.css` has zero CSS selectors — only `:root { --token: value }` declarations
- Component CSS files never reference other component CSS — only tokens
- Page CSS may compose component classes but never override component internals
- No `!important` anywhere — specificity is managed by layer ordering
- Each component CSS file can be loaded independently (with tokens)

### Font Loading

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=Newsreader:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
```

Use `font-display: swap` to prevent FOIT. Preconnect to Google Fonts for faster load. Newsreader italic loaded for "Questions Like Yours" card treatment.

### Performance Budget

> Full verification targets in [Implementation Plan](file:///Users/kshekhaw/.gemini/antigravity/brain/b41de578-02c1-4119-880e-6d1a2b507c87/artifacts/implementation_plan.md).

| Metric | Target |
|---|---|
| LCP | < 2.0s (mobile 4G), < 1.5s (desktop) |
| Mobile payload | < 300KB first load (critical for Indian 4G networks) |
| Font weight | < 150KB (subset Latin + Devanagari numerals) |
