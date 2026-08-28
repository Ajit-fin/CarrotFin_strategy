# PromptEvolve v3 Phase 1 Deliverables: Scripts & Schemas

## File 1: `extract_constitution.py`
**Type:** Python script (executed via `run_command`).
**Usage:** `python3 extract_constitution.py <prompt-file> [--hash-only]`
**Function:**
- Strips content between (and including) `<!-- EVOLVE-START: name -->` / `<!-- EVOLVE-END: name -->`.
- Outputs remaining text (constitution) to stdout.
- `--hash-only`: Outputs SHA-256 hash of the constitution.
- Exit code 0 (success) or 1 (malformed markers).
**Edge Cases:**
- Multiple blocks: Valid.
- No blocks: Valid (warns).
- Nested/Unclosed blocks: Error, reject.

## File 2: `validate_scorecard.py`
**Type:** Python script (executed via `run_command`).
**Usage:** `python3 validate_scorecard.py <scorecard.json>`
**Function:**
- Parses JSON.
- Validates `candidate_id`, `vote_id`, `sanity_gate` ("pass"/"fail") are always required.
- **Sanity-fail branch:** If `sanity_gate == "fail"`, requires only `candidate_id`, `vote_id`, `sanity_gate`, `aggregate_score` (must be 0). Skips all remaining validation. Outputs "VALID (sanity-fail)".
- **Sanity-pass branch:** Validates full field set: `niche_classification` (`dim1_name/value`, `dim2_name/value`), `criteria_scores`, `aggregate_score`, `max_possible_score`.
- Criterion validation (sanity-pass only): requires `criterion_id`, `score` (<= `max_score`), `max_score`, `weight`, `weighted_score`.
- Validates `aggregate_score` == sum of `weighted_score` (±1 tolerance) (sanity-pass only).
- Outputs "VALID" / "VALID (sanity-fail)" or error message. Exit code 0 or 1.

## File 3: `validate_candidate_output.py` (Simulation Mode)
**Type:** Python script (executed via `run_command`). Deterministic hard gate for schema violations.
**Usage:** `python3 validate_candidate_output.py <output.json> [--schema <schema.json>] [--invariants <invariants.json>]`
**Function:**
- Parses JSON output.
- `--schema`: Validates field presence, enum legality, types, nullability via JSON Schema.
- `--invariants`: Validates cross-field rules (e.g., `{"if": {"escalation.needed": true}, "then_required": ["thinkingText"]}`).
- No flags: Validates JSON parseability.
- Outputs "VALID" or field path error. Exit code 0 or 1.

## File 4: `run_simulation.py` (Simulation Mode)
**Type:** Python test harness (executed via `run_command`). Teacher-forcing simulation.
**Usage:** `python3 run_simulation.py <candidate-file> <eval-db-path> <output-dir> [--temperature 0.3] [--min-pass-rate 0.5] [--schema <path>] [--invariants <path>]`
**Architecture:**
- **Teacher Forcing:** Uses predefined golden history.
- **Atomic Independence:** Stateless snapshot per turn (System Prompt + Turn context + Golden History).
**Process:**
1. Connects to SQLite `eval-db-path`. (SQLite used for native read/write in sandbox).
2. Derives candidate ID from `<candidate-file>` basename (e.g., `candidate-1.md` → ID 1). Inserts row into `simulation_runs` (run_id, generation_id from output-dir, prompt_hash from candidate file, created_at).
3. Queries `golden_turns` ordered by `conversation_type_id`, `turn_index`.
4. For each turn: Constructs payload, invokes local Antigravity subagent, captures output, runs validation gate via `validate_candidate_output.py` (with `--schema`/`--invariants` if provided), records to `live_responses`. (`evaluation_score` column is reserved — populated during post-simulation scoring, not by the runner.)
5. Writes summary to `<output-dir>/sim-candidate-{id}.json` (filename parameterized by candidate ID): includes `pass_rate` and a `turns` array with per-turn `{ turn_id, user_input, actual_llm_output, validation_pass }`.
6. Exit code 0 if `pass_rate` >= `--min-pass-rate` (default 0.5), else 1.
**SQLite Schema:**
```sql
CREATE TABLE golden_turns (turn_id TEXT PRIMARY KEY, conversation_type_id TEXT NOT NULL, turn_index INTEGER NOT NULL, user_input TEXT NOT NULL, context_payload TEXT NOT NULL, golden_history TEXT NOT NULL, expected_output_schema TEXT);
CREATE TABLE simulation_runs (run_id TEXT PRIMARY KEY, generation_id INTEGER NOT NULL, prompt_hash TEXT NOT NULL, created_at TEXT NOT NULL);
CREATE TABLE live_responses (run_id TEXT NOT NULL, turn_id TEXT NOT NULL, actual_llm_output TEXT, validation_pass BOOLEAN, evaluation_score REAL, PRIMARY KEY (run_id, turn_id));
```

## File 5: `state-schema.md`
**Content:** JSON schemas for state and scorecard.
**Details:**
- Field descriptions, required/optional status.
- Valid enums: `status` (running/paused_user/completed), `sanity_gate` (pass/fail), `result` (accepted/rejected).
- Versioning: `schema_version: 1`.
- Fully populated state example for gen 7 (2×2 niche), consistent with master plan's canonical example.
- Documentation of `simulation_mode` fields.

## File 6: `example-config.md`
**Content:** Complete worked example configuration.
**Structure:**
- Metadata: Run ID, Max Generations, Mutators Per Generation (fixed at 3), Evaluator Model.
- Seed Prompt: Includes EVOLVE-BLOCKs (haiku example).
- Niche Dimensions: Table definition.
- Golden Dataset: 3 test cases (Input + Expected Behavior).
- Evaluation Rubric: 5 criteria (binary/graduated, weights).
- Held-Out Dataset (optional).
- Advanced Options: `pause_after`, `niche_grid_size`.
- Simulation Mode: `enabled`, `eval_data_path`, `validation_schema_path`, `invariant_rules`, `min_pass_rate`, `runner_temperature`.

## File 7: Directory Scaffolding
**Structure:**
- `AE/configs/` (empty)
- `AE/runs/` (empty)
- `AE/README.md` (brief explanation of directory purpose).

## Phase 1 Verification Checklist
- [ ] `extract_constitution.py --help` runs.
- [ ] `extract_constitution.py` extracts constitution from seed prompt section.
- [ ] `extract_constitution.py --hash-only` outputs SHA-256 hash.
- [ ] `validate_scorecard.py` validates handcrafted valid JSON.
- [ ] `validate_scorecard.py` rejects malformed JSON.
- [ ] `validate_candidate_output.py` validates well-formed JSON against schema.
- [ ] `validate_candidate_output.py` rejects missing required fields.
- [ ] `validate_candidate_output.py --invariants` catches cross-field violations.
- [ ] `run_simulation.py` processes turns against test SQLite DB.
- [ ] `AE/configs/` and `AE/runs/` exist.
