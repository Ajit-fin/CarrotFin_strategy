# BuildSpec — Extraction Rules

> **Purpose:** Defines how to extract and distill content from workspace files into BuildSpec sections.  
> **Used by:** `build-spec-compiler` skill during Phase 4 compilation  
> **Last updated:** 2026-04-15

---

## General Extraction Principles

1. **Relevance filter:** For each source file, extract ONLY the content that directly governs the flow being specified. "Directly governs" means: removing this content would change how the flow should behave or what the user should experience.

2. **Distillation, not truncation:** Don't just grab the first N lines. Understand the full source and synthesize the relevant parts into concise, accurate statements. The distilled version should be faithful to the source's intent.

3. **Preserve specificity:** Concrete examples ("A 25-year-old first-time investor sees a single savings goal with encouragement copy") are more valuable than abstractions ("The interface adapts to user context"). Prefer examples that match the flow being specified.

4. **Resolve cross-references:** If a source file references another file (e.g., "see behavioral-framework.md Part 6"), follow the reference and embed the relevant content. Don't leave dangling references the receiving agent can't access.

5. **Preserve nuance on constraints:** Hard constraints ("mathematical accuracy is non-negotiable") must be conveyed with full force. Don't soften guardrails during distillation.

---

## Source-by-Source Extraction Guide

### `knowledge-base/company-context.md`
**Maps to:** BuildSpec §1.1 (What CarrotFin Is)

| Extract | Skip |
|---|---|
| Product identity (AI-native personal finance advisor, not a robo-advisor) | Team details, current stage metrics |
| What CarrotFin is NOT (the anti-patterns) | Relationship to InsurEasy history |
| Key constraints that affect this flow (e.g., "no direct user data yet") | Existing product descriptions |

**Distillation target:** 3-5 sentences that give the receiving agent enough context to understand the product paradigm.

---

### `knowledge-base/design-principles.md`
**Maps to:** BuildSpec §1.2 (Governing Design Axioms)

| Extract | Skip |
|---|---|
| Axioms that directly govern this flow's design | Axioms that don't affect this flow |
| Concrete examples relevant to the flow | Conflict resolution hierarchy (unless this flow triggers a conflict) |
| The "test" statement for each relevant axiom | Scope notes and meta-commentary |

**Distillation target:** For each relevant axiom — the principle statement, one concrete example relevant to this flow, and the test for compliance.

---

### `product-design/ux-philosophy.md`
**Maps to:** BuildSpec §1 (Context) and §3 (Experience Spec — adaptive behavior)

| Extract | Skip |
|---|---|
| User State Model dimensions relevant to this flow | Full thesis narrative |
| "What adaptive means concretely" examples relevant to this flow | Comparison tables with competitors |
| The Data → Intelligence → Interface pipeline (brief) | "What we don't know yet" (covered in tensions) |

**Distillation target:** The user state dimensions this flow adapts against, with concrete behavior examples.

---

### `product-design/interaction-model.md`
**Maps to:** BuildSpec §1.3 (Interaction Paradigm) and §3 (Conversational Integration)

| Extract | Skip |
|---|---|
| Surface types this flow uses (stream, composed surface, ambient) | Surface types not used in this flow |
| Flow mode decisions relevant to this flow (from the modality table) | Full modality handoff principles (unless this flow has handoffs) |
| Conversational design principles that apply | Scenarios not related to this flow |

**Distillation target:** Which surface types this flow uses, when conversation leads vs. visualization leads, and handoff patterns if applicable.

---

### `product-design/screen-taxonomy.md`
**Maps to:** BuildSpec §3 (Screen Composition per phase)

| Extract | Skip |
|---|---|
| Screen type definitions for the types used in this flow | Screen types not used |
| Composition rules that apply (max components, priority ordering, anchor elements) | Decision framework (the flow's journey definition should already have decided) |
| Constraints relevant to the flow (cognitive load thresholds, CTA limits) | Full examples for other flows |

**Distillation target:** For each phase — the screen type, why that type was chosen, and which composition rules apply.

---

### `product-design/behavioral-framework.md`
**Maps to:** BuildSpec §3 (Behavioral Guardrails per phase) and §4 (Product Constraints)

This is the largest source file (~500 lines). Selective extraction is critical.

| Extract | Skip |
|---|---|
| **Part 1 (Behavioral Principles):** Only the biases that directly affect this flow | Biases not relevant to this flow |
| **Part 2 (AI Decision Logic):** Context triggers relevant to this flow | Context triggers for unrelated scenarios |
| **Part 2 (Nudge Taxonomy):** Nudge levels appropriate for this flow's trust context | Full taxonomy (just reference relevant levels) |
| **Part 3 (Adaptive Rigor):** Guardrails that apply + calibration heuristics for this flow | Full rigor framework (distill to applicable rules) |
| **Part 4 (Content Framing):** Framing principles relevant to this flow's content | Framing examples for other contexts |
| **Part 5 (Verification Anchors):** If this flow involves AI-generated calculations | Full verification system (if not applicable) |
| **Part 6 (Trust Architecture):** Trust level requirements for this flow's phases | Full trust ramp (just the levels and gates relevant to this flow) |

**Distillation target:** A focused behavioral brief — the principles, guardrails, and trust gates that govern this specific flow's AI behavior.

---

### `product-design/journey-catalog/J[N]-*.md`
**Maps to:** BuildSpec §2 (Flow Overview) and §3 (Experience Specification) — PRIMARY SOURCE

This is the primary source artifact. The journey definition IS the flow being compiled.

| Extract | Skip |
|---|---|
| Everything — this is the core source | Nothing — but reformat to match BuildSpec structure |

**Distillation target:** The full journey definition, restructured into the BuildSpec's phase-by-phase format with embedded context from other sources.

---

### `product-design/component-patterns/CP[N]-*.md`
**Maps to:** BuildSpec §3 (Screen Composition per phase)

| Extract | Skip |
|---|---|
| Component purpose, adaptive behavior spec, composition rules | Internal design iteration notes |
| Interaction affordances | Implementation suggestions (if any) |
| User context inputs and rendering rules | Cross-references to other components not in this flow |

**Distillation target:** For each component used in this flow — what it does, how it adapts, and where it can appear.

---

### `product-design/design-decisions/DD-*.md`
**Maps to:** BuildSpec §3 (referenced inline) and §4 (Constraints)

| Extract | Skip |
|---|---|
| Decision and rationale relevant to this flow | Full options evaluation |
| Reversal trigger (what would change this decision) | Historical context |

**Distillation target:** Brief statement of the decision and its rationale, inline where applicable.

---

### `research/*.md`
**Maps to:** BuildSpec §3 (Behavioral Guardrails) and §4 (Product Constraints)

| Extract | Skip |
|---|---|
| Findings that became product constraints for this flow | Research methodology |
| Financial rigor rules applicable to this flow | Findings not relevant to this flow |
| UX pattern research that informed this flow's design | Raw data, intermediate analysis |

**Distillation target:** Distilled findings as product constraints, with source attribution.

---

### `knowledge-base/user-insights.md`
**Maps to:** BuildSpec §2.2 (User Segments)

| Extract | Skip |
|---|---|
| Segments this flow serves (from the segment table) | Segments this flow doesn't target |
| Behavioral insights relevant to this flow (from B1-B3) | Detailed segment characteristics beyond what affects this flow |
| Unmet needs this flow addresses | Full "user data we don't have" section |

**Distillation target:** Which segments, their relevant characteristics, and the behavioral signals that inform this flow's design.

---

### `knowledge-base/assumptions-tracker.md`
**Maps to:** BuildSpec §7 (Unresolved Tensions) and §6 (Verification Criteria)

| Extract | Skip |
|---|---|
| Assumptions this flow depends on (by ID) with status | Assumptions unrelated to this flow |
| The dependency chain if this flow sits on it | Growth/distribution assumptions (unless relevant) |

**Distillation target:** A brief list of assumptions this flow depends on, with current validation status.

---

### `product-design/tension-log.md`
**Maps to:** BuildSpec §7 (Unresolved Tensions)

| Extract | Skip |
|---|---|
| Tensions that affect this flow's design | Tensions unrelated to this flow |
| Both forces, current resolution direction | Full "what would resolve it" (that's for this workspace, not the dev agent) |

**Distillation target:** Each relevant tension stated concisely with current direction.

---

## Post-Extraction Checklist

After extraction, verify:

- [ ] No section contains a link to a file the receiving agent can't access
- [ ] No section uses CarrotFin jargon without a glossary entry
- [ ] No section prescribes technology, architecture, or implementation approach
- [ ] Every section notes its source artifact(s)
- [ ] No section contains content invented during compilation (not from a source)
- [ ] All behavioral guardrails from the source are preserved at full strength (not softened)
