# UX Quality Checklist

> **Skill type:** Design intent resource  
> **Invoked by:** `/mockup`, `/ux-designer`, `/buildspec` (on-demand)  
> **Last updated:** 2026-04-16  
> **Scope:** Design intent only — no technology or implementation prescriptions  
> **Enforcement:** Soft reference. Agents read and apply when designing frontend-touching artifacts. Not yet a hard gate for BuildSpec compilation.

---

## Purpose

Curated UX quality rules that every frontend-touching design artifact should address. These are experience expectations, not implementation specs. When a component pattern, journey definition, or BuildSpec is created, the relevant rules here inform what the experience must deliver.

**Sourced from:** Industry-standard UX best practices (Apple HIG, Material Design, WCAG), curated and adapted for CarrotFin's mobile-first, adaptive, AI-native paradigm.

**How to use:** Scan the categories relevant to what you're designing. Not every rule applies to every surface. Use judgment — but if you're skipping a CRITICAL rule, document why.

---

## 1. Accessibility (CRITICAL)

These are non-negotiable. CarrotFin targets the Indian market — diverse devices, variable screen sizes, users who may rely on assistive technology.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| A-01 | **Color contrast** | ≥4.5:1 for body text, ≥3:1 for large text and UI elements (WCAG AA) | Financial data must be readable in all lighting conditions — users check their money on cheap phones in bright sunlight |
| A-02 | **Color not sole indicator** | Every color-coded state (success, warning, error) must include icon or text | ~8% of Indian males are colorblind. Red/green for gain/loss without secondary indicators excludes them |
| A-03 | **Dynamic type support** | UI must accommodate system text size scaling without layout breakage or truncation | Older users, users with vision impairments. India's demographic spread is wide |
| A-04 | **Reduced motion** | Respect `prefers-reduced-motion`. Animations disable or reduce. Content remains accessible immediately | Vestibular disorders, user preference. Adaptive composition animations must degrade gracefully |
| A-05 | **Screen reader compatibility** | Meaningful accessibility labels for all interactive elements. Logical reading order matches visual order | Screen reader users must be able to navigate AI-composed surfaces as coherently as sighted users |
| A-06 | **Focus management** | After form submission error, auto-focus the first invalid field. Tab/focus order matches visual order | Conversational inline forms are especially tricky — focus must follow the dialogue flow |
| A-07 | **Heading hierarchy** | Sequential heading levels (h1→h2→h3), no level skipping | AI-composed surfaces generate heading structures dynamically — the composition logic must enforce hierarchy |
| A-08 | **Escape routes** | Cancel/back affordance in every modal, sheet, and multi-step flow | Users must never feel trapped in a conversational thread or inline flow |
| A-09 | **Error clarity** | Error messages state cause + how to fix (not "Invalid input") | Financial forms are high-stakes — unclear errors create anxiety and abandonment |
| A-10 | **Aria-live for errors** | Form errors announced to screen readers via live regions | Inline validation in conversational flows must be screen-reader-aware |

---

## 2. Touch & Interaction (CRITICAL)

Mobile-first means touch-first. These rules directly affect whether the app feels usable or frustrating on a phone.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| T-01 | **Touch target size** | Minimum 44×44pt (iOS) / 48×48dp (Android). Extend hit area beyond visual bounds if needed | Financial data often has small elements (chart data points, table rows). Must be tappable |
| T-02 | **Touch spacing** | Minimum 8dp gap between adjacent touch targets | Adaptive surfaces with variable component density must maintain spacing regardless of how many components the AI places |
| T-03 | **Tap feedback** | Visual feedback within 100ms of tap (opacity change, ripple, scale) | Responsiveness builds trust. In a finance app, "did my tap register?" uncertainty is toxic |
| T-04 | **No hover dependency** | Primary interactions via tap/press only. Hover is enhancement, never requirement | Mobile-first product. Hover states are dev-workspace enhancements for future web, not primary design |
| T-05 | **Gesture conflict avoidance** | No horizontal swipe on main content that conflicts with system back gesture. One primary gesture per region | AI-composed surfaces must not place swipeable elements where they conflict with OS navigation |
| T-06 | **Safe area awareness** | No interactive elements under notch, Dynamic Island, or gesture bar | Indian market: extremely diverse Android device form factors with varying safe area insets |
| T-07 | **Haptic feedback** | Use haptic for confirmations and significant actions; avoid overuse | Financial confirmations (SIP started, goal created) benefit from tactile reinforcement |
| T-08 | **No precision required** | Avoid requiring pixel-perfect taps on small icons or thin edges | Chart interactions, slider adjustments, and inline form elements must be forgiving |

---

## 3. Motion & Animation (HIGH)

Motion is how CarrotFin communicates the intelligence of its adaptive composition. These rules ensure it feels polished, not janky.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| M-01 | **Duration timing** | Micro-interactions: 150-300ms. Complex transitions: ≤400ms. Never >500ms | Adaptive surface composition must feel snappy. Slow animations make AI composition feel like loading |
| M-02 | **Easing** | Ease-out for entering, ease-in for exiting. Spring/physics-based preferred | Natural-feeling motion builds subconscious trust in the AI's compositions |
| M-03 | **Exit faster than enter** | Exit animations ~60-70% of enter duration | When the AI recomposes a surface, departing components should leave quickly |
| M-04 | **Stagger sequences** | 30-50ms per item for list/grid entrances | Component-by-component reveal during AI composition communicates "assembled for you" |
| M-05 | **Interruptible** | User tap/gesture cancels any in-progress animation immediately | Never block financial actions waiting for an animation to complete |
| M-06 | **No blocking animation** | UI remains interactive during all animations | Critical: users must be able to act on a CTA even while surrounding components are still animating in |
| M-07 | **Shared element transition** | Components that persist across recompositions use shared element transitions for visual continuity | When AI updates a surface, persisting cards should animate smoothly to new positions, not teleport |
| M-08 | **Layout shift avoidance** | Animations use transform/opacity only. No reflow-causing property animations | Financial data must remain spatially stable. Numbers jumping around erodes trust |

---

## 4. Forms & Inline Data Collection (HIGH)

CarrotFin collects data conversationally, not via form dumps. These rules shape that experience.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| F-01 | **Progressive disclosure** | Reveal complexity progressively. Never overwhelm with all options upfront | Core to CarrotFin's conversational data collection model. Each question builds on the last |
| F-02 | **Visible labels** | Every input has a persistent visible label. Placeholder text is not a label | Inline form elements within conversations must be self-explanatory even after the AI's contextual text scrolls up |
| F-03 | **Inline validation** | Validate on blur, not on keystroke. Show error only after user finishes input | Financial inputs (amounts, PAN numbers) are especially sensitive to premature validation |
| F-04 | **Error placement** | Error messages appear directly below the relevant field with recovery guidance | In conversational flows, errors must feel like the AI's gentle correction, not a system alert |
| F-05 | **Input type matching** | Use semantic input types (number, tel, email) to trigger correct mobile keyboard | Financial inputs: numeric keyboard for amounts, tel pad for phone/Aadhaar. Reduces friction significantly |
| F-06 | **Multi-step progress** | Multi-step conversational flows show progress indicator and allow back navigation | Users must know how far along they are in onboarding, goal setup, or any multi-turn data collection |

---

## 5. Navigation in an AI-Driven Context (HIGH)

> **CarrotFin's navigation is emergent, not menu-driven.** The AI guides the user to what matters. These rules apply to the navigational affordances that exist alongside the AI-driven flow.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| N-01 | **Predictable back behavior** | Back navigation is consistent and preserves scroll/state | When the AI has guided a user through a conversational thread, "back" must return to a sensible prior state — not break the conversation |
| N-02 | **Deep linking** | All key screens reachable via deep link for notifications and sharing | Ambient layer nudges (push notifications) must deep-link into the relevant AI-composed surface |
| N-03 | **State preservation** | Navigating away and returning restores previous position, filters, and input | If a user leaves mid-conversation to check something, the conversation state must persist |
| N-04 | **Modal escape** | Modals/sheets always have a clear close affordance. Swipe-down to dismiss on mobile | Inline detail views, explanatory overlays within AI-composed surfaces must be easily dismissible |

---

## 6. Performance as Experience (HIGH)

Framed as experience expectations, not implementation specs. The dev workspace decides *how* to achieve these.

| ID | Rule | Standard | Why It Matters for CarrotFin |
|---|---|---|---|
| P-01 | **Skeleton screens for AI composition** | Show skeleton/shimmer placeholders during AI surface assembly. Never a blank screen or spinner | The moment between "AI is deciding what to show" and "surface renders" must feel like anticipation, not loading |
| P-02 | **Progressive loading** | Content appears incrementally as AI generates it. Don't wait for complete composition before showing anything | Conversational responses that include visual elements should stream — text first, then visual materializes |
| P-03 | **Main thread responsiveness** | UI remains responsive to touch during AI computation and network requests | Users checking finances during commute on crowded trains won't tolerate a frozen interface |
| P-04 | **Network degradation** | Provide meaningful degraded experience on slow networks. Reduce animation, lower-res assets, cached data | India's mobile network quality is highly variable. 2G/3G must be survivable |

---

## Pre-Design Review

Before finalizing any component pattern, journey definition, or mockup that involves frontend surfaces, scan this abbreviated checklist:

- [ ] **Accessibility:** Contrast ratios considered? Color not sole indicator? Screen reader flow defined?
- [ ] **Touch:** All interactive elements ≥44pt? Tap feedback specified? No gesture conflicts?
- [ ] **Motion:** Animation serves a purpose? Timing in 150-300ms range? Interruptible? Reduced motion considered?
- [ ] **Forms:** Progressive disclosure applied? Labels visible? Validation strategy defined?
- [ ] **Navigation:** Back behavior specified? Deep linking possible? State preservation considered?
- [ ] **Performance:** Skeleton state defined for AI composition? Progressive loading strategy? Network degradation plan?

---

*This checklist is a soft reference — agents consult it when relevant. It may be promoted to a hard gate for BuildSpec compilation once the end-to-end pipeline is tested.*
