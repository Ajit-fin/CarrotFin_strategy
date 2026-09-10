# Original User Request

## Initial Request — 2026-09-04T02:53:55Z

# Teamwork Project Prompt — Draft

> Status: Launched
> Goal: Multi-persona QA execution and evaluation
> Requested team: have as many agents as needed to simulate and evaluate. keep different subagents for diff kind of works. also run these sequentially and have diff subagent for diff persona.

Run sequential end-to-end QA testing on CarrotFin conversational advisory backend services using autonomous persona simulator subagents for Persona T01 (young single salaried, structured UI interaction) and Persona T07 (full household, natural language interaction), followed by an evaluation and audit subagent performing both deterministic checks and LLM-based user experience quality assessments.

Working directory: /Users/kshekhaw/Documents/CarrotFin_strategy
Integrity mode: development

## Reference Material
- Persona Runner Guide: `workspace-files/qa/prompts/persona-runner-system-local.md`
- Local API Reference: `workspace-files/qa/reference/api-reference-local.md`
- Component Palette: `workspace-files/qa/reference/component-palette.md`
- Persona T01 Profile: `workspace-files/qa/personas/T01-young-single-salaried.md`
- Persona T07 Profile: `workspace-files/qa/personas/T07-full-household.md`
- QA Utility Script: `workspace-files/qa/scripts/qa_utils_local.py`
- SQLite Database: `workspace-files/qa/data/journeys.db`

## Requirements

### R1. Sequential Persona Simulation with Dedicated Subagents
- Run the two personas sequentially: complete Persona T01 fully before starting Persona T07.
- Deploy a dedicated persona simulation subagent for Persona T01:
  - Role-play as a 28yo single tech employee in Bangalore (₹80k income, ₹45k expenses, zero emergency fund).
  - Interact using structured UI completions (`--endpoint guided` with appropriate `guidanceMarker`, `data`, and `resolution` fields) whenever interactive components (`FORM_GROUP`, `VALUE_INPUT`, `TIERED_SELECTOR`) are presented.
  - Approve the plan overview (`APPROVE_PLAN_OVERVIEW`) and continue until the journey reaches milestone review/closure.
- Deploy a dedicated persona simulation subagent for Persona T07:
  - Role-play as a 38yo married manufacturing manager in Pune with 3 dependents (2 kids, mother without insurance) and working spouse (self ₹1.5L, spouse ₹85k, expenses ₹1.1L, ₹50k existing savings).
  - Interact using natural language text messages (`--endpoint standard`) even when interactive components appear, conveying complex multi-entity financial figures in natural sentences.
  - Follow the advisory conversation through to plan completion.

### R2. Service Communication & Robust Latency/Failure Handling
- Interact with local backend services (`user-service` at `http://localhost:8081` and `chat-service` at `http://localhost:8080`) exclusively via `qa_utils_local.py`.
- Begin each journey with `create-test-user` to obtain an isolated Bearer token, followed by `create-conversation` to obtain a backend-generated `conversationId`.
- Accommodate backend LLM generation latency up to 25s per turn without premature client timeouts.
- Handle non-deterministic failures, `SYSTEM_ERROR`, and `SILENT_FAILURE` (SSE streams without a DATA event) by logging retry events and re-attempting up to 3 times per turn as specified in the protocol.

### R3. Evaluation and Quality Audit Subagent (Deterministic + User-Facing LLM Assessment)
- Deploy a dedicated evaluation/auditor subagent to inspect the completed runs:
  - **Deterministic Checks**: Execute `check`, `summary`, and `get-turns` via `qa_utils_local.py` against SQLite `workspace-files/qa/data/journeys.db`, checking component schema conformity against `component-palette.md`.
  - **Turn-Level User Experience Evaluation**: For every turn, evaluate the response from the perspective of an end-user viewing the mobile screen. Use direct LLM reasoning to assess whether the bot's `RESPONSE_TEXT` and any rendered UI components (sliders, options, forms, review cards) were contextually appropriate, helpful, empathetic, non-redundant, and easy to understand.
  - **Journey-Level User Experience Evaluation**: After full journey completion, evaluate the entire advisory trajectory using direct LLM reasoning across plan coherence, journey flow, sufficiency of gathered information, empathetic tone, and whether the resulting recommendations realistically suit that persona's life context.
  - **NLU Extraction Fidelity**: Verify that the backend accurately captured multi-entity household data for T07 (income sources, dependents, uninsured mother) despite receiving unstructured natural language.
  - Record evaluation findings into the SQLite database (`turn_evaluations`, `journey_evaluations`) and compile a final QA audit report.

## Verification Resources
- `python3 workspace-files/qa/scripts/qa_utils_local.py check --db workspace-files/qa/data/journeys.db --run-id <run-id>`
- `python3 workspace-files/qa/scripts/qa_utils_local.py summary --db workspace-files/qa/data/journeys.db --run-id <run-id>`
- `python3 workspace-files/qa/scripts/qa_utils_local.py get-turns --db workspace-files/qa/data/journeys.db --run-id <run-id>`
- SQLite tables in `workspace-files/qa/data/journeys.db`: `test_runs`, `turns`, `checkpoint_results`, `retry_events`, `component_completeness`, `turn_evaluations`, `journey_evaluations`.

## Acceptance Criteria

### Test Execution Integrity
- [ ] Persona T01 and Persona T07 runs are executed sequentially, each in an isolated subagent conversation.
- [ ] Both test runs have distinct test user accounts and backend-generated `conversationId`s recorded in `test_runs`.
- [ ] T01 journey completes all phases including plan approval (`APPROVE_PLAN_OVERVIEW`) and final summary/milestones.
- [ ] T07 journey completes with multi-entity financial figures provided in natural language.
- [ ] No unhandled exceptions or dropped turns; all retries and latency events are captured in SQLite.

### Verification & Checkpoint Results
- [ ] All turns, thinking events, and bot component payloads are persisted in SQLite `journeys.db`.
- [ ] `qa_utils_local.py check` executes for both runs with zero FATAL failures.
- [ ] Direct LLM reasoning produces turn-by-turn user perspective ratings and reasoning for every bot message.
- [ ] Whole-journey evaluation scores and qualitative assessments (plan coherence, flow, information sufficiency) are recorded for both T01 and T07.
- [ ] The auditor subagent produces a comprehensive audit report detailing turn count, latency observations, schema compliance, NLU extraction accuracy, and user-facing experience analysis.
