# Plan - CarrotFin Guardrails Evaluation

This plan outlines the steps to evaluate `guardrails_v1.xml` against the three prompt files (`pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`, `flash_conversation_v1.xml`) under text-only constraints.

## Steps

### Phase 1: Initialization
1. [x] Create agent directory metadata files (`ORIGINAL_REQUEST.md`, `BRIEFING.md`).
2. [x] Create `plan.md` and `progress.md`.
3. [ ] Start heartbeat cron timer.

### Phase 2: Analysis Dispatch & Execution
4. [ ] Create a dedicated workspace directory for the analysis subagent (`.agents/explorer_guardrails`).
5. [ ] Dispatch a `teamwork_preview_explorer` agent to analyze the XML files.
    - Input: Paths to target XML files.
    - Task: Evaluate compatibility, gaps, conflicts, text-only constraints, and extraction modules.
    - Output: Analysis handoff report (`analysis.md`).
6. [ ] Monitor explorer progress and retrieve results.

### Phase 3: Synthesis & Draft Compilation
7. [ ] Synthesize findings from explorer reports.
8. [ ] Compile the final first-principles evaluation report at `/Users/kshekhaw/Documents/CarrotFin_strategy/strategy/guardrails_evaluation_report.md`.
9. [ ] Create `PROJECT.md` or update artifact indexes.

### Phase 4: Review & Refinement
10. [ ] Dispatch a `teamwork_preview_reviewer` agent to review the compiled report.
11. [ ] Address feedback and finalize the report.
12. [ ] Write final orchestrator `handoff.md` and report to user.
