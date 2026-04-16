# BuildSpec Template — Flow Specification

> **Template type:** Flow — end-to-end user experience specification  
> **Usage:** Invoked by `build-spec-compiler` skill during compilation  
> **Last updated:** 2026-04-15

---

> **Instructions for compiler:** Replace all `[bracketed placeholders]` with content extracted from source artifacts per `extraction-rules.md`. Remove these instruction callouts before finalizing.

---

# BuildSpec: [Flow Name]

> Spec ID: BS-[NNN]  
> Version: 1.0.0 | Date: [YYYY-MM-DD] | Source: CarrotFin Strategy Workspace  
> Status: Draft  
> Upstream Artifacts: [comma-separated list of source artifact filenames this was compiled from]

---

## 1. Product Context

> This section provides the receiving agent with the minimum viable context about CarrotFin needed to build this flow correctly. The receiving agent has zero prior CarrotFin knowledge.

### 1.1 What CarrotFin Is

[3-5 sentences distilled from company-context.md. Cover: AI-native personal finance advisor for Indian consumers. The AI runs the experience — every screen, recommendation, interaction is AI-driven. Interface is composed at render time, not pre-designed. Conversation and visualization are one integrated paradigm, not separate surfaces.]

[Source: company-context.md]

### 1.2 Governing Design Axioms

[For each axiom from design-principles.md that governs this flow:]

**[Axiom Name]:** [One-sentence principle statement]

[One concrete example relevant to THIS flow, demonstrating what the axiom means in practice]

**Compliance test:** [The test statement from design-principles.md, applied to this flow]

[Source: design-principles.md]

### 1.3 Interaction Paradigm

[From interaction-model.md — only the surface types and flow modes this flow uses:]

**Surface types used in this flow:**
- [Surface type]: [Brief definition + when this flow uses it]

**Modality rules for this flow:**
| User Intent Phase | Leading Modality | Why |
|---|---|---|

**Handoff patterns:** [If this flow transitions between modalities, describe the pattern. If not, state "Single-modality flow — no handoffs."]

[Source: interaction-model.md]

---

## 2. Flow Overview

### 2.1 What This Flow Is

[One paragraph: the end-to-end experience. What user intent it serves. What the user's world looks like before this flow (the problem state) and after (the resolved state).]

[Source: journey-catalog/J[N]-*.md]

### 2.2 User Segments

[Segments from user-insights.md that this flow serves:]

| Segment | Key Characteristics | Behavioral Signals Relevant to This Flow |
|---|---|---|

[Source: user-insights.md]

### 2.3 Flow Phases

[High-level phase map. Each phase names: what the user is doing, what the AI is doing, approximate trust level required.]

```
Phase 1: [Name] — [User action] / [AI action] — Trust: [level]
    ↓
Phase 2: [Name] — [User action] / [AI action] — Trust: [level]
    ↓
...
Phase N: [Name] — [User action] / [AI action] — Trust: [level]
```

[Source: journey-catalog/J[N]-*.md]

---

## 3. Experience Specification

> Repeat this section structure for each flow phase.

### 3.1 [Phase Name]

#### User Intent
[What the user is trying to accomplish in this phase. Written from the user's perspective — not what the system does, but what the user wants.]

#### AI Behavior
[What the AI does in this phase:]
- Questions it asks (and why — what data it needs)
- Decisions it makes (what it selects, prioritizes, or filters)
- What it surfaces (information, recommendations, nudges)
- How it guides the user to the next phase

**Applicable behavioral principles:**
[From behavioral-framework.md — only the principles that govern AI behavior in this specific phase.]

- **[Principle name]:** [How it applies in this phase. Include the anti-pattern to avoid.]

#### Screen Composition

**Screen type:** [Generative / Hybrid / Static] — [Rationale from screen-taxonomy.md]

**Components on this surface:**
| Component | Purpose | Adaptive Behavior | Composition Rules |
|---|---|---|---|
| [Component name] | [What it shows/enables] | [How it changes per user context — reference CP[N] spec] | [Position, density, adjacency constraints] |

[Source: component-patterns/CP[N]-*.md, screen-taxonomy.md]

#### Adaptive Variations

[How this phase changes for different user contexts. Provide at least 2 concrete user profiles:]

**Variation A — [User Profile Label]:**
| Dimension | Value | Effect on This Phase |
|---|---|---|
| Financial literacy | [level] | [Specific change] |
| Trust level | [level] | [Specific change] |
| [Other relevant dimensions] | [value] | [Specific change] |

[Describe the concrete experience this user has in this phase — what they see, what the AI says, how dense the information is.]

**Variation B — [User Profile Label]:**
[Same structure as Variation A, with a meaningfully different user context.]

#### Conversational Integration

[Where conversation leads in this phase vs. where visualization leads:]

- **AI speaks:** [What the AI communicates conversationally — actual copy tone/direction, not verbatim scripts]
- **Visual elements:** [What visual elements appear — charts, cards, forms — and how they integrate with the conversation]
- **User response modes:** [How the user can respond — type, tap, select, adjust slider, etc.]

#### Behavioral Guardrails

[Hard behavioral constraints for this phase from behavioral-framework.md:]

- **Guardrail:** [What the AI MUST do / MUST NOT do in this phase]
- **Trust gate:** [What data the AI can request at this trust level, and what value it must deliver first]
- **Framing rule:** [How information should be framed — loss/gain/neutral — and why]
- **Nudge level:** [Maximum nudge intrusiveness appropriate for this phase's trust context]

---

## 4. Product Constraints

### 4.1 Hard Constraints (MUST)

[Non-negotiable product requirements. Violation = the experience is wrong.]

- **MUST-01:** [Product constraint — what the experience MUST do/be]
- **MUST-02:** [Product constraint]

### 4.2 Soft Constraints (SHOULD)

[Strong product preferences. Deviate only with documented reason.]

- **SHOULD-01:** [Product preference — what the experience SHOULD do/be]
- **SHOULD-02:** [Product preference]

### 4.3 Anti-Patterns (MUST NOT)

[Things that would make this the wrong experience. Include brief rationale.]

- **MUSTNOT-01:** [Anti-pattern] — *Rationale: [why this is wrong for CarrotFin]*
- **MUSTNOT-02:** [Anti-pattern] — *Rationale: [why]*

### 4.4 Decision Boundaries

[Where the dev agent has freedom to make product-adjacent decisions vs. where it must escalate.]

| Decision Area | Dev Agent Freedom | Escalate If |
|---|---|---|
| [Area] | [What the agent can decide on its own] | [Condition that requires escalation to product/strategy] |

---

## 5. Scope Boundary

### 5.1 This Spec Covers

[Explicit list of what's in scope for this build.]

- [In-scope item]

### 5.2 This Spec Does NOT Cover

[Explicit list — the dev agent must NOT build these, even if they seem related.]

- [Out-of-scope item] — *Rationale: [why excluded]*

### 5.3 Adjacent Flows

[Flows that connect to this one. Describe the boundary — where this flow ends and another begins.]

| Adjacent Flow | Relationship | Boundary |
|---|---|---|
| [Flow name] | [Predecessor / Successor / Parallel] | [Where this flow ends and that one begins] |

---

## 6. Verification Criteria

[How to verify the build matches product intent. These are experience-level checks.]

### 6.1 Experience Acceptance Criteria

- **ACCEPT-01:** When [user scenario], the experience should [expected behavior]
- **ACCEPT-02:** When [user scenario], the experience should [expected behavior]

### 6.2 Behavioral Verification

- **BV-01:** The AI must [behavioral expectation] when [context]
- **BV-02:** The AI must [behavioral expectation] when [context]

### 6.3 Anti-Pattern Verification

- **APV-01:** The experience must NOT [anti-pattern behavior]
- **APV-02:** The experience must NOT [anti-pattern behavior]

---

## 7. Unresolved Tensions

[Design tensions from tension-log.md that affect this flow. The dev agent should be aware of these — they represent genuine ambiguity in the product design.]

### [Tension ID]: [Tension Name]

**Force A:** [One side of the tension]  
**Force B:** [The opposing force]  
**Current direction:** [How the product is currently leaning, and why]  
**Impact on this flow:** [How this tension manifests in this specific flow]

---

## 8. Glossary

[CarrotFin-specific terms used in this spec. The receiving agent may not know these.]

| Term | Definition |
|---|---|
| Generative screen | An AI-composed screen where layout and components are assembled at render time based on user context — no canonical design exists |
| Composed surface | A full-screen interface assembled by the AI from components, without a conversational thread |
| User State Model | The multi-dimensional model of user context (financial literacy, risk tolerance, life stage, engagement mode, emotional state, trust level) that drives personalization |
| Adaptive composition | Interface assembly at render time by the AI from a palette of components and structural rules, based on user context |
| Trust level | Progressive trust status (New → Curious → Warming → Trusting → Dependent) that gates what data the AI can request and what advice authority it has |
| [Add flow-specific terms as needed] | [Definition] |
