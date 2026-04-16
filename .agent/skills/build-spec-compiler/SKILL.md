# Build Spec Compiler — Skill Router

> **Skill type:** Artifact compilation  
> **Invoked by:** `/buildspec`  
> **Last updated:** 2026-04-15

---

## Purpose

This skill assembles self-contained **BuildSpec** artifacts from existing workspace artifacts. A BuildSpec is a product intent specification — it defines WHAT the user experience should be and WHY, optimized for consumption by an external LLM agent that has zero CarrotFin context.

The receiving agent uses the BuildSpec as input to its own HLD→LLD process. The BuildSpec never prescribes technology, architecture, or implementation approach.

## When to Use This Skill

| Scenario | Action |
|---|---|
| `/buildspec` workflow reaches Phase 4 (Compilation) | Invoke this skill to assemble the BuildSpec from source artifacts |
| Verifying a compiled BuildSpec for completeness | Run the compilation checklist in `templates/_compilation-checklist.md` |
| Understanding what to extract from a specific KB file | Reference `extraction-rules.md` |

## When NOT to Use This Skill

| Scenario | What to Do Instead |
|---|---|
| The source artifacts don't exist yet (no journey defined, no components specced) | Run upstream workflows first (`/ux-designer`, `/product`, `/research`) |
| You need to make a design decision | Use `/ux-designer` or `/product` — this skill compiles decisions, doesn't make them |
| You need financial domain research | Use `/research` — this skill embeds research findings, doesn't conduct research |
| You need to prescribe technical implementation | Don't. The BuildSpec is tech-agnostic. The dev workspace handles tech decisions. |

## Directory Navigation

```
build-spec-compiler/
├── SKILL.md                ← You are here — routing guide
├── extraction-rules.md     ← Rules for extracting context from workspace files
└── templates/              ← BuildSpec templates and compilation tools
    ├── flow-buildspec.md   ← Primary template — full flow specification
    └── _compilation-checklist.md  ← Pre-finalization completeness check
```

## Compilation Principles

### 1. Distill, Don't Copy-Paste
The receiving agent needs relevant context, not full documents. Every section should contain only what's necessary for the specific flow being specified. A 500-line behavioral framework becomes 20-30 lines of applicable principles.

### 2. Embed, Don't Link
The receiving agent can't access this workspace's files. Every cross-reference must be resolved — embed the content, don't link to the source. Provenance can be noted parenthetically for traceability, but the content must be inline.

### 3. Product Intent Only
The BuildSpec defines the experience, not the implementation. If any sentence starts with "use", "implement with", "the API should", or "the data model" — reframe it as a product constraint or experience expectation.

**Test:** Read every sentence and ask: "Does this tell the dev agent WHAT to build or HOW to build it?" If HOW — rewrite.

### 4. Trace Provenance
Every section in the BuildSpec should note which source artifact(s) it was drawn from. Format: `[Source: filename.md]` at the end of each section. This lets the user verify what was included and trace back to full context if needed.

### 5. Self-Containment Test
The compiled BuildSpec must pass this test: paste it into a fresh Antigravity conversation with no CarrotFin context. The agent should be able to reason about the flow's design without asking product-level questions that the spec should have answered.

## Rules

- **Never invent content** — every claim in the BuildSpec must trace to a source artifact. If the source doesn't exist, flag the gap — don't fill it with assumptions.
- **Never prescribe technology** — the BuildSpec is the product intent layer. Architecture lives in the dev workspace.
- **Prefer specificity over abstraction** — "The AI surfaces a spending breakdown chart inline below the conversation message" is better than "The AI presents relevant information."
- **Include behavioral anti-patterns** — what the experience should NOT be is as important as what it should be. The MUSTNOT section is not optional.
- **Flag unresolved tensions** — if a design tension from tension-log.md affects this flow, include it honestly. The dev agent should know what's uncertain.
