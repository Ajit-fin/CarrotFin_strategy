# BuildSpec — Compilation Checklist

> **Purpose:** Pre-finalization completeness and quality check for compiled BuildSpecs.  
> **Used by:** `build-spec-compiler` skill before presenting the BuildSpec for user review.  
> **Last updated:** 2026-04-15

---

## Self-Containment Checks

- [ ] No links to files the receiving agent can't access (workspace-internal paths)
- [ ] Every CarrotFin-specific term appears in the Glossary (§8)
- [ ] Product context (§1) is sufficient for an agent with zero CarrotFin knowledge to understand the paradigm
- [ ] All cross-references between sections are resolved (no "see above" without the content being present)

## Content Integrity Checks

- [ ] Every section notes its source artifact(s) with `[Source: filename.md]`
- [ ] No content was invented during compilation — everything traces to an existing artifact
- [ ] Behavioral guardrails from source artifacts are preserved at full strength (not softened or summarized away)
- [ ] Unresolved tensions are surfaced honestly (§7) — not hidden or prematurely "resolved"
- [ ] Assumptions this flow depends on are flagged with their current validation status

## Tech-Neutrality Checks

- [ ] No sentence prescribes a technology, framework, library, or language
- [ ] No sentence prescribes database schema, API design, or system architecture
- [ ] No sentence uses implementation-specific language ("endpoint", "microservice", "state management", "ORM")
- [ ] Every constraint is framed as a product/experience requirement, not a technical one
- [ ] The "how to build it" is entirely absent — only "what the experience should be" is present

## Completeness Checks

- [ ] §1 (Product Context) — all subsections populated
- [ ] §2 (Flow Overview) — flow phases map the full lifecycle, no gaps between phases
- [ ] §3 (Experience Specification) — every flow phase has all subsections (User Intent, AI Behavior, Screen Composition, Adaptive Variations, Conversational Integration, Behavioral Guardrails)
- [ ] §3 — at least 2 adaptive variations per phase (different user contexts)
- [ ] §4 (Product Constraints) — MUST, SHOULD, and MUSTNOT sections all populated
- [ ] §4.4 (Decision Boundaries) — at least 3 decision areas with clear freedom/escalation guidance
- [ ] §5 (Scope Boundary) — both in-scope and out-of-scope lists are explicit
- [ ] §6 (Verification Criteria) — all three verification types populated (acceptance, behavioral, anti-pattern)
- [ ] §7 (Unresolved Tensions) — reviewed tension-log.md for applicable tensions
- [ ] §8 (Glossary) — all domain terms defined

## Quality Checks

- [ ] Specificity: Concrete examples preferred over abstract statements throughout
- [ ] Actionability: The dev agent can derive implementation decisions from every section
- [ ] Anti-patterns: MUSTNOT section includes at least 3 substantive anti-patterns with rationale
- [ ] Adaptive behavior: Every component and phase specifies how it changes per user context
- [ ] Scope clarity: No gray zone between in-scope and out-of-scope — the boundary is sharp

## Final Gate

- [ ] **Self-containment test passed:** A fresh Antigravity agent given only this document could reason about the flow's product design without needing to ask product-level clarifying questions
- [ ] **User has reviewed and approved the BuildSpec**
