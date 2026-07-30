# Original User Request

## 2026-07-11T09:19:08Z

Evaluate `guardrails_v1.xml` against `pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`, and `flash_conversation_v1.xml` for CarrotFin (text-only mode). Assess compatibility, identify gaps or conflicts, and generate best-fit solutions without modifying the prompt files directly.

Working directory: ~/teamwork_projects/carrotfin_guardrails_eval
Integrity mode: development

## Requirements

### R1. First-Principles Markdown Report
Produce a concise (not verbose) markdown report with structured sections and recommendations. The report must evaluate how `guardrails_v1.xml` interacts with the three specified prompt files, focusing on major first-principles aspects (advisory boundaries, accuracy, risk disclosure, trust architecture, privacy).

### R2. Text-Only Constraints
The evaluation must assume a text-only mode for CarrotFin (voice is excluded from the MVP scope) and generate best-fit solutions to address any identified gaps or conflicts. Do not edit the original prompt files.

### R3. Module Optimization Opportunities
Identify other recurring logic or instructions common across the three prompts that could be extracted into new shared modules (similar to `guardrails_v1.xml`) to optimize the architecture.

## Acceptance Criteria

### Formatting & Scope
- [ ] Output is a single markdown file containing structured sections.
- [ ] The report explicitly evaluates the interaction with all three target prompts (`pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`, `flash_conversation_v1.xml`).
- [ ] The report explicitly addresses text-only mode constraints (ignoring voice-specific rules).

### Quality & Constraints
- [ ] The report identifies specific gaps/conflicts and proposes concrete solutions.
- [ ] The report identifies opportunities for creating new shared modules from common logic across the prompts.
- [ ] The analysis is concise and focuses on first-principles, avoiding overly verbose line-by-line breakdowns.
- [ ] No modifications are made to the original `.xml` prompt files.
