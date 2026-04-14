# Frontend Design — Skill Router

> **Skill type:** Implementation patterns  
> **Invoked by:** `/ux-designer`, `/product`, `/mockup`  
> **Last updated:** 2025-04-15

---

## Purpose

This skill provides frontend implementation patterns and guidelines for CarrotFin's mobile-first, AI-native interface. It bridges the gap between UX design decisions (what the user should experience) and technical implementation (how to build it). Patterns here are informed by CarrotFin's adaptive UI architecture — every implementation pattern must account for dynamic composition, user context-driven rendering, and conversational integration.

## When to Use This Skill

| Scenario | Action |
|---|---|
| Evaluating a frontend technology choice | Check implementation patterns for constraints and recommendations. |
| Translating a UX design into implementation specs | Cross-reference component patterns from `ux-design-system/` with implementation patterns here. |
| Building a prototype or proof-of-concept | Use the PoC guidelines below for appropriate scope and fidelity. |
| Debating architecture (native vs. cross-platform, rendering approach) | Check technology considerations and constraints. |

## Directory Navigation

```
frontend-design/
├── SKILL.md              ← You are here — routing guide
├── patterns/             ← Implementation patterns
│   └── _index.md         ← Pattern inventory
├── technology/           ← Technology evaluation and constraints
│   └── _index.md         ← Technology inventory
└── resources/            ← Reference materials and guidelines
    └── _index.md         ← Resource inventory
```

## CarrotFin-Specific Implementation Constraints

These constraints distinguish CarrotFin's frontend from conventional app development:

### 1. Dynamic Composition Engine
The frontend must support AI-driven screen assembly at render time. This is NOT a template system. The AI selects components and their arrangement — the frontend renders whatever composition it receives.

**Implications:**
- Components must be independently renderable (no hard dependencies on sibling components)
- Layout must accept variable component counts and types
- A composition protocol defines how the AI communicates what to render

### 2. User Context as First-Class Input
Every component receives user context (literacy, risk, stage, mode, emotion, trust) and adapts its rendering. This is not theming — it's structural adaptation (different content, different density, different interaction patterns).

**Implications:**
- A context provider pattern propagates user state to all components
- Components define their own adaptive behavior (not controlled by a central theme)
- Performance: adaptive rendering must not cause perceptible layout shifts

### 3. Conversational-Visual Integration
The interface weaves conversational elements (AI messages, user responses) with visual elements (charts, cards, trackers). These are not separate views — they coexist on the same surface.

**Implications:**
- A unified rendering pipeline handles both message-type and visual-type elements
- Rich inline components (charts, forms, sliders) work within conversational flow
- Streaming support: the AI's response may include visual elements that render progressively

### 4. Mobile-First, But Not Mobile-Only
Primary target: mobile (iOS/Android). The architecture should not preclude future web/tablet surfaces, but mobile UX is the design target.

## PoC Guidelines

For pre-funding stage, prototypes should be:
- **High-fidelity interaction, low-fidelity visual** — demonstrate the adaptive behavior and conversational integration, not pixel-perfect styling.
- **Scoped to one journey** — a single end-to-end user journey that proves the paradigm works.
- **Real AI, not mocked** — even early prototypes should use actual AI composition to demonstrate the adaptive UX thesis.

## Rules

- **Design system alignment** — every implementation pattern must trace back to a UX design system pattern in `ux-design-system/`. Implementation without design rationale is drift.
- **Adaptive behavior is non-negotiable** — no component may be implemented without its adaptive behavior spec. Static-only implementations are tech debt from day one.
- **Mobile performance is a constraint** — every pattern must consider mobile performance (rendering speed, memory, battery). Adaptive doesn't mean slow.
- **Technology decisions as decision records** — major technology choices (framework, rendering approach, state management) warrant a record in `decision-log/`.
