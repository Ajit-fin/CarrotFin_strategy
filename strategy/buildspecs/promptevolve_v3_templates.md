# PromptEvolve v3 — Prompt Templates

> Companion to `promptevolve_v3_plan.md`. Phase 2 template specs.

## Phase 2 Overview
**Goal:** Create mutator/evaluator prompt templates for dynamic orchestrator injection.
**Effort:** Medium. 2 files, ~110 lines each.
**Outputs:**
- `.agent/skills/alpha-evolve-engine/resources/mutator-prompt-template.md`
- `.agent/skills/alpha-evolve-engine/resources/evaluator-prompt-template.md`

## File 1: `mutator-prompt-template.md`

**Placeholders (8):** PARENT_PROMPT, INSPIRATION_PROMPTS, FAILED_CRITERIA, MUTATION_STRATEGY, STRATEGY_DESCRIPTION, RECENT_FAILURES, CONSTITUTION, OUTPUT_PATH

**Template Structure:**
```markdown
# Mutator Subagent Instructions

## Role
Prompt mutation specialist. Produce targeted mutations following assigned strategy.

## Rules (Non-Negotiable)
1. Modify ONLY within `<!-- EVOLVE-START -->`/`<!-- EVOLVE-END -->` blocks.
2. DO NOT modify text outside these blocks (the Constitution).
3. Output exact diff format specified below.
4. Write output to specified file path.

## Strategy: {{MUTATION_STRATEGY}}
{{STRATEGY_DESCRIPTION}}

## Constitution (DO NOT MODIFY)
{{CONSTITUTION}}

## Parent Prompt
{{PARENT_PROMPT}}

## Inspiration Prompt(s)
{{INSPIRATION_PROMPTS}}

## Failed Criteria
{{FAILED_CRITERIA}}

## Recent Failed Mutations
{{RECENT_FAILURES}}

## Output Format
Write mutation to: `{{OUTPUT_PATH}}`

Output ONLY mutation blocks:
=== MUTATION: <section-name> ===
--- SEARCH ---
[exact text to find]
--- REPLACE ---
[replacement text]
=== END ===

Respond with ONLY:
`WRITTEN: {{OUTPUT_PATH}}`
[One-line summary of change]
```

**Strategy Descriptions:**
- **Targeted Repair:** Fix weakest criteria from scorecard. Identify responsible EVOLVE-BLOCK section(s) and surgically rewrite.
- **Structural Refactor:** Reorganize logical flow within EVOLVE-BLOCKs (reorder, group) without changing semantics.
- **Example Engineering:** Modify/add few-shot demonstrations within EVOLVE-BLOCKs.
- **Simplification:** Reduce complexity, shorten sentences, remove redundancies in EVOLVE-BLOCKs.
- **Constraint Tightening:** Add explicit guardrails and output constraints in EVOLVE-BLOCKs.

## File 2: `evaluator-prompt-template.md`

**Placeholders (8+1):** CANDIDATE_PROMPT, RUBRIC, GOLDEN_DATASET, NICHE_DIMENSIONS, SCORECARD_SCHEMA, OUTPUT_PATH, CANDIDATE_ID, VOTE_ID, (Optional: SIMULATION_RESULTS)

**Template Structure:**
```markdown
# Evaluator Subagent Instructions

## Role
Blind evaluator. Score candidate against rubric/test cases. 

## Critical Rules
1. Use default model temperature.
2. Score ONLY against rubric criteria.
3. Be harsh/precise.
4. Write scorecard to file path as valid JSON.

## Assignment
- Candidate ID: {{CANDIDATE_ID}}
- Vote ID: {{VOTE_ID}}

## Candidate Prompt
{{CANDIDATE_PROMPT}}

## Evaluation Rubric
{{RUBRIC}}

## Golden Dataset
{{GOLDEN_DATASET}}

## Niche Dimensions
{{NICHE_DIMENSIONS}}

## Procedure
### Stage 1: Sanity Gate
Check structural integrity/coherence. If FAILS → write `{"candidate_id": {{CANDIDATE_ID}}, "vote_id": {{VOTE_ID}}, "sanity_gate": "fail", "aggregate_score": 0}`, stop.

### Stage 2: Full Rubric Evaluation
**Mental Simulation Mode (Default):** Mentally simulate test cases. Assign **single holistic score** per criterion across ALL test cases. Sum weighted scores.
**Simulation Mode (if {{SIMULATION_RESULTS}} present):** Score based on ACTUAL model outputs from simulation run.

### Stage 3: Niche Classification
Classify candidate on each niche dimension based on prompt characteristics.

## Required Output
Write valid JSON to: `{{OUTPUT_PATH}}`
Schema: {{SCORECARD_SCHEMA}}

Respond ONLY:
`WRITTEN: {{OUTPUT_PATH}}`
`GATE: pass/fail`
`SCORE: N/M`
```

## Phase 2 Verification
- [ ] Mutator template has 8 placeholders.
- [ ] Evaluator template has 6 base placeholders + SIMULATION_RESULTS.
- [ ] Both use "write to disk, return pointer" pattern.
- [ ] Diff format matches master plan.
- [ ] JSON schema matches master plan.
- [ ] Evaluator handles mental vs. actual scoring.
