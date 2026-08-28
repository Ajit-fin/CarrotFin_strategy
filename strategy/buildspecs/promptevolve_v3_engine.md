# PromptEvolve v3 — Engine Specification

> Companion to `promptevolve_v3_plan.md`. Contains SKILL.md structure, workflow registration, and verification procedures.

## Phase 3: Core SKILL.md

**Goal:** Write the main engine file. ~500 lines. Most complex phase.
**Inputs:** All Phase 1 + Phase 2 files (referenced by path).
**Output:** `.agent/skills/alpha-evolve-engine/SKILL.md`

### SKILL.md Frontmatter
```yaml
---
name: alpha-evolve-engine
description: >
  AlphaEvolve-inspired evolutionary prompt optimization engine.
  Runs an autonomous mutation/evaluation loop to evolve natural language
  prompts against a user-defined rubric and test suite.
---
```

### Section 1: Overview
Engine coordinates the AlphaEvolve mutation/evaluation loop. Runs autonomously using state files for checkpointing. 
**References:** Configuration definitions, mutator/evaluator templates, schemas, helper scripts.

### Section 2: Initialization Protocol
1. Read and validate config file (required fields).
2. Handle run-ID collision: if `AE/runs/<run-id>/` exists, append `-2`, `-3`, etc.
3. Create run directory structure: `AE/runs/<run-id>/`, `gen-0/`, `champions/`.
4. Copy config to run directory as `config.md`.
5. Extract seed prompt (parse `## Seed Prompt` section boundaries, write to temp file).
6. Run `extract_constitution.py` (no `--hash-only`) → `constitution.txt`. Run with `--hash-only` → store `constitution_hash` in state.
7. Hash full config file (SHA-256 via `run_command`) → store as `config_hash` in state.
8. Define `pe-mutator` subagent (`define_subagent`): `enable_write_tools: true`, minimal system prompt.
9. Define `pe-evaluator` subagent (`define_subagent`): `enable_write_tools: true`, minimal system prompt.
10. Evaluate seed prompt: invoke 3× evaluator on seed (scorecards to `gen-0/eval-seed-vote-{1..3}.json`), validate each via `validate_scorecard.py`, aggregate per STEP 8 rules.
11. Write `state-gen-0.json` with seed as champion in its baseline niche. Initialize `exploration_counts` for all grid niches to 0 (seed's niche=1, counting the seed evaluation as first exploration). Includes `config_hash` from step 7.
12. Write `archive.json` (derived from state).
13. Write `checkpoint_summary.md`.
14. Write `gen-0/DONE`.

### Section 3: Generation Loop

```
STEP 1 — RECONSTRUCT
  - Re-read this SKILL.md (compaction defense).
  - Determine N: highest-numbered `state-gen-*.json` with matching `gen-*/DONE` marker + 1. Read state.
  - If status == "paused_user" → STOP (defensive guard).
  - If user_notes != null → acknowledge in evolution_log.md, clear.
  - Hash current run config.md → compare to config_hash.
    - If different: log warning, re-evaluate all archive champions, update state, reset config_hash.

STEP 2 — SELECT PARENT
  - Roll: 70% exploit, 30% explore.
  - Exploit: niche with highest champion score → parent.
  - Explore: occupied niche with lowest `exploration_count` → parent. (Fallback to exploit if 1 niche occupied).
  - Select 1-2 inspiration prompts from OTHER niches (if >1 occupied).
  - Increment `exploration_count` for selected niche.

STEP 3 — ASSIGN STRATEGIES
  - For 3 mutators, randomly assign 1 of 5 strategies. Ensure unique strategies if possible.

STEP 4 — INVOKE MUTATORS
  - Create gen-N/ directory.
  - Read mutator-prompt-template.md.
  - For each mutator (1..3):
    - Fill template: parent, inspirations, failed criteria, strategy, failures, constitution, output path (`gen-N/mutant-{id}.md`).
  - Invoke 3× via invoke_subagent: TypeName: "pe-mutator", Model: "flash", Workspace: "inherit", Prompt: template.
  - STOP calling tools. Wait for messages.

STEP 5 — COLLECT & APPLY
  - Verify `gen-N/mutant-{1..3}.md` exist. Log missing.
  - For each mutant file:
    - Apply search-and-replace within parent's EVOLVE-BLOCKs.
    - Write to `gen-N/candidate-{id}.md`.
    - Run `run_command("python3 .../extract_constitution.py gen-N/candidate-{id}.md --hash-only")`.
    - Compare to stored constitution_hash. Reject if mismatch.

STEP 6 — SANITY GATE
  - Check EVOLVE-BLOCK markers presence/formatting.
  - Check coherence. Reject if fail.

STEP 6.5 — SIMULATION (If config simulation_mode.enabled = true)
  - For each candidate C:
    - Run `run_command("python3 .../run_simulation.py <candidate-file> <eval_data.db> gen-N/ --temperature <runner_temperature> --min-pass-rate <min_pass_rate> [--schema <validation_schema_path>] [--invariants <invariant_rules>]")` (omit `--schema`/`--invariants` if not set in config). Script derives candidate ID from `<candidate-file>` basename (e.g., `candidate-1.md` → ID 1) for output filename `sim-candidate-1.json`.
    - If exit code non-zero → reject candidate (script enforces min_pass_rate internally).
  - Evaluators in STEP 7 receive ACTUAL per-turn outputs from simulation summary (e.g., `sim-candidate-1.json`).

STEP 7 — INVOKE EVALUATORS
  - Read evaluator-prompt-template.md.
  - For each candidate C:
    - For each vote V (1..3):
      - Fill template: candidate, rubric, dataset/sim results (per-turn actual outputs from `sim-candidate-C.json` if simulation enabled), niche dimensions, scorecard schema, output path, candidate_id=C, vote_id=V.
      - Invoke via invoke_subagent: TypeName: "pe-evaluator", Model: `evaluator_model`, Workspace: "inherit", Prompt: template.
  - STOP calling tools. Wait for messages.

STEP 8 — AGGREGATE & ARCHIVE UPDATE
  - For each candidate C:
    - Read 3 scorecards: `gen-N/eval-candidate-{C}-vote-{1..3}.json`.
    - Validate: `run_command("python3 .../validate_scorecard.py <path>")`.
    - **Mixed-sanity rule:** If ≥2 of 3 scorecards have `sanity_gate: "fail"` → candidate fails (aggregate_score=0, skip archive update). If exactly 1 has `sanity_gate: "fail"` → drop the failed scorecard, aggregate from the 2 passing scorecards only (mean per criterion instead of majority).
    - Aggregate (passing scorecards only): majority vote per criterion (≥2 of 3; mean if only 2). For graduated criteria: median if all 3 scores differ (binary criteria can have at most 2 distinct values, so majority always resolves).
    - Compute aggregate_score (weighted sum).
    - Niche classification: majority vote across passing scorecards. (Tiebreak: highest aggregate_score).
  - For each scored candidate:
    - If niche empty → unconditional fill.
    - If occupied → replace champion only if score > champion score.
    - Write to `champions/niche-{dim1}-{dim2}.md`.

STEP 9 — COMMIT
  - Build state snapshot (current_generation=N, updated archive, mutation_history, best_score_trajectory, plateau_length (informational — count of consecutive gens with no score improvement), next_action="generation_{N+1}").
  - Write `state-gen-N.json`, `archive.json`.
  - Append to `evolution_log.md`.
  - Write `checkpoint_summary.md`.
  - Write `gen-N/DONE`.

STEP 10 — CONTINUE OR STOP
  - If status == "paused_user" → output summary → STOP.
  - If current_generation >= max_generations → write `champion.md` → STOP.
  - If pause_after set and current_generation >= pause_after → set status="paused_user" → STOP.
  - Else → `schedule(DurationSeconds=30, TimerCondition="never")` → STOP.
```

### Section 4: Mutator Subagent Protocol
- **Input:** Filled mutator template.
- **Output:** Mutation file on disk (`gen-N/mutant-{id}.md`).
- **Return:** File path + status message via `send_message`.
- **Error:** If unable to produce mutation, write `=== NO MUTATION ===` with reason.

### Section 5: Evaluator Subagent Protocol
- **Input:** Filled evaluator template.
- **Rules:** Two-stage cascade (sanity → full). Default temperature. Score actual outputs if simulation enabled.
- **Output:** Scorecard JSON on disk.
- **Return:** File path + gate status + score via `send_message`.
- **Error:** Write scorecard with `sanity_gate: "fail"`.

### Section 6: MAP-Elites Archive Protocol
- **Grid:** 2 user-defined dimensions, format `{dim1}×{dim2}`.
- **Rule:** Strict elitism for occupied; unconditional for empty.
- **Tiebreak:** 2/3 majority niche assignment; tiebreak by highest score.
- **Storage:** Full resolved text in state + standalone `.md`.
- **Start:** Seed assigned baseline niche, others empty.

### Section 7: Resume Protocol
- Find latest `state-gen-N.json` with matching `DONE` marker (fallback to N-1).
- Check `config_hash` against run-directory `config.md` copy. Re-evaluate if changed.
- Read/log user_notes. Set status="running". Continue from N+1.

### Section 8: Termination & Champion Output
- Write `champion.md`: best prompt, niche table, stats.
- Final evaluation on `held_out_dataset` if present.
- Update `checkpoint_summary.md` (status: "completed").

### Section 9: HITL Checkpoint Protocol
- Format: Checkpoint dashboard.
- Safe to edit: config run-copy, user_notes.
- Triggers re-evaluation: config edits. Immutable: past generations.

### Phase 3 Verification
- [ ] Valid YAML frontmatter.
- [ ] 9 sections + STEP 6.5 present.
- [ ] Correct path references.
- [ ] `enable_write_tools: true` in definitions.
- [ ] `flash` mutators, config `evaluator_model` evaluators.
- [ ] Timer uses `DurationSeconds` & `TimerCondition="never"`.
- [ ] Subagent Workspace `"inherit"`.
- [ ] Script verification uses `run_command`.
- [ ] STEP 6.5 conditional on `simulation_mode.enabled`.

---

## Phase 4: Workflow & Registration

**Goal:** Create entry point and register.
**Output:** `.agent/workflows/evolve.md` [NEW], `AGENTS.md` [MODIFY]

### evolve.md
- Role: Evolution Orchestrator
- Read-Path: SKILL.md + state-schema.md
- Activation: `/evolve` (new), `/evolve-resume` (paused)
- Principles: stateless reconstruction, files over messages, deterministic gates, commit-or-rollback.

### AGENTS.md Edits
Add to Available Workflows:
| `/evolve` | 🧬 Evolution Orchestrator | Autonomous prompt optimization | SKILL.md |
| `/evolve-resume` | 🧬 Evolution Orchestrator | Resume paused evolution run | SKILL.md + run directory state |

### Phase 4 Verification
- [ ] `evolve.md` matches `analyst.md` structure.
- [ ] Workflows added to `AGENTS.md`.
- [ ] Correct SKILL.md read-path.

---

## Phase 5: Dry Run Verification

**Goal:** End-to-end verification.

### Platform Assumption Tests
| # | Test | Expected |
|---|---|---|
| P1 | Timer persistence (IDE close/reopen) | Timer fires |
| P2 | Subagent model selection | Pro more detailed than flash |
| P3 | Subagent file write (`inherit`) | File exists/readable |
| P4 | Helper scripts (`run_command`) | Correct output |

### Dry Run Execution
1. Create `AE/configs/haiku-test-config.md` (max_generations=3).
2. Run `/evolve`.
3. Verify gen-by-gen: state JSON, hash, scorecards, elitism, markers, timer.
4. Verify `champion.md`.
5. Run `/evolve-resume` after config edit → verify rubric change detection.

### Verification Report
Document: platform assumptions (✅/❌/⚠️), checkpoints (pass/fail), surprises, adjustments.
