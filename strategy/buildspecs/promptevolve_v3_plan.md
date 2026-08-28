# PromptEvolve v3 — Master Plan

> AlphaEvolve-inspired evolutionary prompt optimization engine for Antigravity.

---

## How to Use This Plan

Modular structure — this master plan + 3 companion docs. Each build phase reads **this file** (architecture) + **one companion**:

| Phase | Companion Doc | What to read |
|---|---|---|
| 1 | [`promptevolve_v3_scripts.md`](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/promptevolve_v3_scripts.md) | Script specs, schemas, config, scaffolding |
| 2 | [`promptevolve_v3_templates.md`](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/promptevolve_v3_templates.md) | Mutator + evaluator prompt templates |
| 3-5 | [`promptevolve_v3_engine.md`](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/promptevolve_v3_engine.md) | SKILL.md structure, workflow, dry run |

**Execute each phase in a separate thread** to avoid compaction.

---

## Section I: Architecture & Design Decisions

### Core Concept

Accepts a seed prompt + evaluation rubric + test cases → runs autonomous mutation/evaluation generations → outputs a demonstrably better prompt.

### Key Architectural Decisions

| Decision | Rationale |
|---|---|
| **Immutable Constitution + EVOLVE-BLOCKs** | User wraps mutable sections in `<!-- EVOLVE-START: name -->` / `<!-- EVOLVE-END: name -->`. Everything outside is constitutionally protected. Hash-verified via `run_command` (shell `shasum`) as an integrity self-check on the orchestrator's diff-apply logic (the orchestrator confines edits to EVOLVE-BLOCKs by construction, so the hash detects apply-logic bugs, not mutator misbehavior). |
| **Actor-Critic Separation** | 3× `flash` mutator subagents (generators) + `pro` evaluator subagent (critic). Evaluator scores blind — receives no mutator reasoning. |
| **Subagents Write to Disk, Return Pointers** | Message channel can summarize/truncate. Subagents write outputs as files to the shared run directory (using default `inherit` workspace mode) and return only file paths + one-line status. |
| **Deterministic Ops via `run_command`** | SHA-256 hashing, JSON validation, and constitution extraction run as shell commands — not LLM text generation. Hard gates, not advisory. |
| **JSON Machine State, Markdown Human State** | `state-gen-N.json` and scorecard `.json` files for machine parsing. `checkpoint_summary.md`, `evolution_log.md`, `champion.md` for human reading. |
| **Per-Generation State Snapshots** | `state-gen-N.json` is a full snapshot (not delta). Never overwritten. Partial writes can't destroy prior good state. Resume finds the latest snapshot with a matching `gen-N/DONE` marker. |
| **One-Shot Timer, Post-Commit Re-Arm** | `schedule(DurationSeconds=30)` fired only after DONE marker written. Not cron. No overlap possible by construction. |
| **3× Independent Evaluator Invocations** | One subagent per candidate per vote. Genuinely independent samples (no in-context anchoring). Aggregate via majority vote on disk. |
| **Strict Per-Niche Elitism** | Replace a niche champion only if the new candidate's aggregate score is strictly higher. |
| **70/30 Exploit/Explore Heuristic** | 70% mutate the highest-scoring niche champion, 30% mutate the champion of the least-explored *occupied* niche (empty niches have no parent to mutate; fallback to exploit if only 1 niche occupied). Simplified from formal UCB1 (whose guarantees require deterministic scoring we don't have). State field: `exploration_counts`. |
| **Run-ID Collision: Auto-Suffix** | If `AE/runs/<run-id>/` exists, append `-2`, `-3`, etc. |
| **Optional Simulation Stage** | For schema-heavy prompts, an opt-in STEP 6.5 runs candidates against golden turns via a local subagent, validates structured output, and feeds actual responses to evaluators instead of mental simulation. |

### Directory Layout

```
CarrotFin_strategy/
├── .agent/
│   ├── workflows/
│   │   └── evolve.md                          # /evolve entry point
│   └── skills/
│       └── alpha-evolve-engine/
│           ├── SKILL.md                       # Core engine (~500 lines)
│           └── resources/
│               ├── mutator-prompt-template.md  # Mutator prompt template
│               ├── evaluator-prompt-template.md# Evaluator prompt template
│               ├── state-schema.md            # JSON schemas
│               ├── example-config.md          # Worked config example
│               ├── extract_constitution.py    # Constitution extractor
│               ├── validate_scorecard.py      # Scorecard validator
│               ├── validate_candidate_output.py # Candidate output validator (simulation mode)
│               └── run_simulation.py          # Simulation runner (simulation mode)
├── AE/
│   ├── configs/
│   │   └── haiku-test-config.md              # Phase 5 dry-run config
│   └── runs/
│       └── <run-id>/
│           ├── config.md                      # Copied from source at init
│           ├── constitution.txt               # Extracted immutable text
│           ├── checkpoint_summary.md          # Human dashboard
│           ├── evolution_log.md               # Append-only history
│           ├── champion.md                    # Final output
│           ├── archive.json                   # Derived convenience; reconstructable from state
│           ├── champions/                     # Per-niche champion files
│           ├── state-gen-0.json               # Per-gen snapshots
│           └── gen-N/
│               ├── mutant-{1,2,3}.md
│               ├── candidate-{1,2,3}.md
│               ├── sim-candidate-{C}.json      # Simulation results (if enabled; C = candidate ID)
│               ├── eval-candidate-{C}-vote-{V}.json
│               └── DONE
```

### Subagent Execution Model

1. **Invoke subagents** (e.g., 3 mutators in parallel via one `invoke_subagent` call)
2. **Stop calling tools** — the system automatically resumes when subagent messages arrive
3. **On each message arrival**: check disk for completion (do all expected `gen-N/mutant-*.md` files exist?)
4. **When all expected files exist**: proceed to next step
5. **Subagents use `Workspace: "inherit"`** (default) — they share the orchestrator's workspace

### Mutation Diff Format (Strict)

```
=== MUTATION: <section-name> ===
--- SEARCH ---
[exact text to find within the named EVOLVE-BLOCK]
--- REPLACE ---
[replacement text]
=== END ===
```

Multiple blocks allowed. Orchestrator applies as literal string replacements within EVOLVE-BLOCK boundaries.

### Scorecard JSON Schema

```json
{
  "candidate_id": 1,
  "vote_id": 1,
  "sanity_gate": "pass",
  "niche_classification": {
    "dim1_name": "tone",
    "dim1_value": "formal",
    "dim2_name": "depth",
    "dim2_value": "concise"
  },
  "criteria_scores": [
    {
      "criterion_id": 1,
      "criterion_name": "Factual accuracy",
      "type": "binary",
      "score": 1,
      "max_score": 1,
      "weight": 3,
      "weighted_score": 3,
      "notes": "All facts verified correct"
    }
  ],
  "aggregate_score": 42,
  "max_possible_score": 50,
  "qualitative_notes": "Strong on accuracy, weak on actionability in section 2"
}
```

*Note: `criteria_scores` is truncated for brevity. A valid scorecard includes all criteria from the rubric, with `aggregate_score` equaling the sum of all `weighted_score` values.*

### State JSON Schema

```json
{
  "schema_version": 1,
  "run_id": "advisory-prompt-v1",
  "current_generation": 7,
  "max_generations": 15,
  "pause_after": null,
  "status": "running",
  "evaluator_model": "pro",
  "seed_prompt": "(full original prompt text — never modified)",
  "constitution_hash": "a1b2c3d4...",
  "config_hash": "e5f6g7h8...",
  "niche_dimensions": {
    "dim1": { "name": "tone", "values": ["formal", "conversational"] },
    "dim2": { "name": "depth", "values": ["concise", "comprehensive"] }
  },
  "archive": {
    "formal×concise": {
      "champion_text": "(full resolved prompt)",
      "champion_file": "champions/niche-formal-concise.md",
      "score": 36,
      "max_score": 50,
      "generation_crowned": 5,
      "niche_votes": { "formal×concise": 3 }
    }
  },
  "mutation_history": [
    {
      "generation": 7,
      "mutant_id": 2,
      "strategy": "Targeted Repair",
      "parent_niche": "formal×comprehensive",
      "result": "accepted",
      "score": 38,
      "target_niche": "formal×comprehensive",
      "one_line_summary": "Rewrote guardrails section to explicitly handle edge case X"
    }
  ],
  "exploration_counts": {
    "formal×concise": 4,
    "formal×comprehensive": 5,
    "conversational×concise": 3,
    "conversational×comprehensive": 4
  },
  "best_score_trajectory": [22, 25, 28, 30, 33, 35, 35, 38],  // informational — human monitoring only
  "plateau_length": 0,  // informational — human monitoring only; no automated termination rule
  "next_action": "generation_8",
  "user_notes": null
}
```

---

## Phase Overview

| Phase | Goal | Inputs | Outputs | Companion Doc | Effort |
|---|---|---|---|---|---|
| 1 | Helper scripts & schemas | None | 6 resource files, AE/ directory scaffolding | `promptevolve_v3_scripts.md` | Small, ~450 lines |
| 2 | Prompt templates | Phase 1 schemas | 2 template files | `promptevolve_v3_templates.md` | Medium, ~220 lines |
| 3 | Core SKILL.md | Phase 1+2 files | SKILL.md engine | `promptevolve_v3_engine.md` | Large, ~500 lines |
| 4 | Workflow & registration | Phase 3 SKILL.md | evolve.md + AGENTS.md edit | `promptevolve_v3_engine.md` §Workflow | Small, ~60 lines |
| 5 | Dry run verification | All prior phases | Test run + report | `promptevolve_v3_engine.md` §Verification | Medium |

---

## File Inventory

| Phase | File | Type | ~Lines |
|---|---|---|---|
| 1 | `resources/extract_constitution.py` | [NEW] | ~60 |
| 1 | `resources/validate_scorecard.py` | [NEW] | ~70 |
| 1 | `resources/validate_candidate_output.py` | [NEW] | ~80 |
| 1 | `resources/run_simulation.py` | [NEW] | ~120 |
| 1 | `resources/state-schema.md` | [NEW] | ~80 |
| 1 | `resources/example-config.md` | [NEW] | ~80 |
| 1 | `AE/README.md` | [NEW] | ~10 |
| 2 | `resources/mutator-prompt-template.md` | [NEW] | ~120 |
| 2 | `resources/evaluator-prompt-template.md` | [NEW] | ~110 |
| 3 | `SKILL.md` | [NEW] | ~500 |
| 4 | `workflows/evolve.md` | [NEW] | ~50 |
| 4 | `AGENTS.md` | [MODIFY] | ~3 lines |

**Total new files:** 11 | **Modified:** 1 | **Total ~lines:** ~1,380

---

## Design Rationale

Full design rationale preserved at [`strategy/promptevolve_design_rationale.md`](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/promptevolve_design_rationale.md).

## Compatibility Analysis

First-principles compatibility check against `flash_conversation_v1.xml` preserved at [`first_principles_check.md`](file:///Users/kshekhaw/.gemini/antigravity/brain/e43a3b30-aa4f-4569-8dcc-5d0effd825c6/first_principles_check.md). Key finding: system works for schema-heavy prompts when simulation mode is enabled.
