# AlphaEvolve: Technical Reference for PromptEvolve

> Context-transfer artifact. Pair with `implementation_plan.md` and `promptevolve_design_rationale.md`.

---

## What AlphaEvolve Is

Google DeepMind's evolutionary coding agent. Uses LLMs as mutation operators inside an evolutionary search loop to discover algorithms that surpass human-designed solutions. Key results: beat Strassen's 56-year matrix multiplication record, optimized Google data center scheduling, improved hardware accelerator circuits.

Our goal: adapt its core evolutionary loop for **natural language prompt optimization** within Antigravity.

---

## Architecture (4 Modules)

### 1. Program Database
**How it works:** Stores all candidate solutions + scores. Uses two mechanisms:

- **MAP-Elites:** Partitions search space into a multi-dimensional grid of "niches" defined by phenotypic features (e.g., code complexity × execution speed). Each niche retains only its highest-scoring champion. A new candidate replaces a champion **only if strictly better** (strict elitism).
- **Island Model:** Multiple independent populations evolve in parallel. Elite programs periodically migrate between islands (ring topology) to cross-pollinate traits without destroying local diversity.

| Aspect | Our applicability |
|---|---|
| MAP-Elites niche grid | ✅ v1 — niche dimensions = prompt characteristics (tone, depth, etc.) |
| Strict per-niche elitism | ✅ v1 — exact replication |
| Island model | 🔵 v2 — requires parallel orchestrator loops; complex but feasible |

### 2. Prompt Sampler
**How it works:** Constructs rich, contextual prompts for the LLMs. Does NOT just feed raw code. Includes:

- **Parent program** being mutated (selected via UCB1 bandits)
- **Inspiration programs** sampled from other niches (crossover)
- **Execution traces and error logs** from past evaluations (anti-cycling)
- **Stochastic formatting:** Randomizes prompt structure and meta-instructions to prevent LLMs from falling into repetitive generation patterns
- **UCB1 (Upper Confidence Bound) bandits:** Balances exploitation (mutate the best) vs exploration (try underexplored niches). Formally: select niche with highest `score + c * sqrt(ln(total_selections) / niche_selections)`.

| Aspect | Our applicability |
|---|---|
| Parent + inspiration selection | ✅ v1 — orchestrator selects parent + 1-2 inspirations from archive |
| Failure log injection | ✅ v1 — evaluator scorecard failures passed to next gen's mutators |
| Stochastic formatting | ✅ v1 — 5 randomized mutation strategies |
| UCB1 bandits | ✅ v1 — simplified to 70% exploit / 30% explore |

### 3. LLM Ensemble
**How it works:** Heterogeneous mix of models as mutation/crossover operators:

- **Fast models (Gemini Flash):** High-throughput, low-latency. Generate many minor variations rapidly. Breadth.
- **Powerful models (Gemini Pro):** Deep reasoning, complex refactoring. Depth.
- Models don't "debate" — they act as parallel mutators whose outputs live or die by the evaluator.

| Aspect | Our applicability |
|---|---|
| Flash for mutation breadth | ✅ v1 — 3× flash mutator subagents |
| Pro for deep reasoning | ✅ v1 — pro evaluator subagent (critic role) |

### 4. Evaluator Pool
**How it works:** Sandboxed, automated, programmatic execution:

- **Strictly deterministic:** Measures CPU cycles, execution time, mathematical correctness. Human removed.
- **Evaluation Cascades:** Progressively harder tests. Weak candidates filtered early.
- **LLM-as-judge problem:** Research explicitly warns LLM-based evaluation introduces variance that **disrupts the evolutionary gradient**. Can't distinguish real improvement from lucky scores.
- **LLM Feedback (secondary):** Qualitative grading as additional signal, not primary metric.

| Aspect | Our applicability |
|---|---|
| Programmatic deterministic eval | ❌ Not possible for NL prompts |
| Evaluation cascades | ✅ v1 — sanity gate → full rubric |
| Quasi-deterministic rubric | ✅ v1 — our mitigation: 15-25 criteria, binary/graduated, 3× majority voting |
| LLM qualitative feedback | ✅ v1 — evaluator returns targeted notes alongside scores |

---

## The Evolutionary Loop

```
1. User provides seed program + objective function
2. EVOLVE-BLOCK markers demarcate mutable sections
3. Loop:
   a. Prompt Sampler selects parent (UCB1) + inspirations
   b. LLM ensemble generates diff-style mutations (not full rewrites)
   c. Evaluator cascade: sanity gate → full scoring
   d. Strict elitism: update archive only if strictly better
   e. Repeat
4. Output: champion(s) from archive
```

---

## Advanced Mechanisms

### Meta-Prompting (PromptGenome)
Maintains a separate database of prompting strategies. Evolves the instructions given to mutators alongside the target. The mutation instructions themselves face evolutionary pressure.

| Applicability | 🔵 v2 — adds a second evolutionary loop. Defer until v1 validated. |
|---|---|

### Flexible Abstraction Levels
Can evolve: raw solution strings, constructor functions, or heuristic search algorithms with time budgets.

| Applicability | ❌ Not applicable — our domain is NL prompt text. |
|---|---|

### Full-File Multi-Block Evolution
Modifies entire codebases spanning hundreds of lines, not just single functions.

| Applicability | ✅ Partial — our prompts can have multiple EVOLVE-BLOCKs (task instructions, output format, examples, guardrails). |
|---|---|
