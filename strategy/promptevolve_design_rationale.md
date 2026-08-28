# PromptEvolve: Design Rationale

> Context-transfer artifact. Pair with `implementation_plan.md` and `promptevolve_alphaevolve_reference.md`.

---

## The Problem

Manual prompt iteration suffers from three failure modes:

1. **Prompt Drift:** Over multiple revision turns, output quality degrades slowly. Each edit subtly erodes previously working elements — like a game of telephone.
2. **Overfitting:** The optimizer hyper-fixates on the most recent failure, breaking things that used to work. Fixing edge case A introduces regression in case B ("whack-a-mole").
3. **First Principles Amnesia:** Core design axioms get diluted or rewritten as the prompt grows. By turn 10, the prompt may contradict its own founding logic.

**Root cause:** No structural separation between what's mutable and what's sacred. No empirical scoring across a stable test suite. No memory of what was tried and why it failed.

---

## The Goal

Build a reusable, parameterized scaffold in Antigravity that:
- Accepts any prompt + evaluation criteria + test cases as inputs
- Runs an autonomous evolutionary optimization loop (10-20 generations)
- Outputs a demonstrably better prompt, scored against empirical evidence
- Prevents all three failure modes by design

---

## Key Design Decisions & Rationale

### 1. Immutable Constitution + Mutable EVOLVE-BLOCKs
**Decision:** User marks mutable sections with `<!-- EVOLVE-START -->` / `<!-- EVOLVE-END -->`. Everything else is untouchable.
**Why:** Structurally prevents First Principles Amnesia. The constitution can never be rewritten by the evolutionary loop. Directly adapted from AlphaEvolve's `# EVOLVE-BLOCK-START/END` pattern.

### 2. Actor-Critic Separation (Non-Negotiable)
**Decision:** Generator (3× flash mutator subagents) and Evaluator (1× pro subagent) are strictly separate agents.
**Why:** If the same agent writes and grades, it's biased toward its own output. AlphaEvolve enforces this separation absolutely. The evaluator has no access to the mutator's reasoning — it scores blind.

### 3. Quasi-Deterministic Rubric (15-25 Criteria, 3× Voting)
**Decision:** Instead of "rate this prompt 0-100," the evaluator scores against 15-25 specific criteria with binary/graduated scoring, each test case run 3 times with majority vote.
**Why:** AlphaEvolve uses strictly programmatic evaluation. We can't run code for NL prompts, so we engineer determinism through criterion density and voting. A prompt that scores 47/50 vs 45/50 is a meaningful, stable signal. A prompt that scores "82 vs 79" on subjective feel is noise.

### 4. MAP-Elites Archive (Not Just "Keep the Best")
**Decision:** Maintain a grid of niche champions across 2-3 dimensions (e.g., tone × depth), not just one "best prompt."
**Why:** Prevents premature convergence. A concise-formal prompt and a comprehensive-conversational prompt may both be valuable. Keeping diverse champions ensures the evolutionary pool has genetic variety to draw from. If you only keep the #1 prompt, you converge into a local optimum.

### 5. UCB1 Parent Selection (70/30 Exploit/Explore)
**Decision:** 70% of the time mutate the best niche champion. 30% of the time mutate the least-explored niche.
**Why:** Pure exploitation (always mutate the best) converges too fast and misses novel approaches. Pure exploration (random selection) wastes generations. UCB1 is the mathematically optimal balance. Simplified from the formal UCB1 formula to natural language instructions.

### 6. Stateless Orchestrator Pattern
**Decision:** At the end of every generation, serialize the complete state to `evolution_state.md`. At the start of every generation, read it back fully.
**Why:** Antigravity triggers context compaction at ~135k tokens. Over 15-20 generations, the orchestrator will lose nuanced context from earlier generations. By externalizing all state to disk, every generation is idempotent — the orchestrator can reconstruct perfectly from the file regardless of what was compacted.

### 7. Diff-Based Mutations (Not Full Rewrites)
**Decision:** Mutators output targeted search-and-replace blocks, not rewritten prompts.
**Why:** Full rewrites cause prompt drift — each rewrite introduces subtle, untracked changes. Diffs make every change explicit, trackable, and reversible. Also mirrors AlphaEvolve's approach.

### 8. Evaluation Cascades (Two-Stage)
**Decision:** Stage 1 (sanity gate) checks structural integrity before Stage 2 (full rubric) runs the expensive evaluation.
**Why:** A mutator might accidentally break the prompt's structure (e.g., delete a required section). Running the full 15-25 criteria rubric on garbage wastes evaluator tokens. The sanity gate filters these out cheaply.

### 9. Stochastic Mutation Strategies (5 Types)
**Decision:** Each mutator is randomly assigned one of 5 strategies: targeted repair, structural refactor, example engineering, simplification, constraint tightening.
**Why:** LLMs fall into repetitive generation patterns when given identical instructions. AlphaEvolve's Prompt Sampler uses randomized formatting for this reason. Assigning different strategies forces diverse mutations per generation.

### 10. Human-in-the-Loop Checkpoints (Every Generation Boundary)
**Decision:** Every generation boundary is a checkpoint. The orchestrator writes a human-readable `checkpoint_summary.md` after every generation. The loop can be paused (kill timer or set `pause_after` in config), the user can inspect progress, adjust config/rubric/archive, and resume via `/evolve-resume`. Plateau length is reported in the dashboard for awareness but **never auto-interrupts** — breakthroughs in evolutionary search often come after 10-20 stagnant generations.
**Why:** A natural consequence of the stateless orchestrator pattern — since all state is serialized to disk after every generation, pause/resume comes for free. Human judgment at checkpoints catches problems the evaluator rubric might miss. The `user_notes` field in `evolution_state.md` lets the user inject qualitative steering that the orchestrator reads on resume.

---

## Antigravity Platform Constraints That Shaped the Design

| Constraint | Impact on Design |
|---|---|
| **Compaction Amnesia** (~135k token limit) | → Stateless Orchestrator pattern with full state serialization every generation |
| **Clean Slate Subagents** (no inherited context) | → Each subagent must receive all necessary context in its prompt (parent, inspirations, rubric, failures) |
| **No In-Memory State Sharing** | → All state lives in markdown files on disk. Evolution state, archive, and log are all filesystem artifacts. |
| **Subagent nesting limit** (10 levels) | → Flat architecture: orchestrator spawns mutators + evaluator directly. No nested delegation. |
| **Schedule tool** for autonomous loops | → 30-second timer between generations. Orchestrator ends turn, timer wakes it up for next generation. |

---

## What's NOT in v1 (Deferred)

| Feature | Why Deferred |
|---|---|
| **Island Model** | Requires parallel orchestrator loops with periodic migration. Significant complexity. v1 validates the core loop first. |
| **Meta-Prompting (PromptGenome)** | Evolving the mutation instructions themselves is a second-order optimization. Adds a second evolutionary loop. Defer until v1 proves the single-loop architecture works. |
| **Evaluator Model Flexibility** | v1 hardcodes pro for evaluator. Could allow flash evaluator for faster rubric development iterations in v2. |
| **Multi-Run Comparison** | v1 produces one champion archive per run. v2 could compare champions across multiple runs with different configs. |
