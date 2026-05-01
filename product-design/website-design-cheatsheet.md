# CarrotFin Website — Design Cheat Sheet

> **Use during code generation.** Full rationale → `website-design-system.md`. Tokens → `tokens.css`.

## Fonts

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=Newsreader:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
```

Headlines = `Newsreader` (serif). Body/Labels = `Inter` (sans-serif).

## Type Scale (desktop → mobile)

| Class | Font | Size | Weight | Line Height |
|---|---|---|---|---|
| display-lg | Newsreader | 57→36px | 500 | 64px |
| display-md | Newsreader | 45→28px | 500 | 52px |
| display-sm | Newsreader | 36→24px | 400 | 44px |
| headline-lg | Newsreader | 32px | 500 | 40px |
| headline-md | Newsreader | 28px | 400 | 36px |
| headline-sm | Newsreader | 24px | 400 | 32px |
| title-lg | Inter | 22px | 600 | 28px |
| title-md | Inter | 16px | 600 | 24px |
| title-sm | Inter | 14px | 500 | 20px |
| body-lg | Inter | 18→17px | 400 | 28px |
| body-md | Inter | 16→15px | 400 | 24px |
| body-sm | Inter | 14px | 400 | 20px |
| label-lg | Inter | 14px | 500 | 20px |
| label-md | Inter | 12px | 500 | 16px |
| label-sm | Inter | 11px | 500 | 16px |

₹ amounts: `font-variant-numeric: tabular-nums`.

## Glassmorphism Recipes

```css
/* Level 1 — cards at rest */
.glass-1 {
  background: var(--color-surface-1);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid var(--color-border);
  border-radius: var(--shape-xl);
}

/* Level 2 — hover / active */
.glass-2 {
  background: var(--color-surface-2);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid var(--color-border-hover);
  border-radius: var(--shape-xl);
}

/* Level 3 — modals */
.glass-3 {
  background: var(--color-surface-3);
  backdrop-filter: blur(32px);
  -webkit-backdrop-filter: blur(32px);
  border: 1px solid var(--color-border-hover);
  border-radius: var(--shape-xl);
}

/* Fallback for no backdrop-filter support */
@supports not (backdrop-filter: blur(1px)) {
  .glass-1 { background: rgba(8, 20, 37, 0.92); }
  .glass-2 { background: rgba(8, 20, 37, 0.95); }
  .glass-3 { background: rgba(8, 20, 37, 0.97); }
}
```

## Component Quick-Ref

### Buttons

```css
.cf-btn-primary {
  background: var(--color-primary);
  color: var(--color-on-primary);
  border: none;
  border-radius: var(--shape-full);
  height: 48px;
  padding: 0 var(--spacing-lg);
  font: 500 14px/20px var(--font-body);
  cursor: pointer;
  transition: transform var(--duration-short) var(--ease-standard),
              box-shadow var(--duration-short) var(--ease-standard);
}
@media (hover: hover) {
  .cf-btn-primary:hover {
    transform: scale(1.02);
    box-shadow: var(--glow-orange);
  }
}
.cf-btn-primary:active { transform: scale(0.98); }
.cf-btn-primary:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
.cf-btn-primary:disabled { opacity: 0.38; pointer-events: none; }

.cf-btn-tonal {
  background: var(--color-surface-2);
  color: var(--color-on-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--shape-full);
  height: 44px;
  padding: 0 var(--spacing-lg);
  font: 500 14px/20px var(--font-body);
}

.cf-btn-outlined {
  background: transparent;
  color: var(--color-secondary);
  border: 1px solid var(--color-border);
  border-radius: var(--shape-full);
  height: 44px;
  padding: 0 var(--spacing-lg);
  font: 500 14px/20px var(--font-body);
}
```

### Cards

| Variant | BG | Border | Radius | Hover |
|---|---|---|---|---|
| Glass | `surface-1` + blur(12px) | `--color-border` | `--shape-xl` | Level 2 + border glow |
| Accent | `surface-1` | left 3px `--color-primary` | `--shape-xl` | — |
| Outlined | `surface-0` | `--color-border` | `--shape-lg` | — |

### Nav Bar

Floating pill: `glass-2` + `shape-full` + fixed top + 16px margin. Height 56px (desktop), 48px (mobile).

### Chips

Pill shape, 36px height. Unselected: `surface-1` bg + `--color-border`. Selected: `--color-primary` bg + white text.

### Text Fields

12px radius, 56px height, `surface-0` bg. Border: `--color-border` → `--color-primary` on focus. Label: `--color-on-surface-muted`.

### Toast

Glass pill (`glass-2` + `shape-full`). Bottom center (mobile), bottom right (desktop). 3s auto-dismiss.

## Section Backgrounds

| Section | Background |
|---|---|
| Hero | `--color-bg` + `radial-gradient(ellipse at 50% 0%, rgba(255,107,53,0.08), transparent 60%)` |
| Alternating | `--color-bg` ↔ `--color-surface-0` |
| Waitlist | `--color-surface-1` + `radial-gradient(ellipse at 50% 100%, rgba(255,107,53,0.12), transparent 50%)` |
| Footer | `--color-surface-0` |

## Layout

| Container | Max-Width |
|---|---|
| Page | 1240px |
| Content | 1080px |
| Reading | 680px |
| Tool widget | 580px |
| Form | 480px |

Breakpoints: `<600px` compact · `600-904px` medium · `905-1240px` expanded · `>1240px` large.

## Motion

```css
transition: transform var(--duration-short) var(--ease-standard);   /* hover */
transition: all var(--duration-medium) var(--ease-emphasized);       /* card depth */
transition: all var(--duration-long) var(--ease-emphasized-decel);   /* section reveal */
```

Scroll reveal: `fade-in + translateY(20px)`, staggered 100ms. Use IntersectionObserver. `transform`/`opacity` only. Respect `prefers-reduced-motion`.

## Accessibility

- Focus: `outline: 2px solid var(--color-primary); outline-offset: 2px`
- Touch targets: min 48×48px (mobile), 44×44px (desktop)
- `#FF6B35` on `#081425`: 4.6:1 — large text only. Use white for body text.
- Hover states: `@media (hover: hover)` only
