## 2026-07-11T09:21:58Z
Write the guardrails evaluation report to:
/Users/kshekhaw/Documents/CarrotFin_strategy/strategy/2026-07-11-guardrails-evaluation.md

The report must contain the following content exactly:

# Guardrails Evaluation: Text-Only Compatibility & Optimization

> **Created:** 2026-07-11  
> **Status:** Final  
> **Related assumptions:** C4, C5, C6, EF-16  
> **Related decisions:** none  

---

## Executive Summary
This report evaluates the shared `guardrails_v1.xml` module against the three core prompt files: `pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`, and `flash_conversation_v1.xml`. It identifies critical gaps in companion safety, trust gating misalignment, privacy exposure, and legacy voice assumptions, and outlines concrete text-only adaptations and modular extractions to optimize token footprint and architectural coherence for CarrotFin's MVP.

---

## 1. First-Principles Compatibility Evaluation

### Advisory Boundaries
- **Status:** Partially Compatible.
- **Analysis:** `guardrails_v1.xml` mandates advisory-only recommendations (no specific AMC or fund names). This is integrated into both planners. However, `flash_conversation_v1.xml` (companion) does not import the guardrails module, allowing it to bypass these boundaries during direct user Q&A.

### Accuracy
- **Status:** Fully Compatible.
- **Analysis:** All three prompts correctly route arithmetic to the Deterministic Compute Engine (DCE). No inline LLM calculation is permitted.

### Risk Disclosure
- **Status:** Partially Compatible.
- **Analysis:** Planners correctly escalate risk presentation based on consequence level. The companion references guardrails for high-consequence items, but lacks the actual rule set in its context.

### Trust Architecture
- **Status:** Conflict Identified.
- **Analysis:** `guardrails_v1.xml` and `voice_v1.xml` define a three-tier trust model (`NEW`, `WARMING`, `TRUSTING`). In contrast, `pro_detail_planner_v1.xml` and `pro_overview_planner_v1.xml` reference binary trust levels (`Low trust`, `High trust`), creating misalignment in adaptive density limits (e.g., maximum field requests per step).

### Privacy
- **Status:** Gap Identified.
- **Analysis:** `guardrails_v1.xml` forbids echoing exact sensitive values. The companion adheres to this. However, `pro_detail_planner_v1.xml` lacks echoing constraints in its `<constraints>` block, risking sensitive data exposure in `responseText` outputs for compute steps.

---

## 2. Text-Only MVP Adaptation & Solutions

### Voice-Specific Assumptions & Gaps
1. **Legacy Terminology:** Consuming prompts refer to "voice", "hears", and "reads/hears" (e.g., `flash_conversation_v1.xml` line 38: "the face, the voice"; line 82: "reads/hears").
2. **Audio Triggers:** `voice_v1.xml` references emotional state triggers (e.g., ANXIOUS → "slower, gentler") which are audio-centric and do not optimize for text-only layouts.
3. **Fallback Logic:** `guardrails_v1.xml` contains "text-input fallback" instructions that are redundant for a text-only interface.

### Solutions
- **Rename & Adapt Voice Module:** Rename `modules/voice_v1.xml` to `modules/persona_v1.xml`. Update all consuming imports.
- **Terminology Purge:** Standardize "reads/hears" to "reads". Replace references to "voice" with "persona".
- **Visual Adaptation Triggers:** Map the ANXIOUS emotional state in `modules/persona_v1.xml` to text layout instructions: "use shorter paragraphs, bullet points, and high whitespace density to reduce visual cognitive load".
- **Refactor Fallback Instructions:** In `guardrails_v1.xml`, change "text-input fallback" to "masked security inputs or bypass option".

---

## 3. Module Optimization Opportunities

### Shared Module Extraction
1. **Goal Crystallization Protocol:** Extract the identical goal lifecycle logic from §7 of `pro_detail_planner_v1.xml` and §9 of `flash_conversation_v1.xml` into a new shared module `modules/goals_context_v1.xml` under a `<goalsBehavioralRules>` tag.
2. **Unified Adaptive Mapping:** Consolidate the user profile behavioral mapping tables (mapping trust, literacy, and emotional state to layout/tone changes) from both planners and the voice module into `modules/persona_v1.xml`.

---

## 4. Gaps, Conflicts, & Actionable Solutions

| Gap / Conflict | Impact | Proposed Solution (Without Modifying XML Files) |
|---|---|---|
| Companion lacks guardrails module | Compliance/advisory boundary bypass | Inject `<moduleRef id="guardrails" version="1.0" path="modules/guardrails_v1.xml" />` into the assembly flow of `flash_conversation_v1.xml`. |
| Trust terminology misalignment | Inconsistent step field density | Standardize planners to use the three-tier trust model (`NEW`, `WARMING`, `TRUSTING`) instead of binary levels. |
| Detail Planner lacks privacy echo rules | Risk of sensitive data exposure | Append explicit no-echoing rules to the `<constraints>` block of `pro_detail_planner_v1.xml`. |
| Tone rules are audio-specific | Sub-optimal text layout for anxious users | Redefine tone rules in the newly renamed `persona_v1.xml` to drive layout elements (shorter paragraphs, whitespace density). |

---

## Considered & Rejected

- **Merge companion and planner prompts:** Rejected because the separation between interaction orchestration (Companion) and journey/step construction (Planners) is essential to maintain low latency and context size on `gemini-3-flash-preview`.
- **Implement a separate rule checking model:** Rejected because it introduces unnecessary cost, complexity, and latency for a zero-budget, two-founder pre-seed MVP.
- **Maintain voice module for future scalability:** Rejected because maintaining unused voice logic increases token overhead and confuses model parsing of text layouts.


Ensure you update your progress.md and send a confirmation message to your parent upon completion. Do not modify the original XML files under any circumstances.
