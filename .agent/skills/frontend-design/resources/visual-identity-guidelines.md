# Visual Identity Guidelines

> **Skill type:** Design intent resource  
> **Invoked by:** `/mockup`, `/ux-designer`, `/buildspec` (on-demand)  
> **Last updated:** 2026-04-16  
> **Scope:** Design intent only — no technology or implementation prescriptions

---

## Purpose

CarrotFin's product-design layer defines *what* the AI composes and *why*. This file defines *how it should look and feel* — the visual identity principles that every component, mockup, and BuildSpec must carry as design intent. The dev workspace makes all implementation-level choices (specific fonts, specific CSS, specific framework patterns). This file provides the aesthetic direction they work within.

**Not covered here:** component adaptive behavior (see `ux-design-system/`), composition rules (see `screen-taxonomy.md`), or interaction model (see `interaction-model.md`).

---

## 1. Design Thinking Protocol

Before designing any component pattern, mockup, or visual surface, answer these four questions:

| Question | What It Governs | CarrotFin-Specific Constraint |
|---|---|---|
| **Purpose** — What financial problem does this surface solve? Who sees it? | Content selection, information density | Must map to a user state model dimension (literacy, risk, stage, mode, emotion, trust) |
| **Tone** — What aesthetic direction serves this purpose? | Visual treatment, color intensity, animation energy | Must be *contextually adaptive* — the same surface may need a reassuring tone for one user and a celebratory tone for another |
| **Constraints** — What technical and cognitive limits apply? | Component count, density, motion budget | Must respect composition rules from `screen-taxonomy.md` (max 5-7 components, anchor consistency) |
| **Differentiation** — What makes this *unforgettable*? | The one design choice someone will remember | Must not look like CRED, Groww, ET Money, or INDmoney. See anti-generic rules below |

### Adaptive Tone Mapping

Unlike traditional design where tone is set once per product, CarrotFin's tone adapts to user emotional context:

| User Emotional State | Visual Tone | What Changes |
|---|---|---|
| **Calm / Neutral** | Confident, clean | Standard palette, measured spacing, subtle animation |
| **Anxious** (spending spike, market downturn) | Reassuring, warm | Softer accent colors, reduced information density, slower animation timing, grounding anchor elements |
| **Excited** (goal milestone, positive returns) | Celebratory, energetic | Brighter accents, micro-celebration animations, upward visual momentum |
| **Overwhelmed** (first-time, information overload) | Minimal, guided | Reduced component count, generous whitespace, single-focus CTA |

---

## 2. Typography Intent

### Pairing Philosophy

CarrotFin's interface weaves two distinct typographic voices:

- **Data voice** — for financial figures, metrics, projections, chart labels. Needs: precision, tabular alignment, clarity at small sizes. Intent: a typeface with geometric or monospaced characteristics that conveys accuracy and trust.
- **Conversational voice** — for AI dialogue, explanations, nudges, educational copy. Needs: warmth, approachability, readability at length. Intent: a humanist typeface that feels like a knowledgeable friend, not a bank document.

**The pairing should feel like one personality with two registers:** professional when showing your portfolio, approachable when explaining what it means.

### Adaptive Typography Hierarchy

Typography density adapts to user literacy level:

| Literacy Level | Type Treatment |
|---|---|
| **Level 1-2** (novice) | Larger body text, generous line height, headline + single-line explanations. Prioritize clarity over information density |
| **Level 3** (intermediate) | Standard body text, headline + subtitle + brief explanation. Balanced density |
| **Level 4-5** (advanced) | Compact body text permitted, headline + data-dense subtitles with delta metrics. Density serves efficiency |

### Rules

- **No generic defaults.** System fonts, Arial, Helvetica, Roboto as *design direction* signal "we didn't think about this." The typography should be distinctive for a finance app.
- **Tabular figures for financial data.** Amounts, percentages, and projections must use tabular/monospaced figures so columns align and values don't shift as they change.
- **Body text minimum 16px equivalent on mobile.** Non-negotiable for readability and to avoid browser auto-zoom on iOS.
- **Line height ≥1.5 for body text.** Financial content requires careful reading — cramped text increases cognitive load and error.

---

## 3. Color Philosophy

### Palette Structure

CarrotFin's color system uses a **dominant + accent** model with semantic overlays:

- **Dominant palette** — the ambient visual identity. 2-3 closely related colors that define the brand feeling. Should convey trust and intelligence without defaulting to "fintech blue."
- **Accent palette** — 1-2 sharp accent colors for CTAs, highlights, and attention-drawing moments. High contrast against the dominant palette.
- **Semantic palette** — functional colors with universal meaning:
  - Success/growth (positive returns, goal on track)
  - Warning (budget approaching limit, upcoming deadline)
  - Alert/danger (overspending, missed payment)
  - Neutral/informational

### Adaptive Color Intent

Color adapts to emotional context (see Adaptive Tone Mapping above):

- **Anxious state:** Semantic alert colors softened (less saturated red → warm amber). Background moves toward calming tones.
- **Celebratory state:** Accent colors intensified. Brief color bursts in micro-animations.
- **Overwhelmed state:** Palette simplified. Fewer competing colors. Increased whitespace as visual breathing room.

### Rules

- **Semantic color tokens, not raw values.** Colors in design specs should be named by function (`primary`, `surface`, `on-surface`, `success`, `warning`, `danger`) not by value. This enables adaptive theming and light/dark mode.
- **Light and dark mode designed together.** Dark mode is not "invert the colors." It requires desaturated/lighter tonal variants, separate contrast testing, and independent palette tuning.
- **Contrast minimums are non-negotiable.** 4.5:1 for body text (AA), 3:1 for large text and UI elements. See `ux-quality-checklist.md` for full accessibility requirements.
- **Color is never the sole indicator.** Every color-coded state must also use icon, text, or pattern to convey meaning (colorblind accessibility).
- **Avoid fintech clichés.** "Trust blue" is the fintech equivalent of clip art. CarrotFin's palette should feel distinctive — possibly drawing from unexpected palettes that still convey financial competence (warm earth tones, sophisticated darks, jewel accents).

---

## 4. Motion Language

### Philosophy

Motion in CarrotFin serves the adaptive composition paradigm. When the AI assembles a surface from components, the *way* those components appear is as important as *what* appears. Motion communicates the AI's intelligence — the user should feel that the interface was *composed for them*, not loaded from a template.

### High-Impact Moments (Invest Animation Budget Here)

| Moment | What Motion Communicates | Intent |
|---|---|---|
| **Surface composition** — AI assembles the home/generative screen | "I built this for you right now" | Staggered component reveals (30-50ms per item), spatial flow suggesting intentional ordering |
| **Conversation → Visual pivot** — AI response transitions from text to chart/card | "Let me show you what I mean" | Smooth materialization: visual element grows/fades into place below conversational text |
| **Goal milestone** — user hits a savings target | Celebration, positive reinforcement | Brief, joyful micro-animation. Restraint is key — one well-timed burst, not confetti everywhere |
| **Recomposition** — AI updates the surface based on new context | "Things have changed, here's what matters now" | Shared element transitions for components that persist, crossfade for swapped components |

### Timing & Physics

- **Micro-interactions:** 150-300ms. Quick enough to feel responsive, slow enough to be perceived.
- **Complex transitions:** ≤400ms. Surface recomposition, screen transitions.
- **Easing:** Ease-out for entering (decelerating into place), ease-in for exiting (accelerating out of view). Spring/physics-based curves preferred for natural feel.
- **Exit faster than enter:** Exit animations at ~60-70% of enter duration. Makes the interface feel responsive — what's leaving should get out of the way quickly.
- **Stagger sequences:** 30-50ms per item for list/grid entrances. Avoids the "everything at once" dump.
- **Interruptible always:** User tap/gesture cancels any in-progress animation immediately. Never block interaction during animation.

### Rules

- **Motion must be meaningful.** Every animation expresses a cause-effect relationship. Decorative-only animation is banned.
- **Respect reduced motion.** Users who set reduced motion preferences must get a fully functional, non-animated experience. Content must be readable immediately without waiting for entrance animations.
- **Max 1-2 animated elements per view at any moment.** Simultaneous animations compete for attention and feel chaotic.
- **No layout-shifting animation.** Use transform/opacity for position changes. Animation must never cause content to reflow or jump.
- **Mobile-first motion budget.** Animations must not cause frame drops on mid-range Android devices (the Indian market modal device). Performance is a hard constraint on visual ambition.

---

## 5. Spatial Composition Intent

### For Generative Surfaces

CarrotFin's generative screens are AI-composed — the spatial layout is *computed*, not hand-placed. But the composition grammar needs spatial principles:

- **Visual hierarchy through size and spacing, not color alone.** The highest-priority component should be spatially dominant (larger, more breathing room).
- **Generous negative space as a design choice.** For overwhelmed or novice users, whitespace is not wasted space — it's the visual equivalent of a calm voice. The AI should compose sparser layouts for users who need breathing room.
- **Anchor consistency:** Primary action button location, AI avatar/persona element, and navigation affordance should maintain spatial position across sessions. The user should always know where to look for "what should I do?" even when everything else adapts.
- **Spatial continuity between modes:** When the conversation hands off to a visual surface (or vice versa), the transition should feel like the same space reconfiguring, not a page switch.

### For Conversational Surfaces

- **Vertical flow with embedded visual breakpoints.** The conversation stream flows vertically, but inline charts, cards, and forms create visual "rest stops" that break the monotony of text.
- **Inline elements inherit conversational rhythm.** A chart that appears mid-conversation should feel like part of the dialogue's pacing, not a foreign embed. Margin, timing, and entrance animation should match the conversational flow.

---

## 6. Anti-Generic Mandate

> **CarrotFin must not look like a fintech app built by an AI.**

### Explicit Anti-Patterns

| Anti-Pattern | Why It Fails | What to Do Instead |
|---|---|---|
| Purple/blue gradient on white background | Default "fintech" aesthetic. Every AI-generated landing page looks like this | Develop a distinctive palette that surprises — can be warm, dark, earthy, jewel-toned. Anything with intentionality |
| Inter/Roboto/system fonts as design choice | Signals "we used the default." These are fine as *fallbacks*, not as *identity* | Choose typefaces that have character. The typography should feel curated |
| Card grids with equal spacing | "Dashboard as bento box" is the most overused finance app pattern | Adaptive composition means variable hierarchy. Let the important thing be *visually dominant*, not equal-sized |
| Generic chart colors (red/green for loss/gain) | Accessibility issue + visual cliché | Use the semantic palette with distinctive treatment — not every finance app needs to be a trading terminal |
| Flat icons + solid buttons + rounded corners everywhere | The "friendly fintech" starter kit | Develop a distinctive interaction vocabulary. What do *CarrotFin's* interactive elements look and feel like? |

### The Differentiation Test

For any visual design choice, ask: **"Would this look at home in 3+ existing finance apps?"** If yes, it fails. CarrotFin's adaptive composition creates a fundamentally different *interaction* pattern — the visual identity must match that ambition.

---

*This file defines visual intent. Implementation choices — specific font families, color hex values, animation libraries — are dev workspace decisions. BuildSpec documents should carry these principles as design constraints, not code.*
