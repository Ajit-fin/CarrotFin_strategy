# Master Plan — Scenarios & What-If Simulations Mobile UX

## Objective
Deliver a comprehensive, production-grade research, framework, component specification, and interactive mobile prototype package for Scenarios & What-If Simulations on mobile viewports (390px base).

## Requirements Breakdown
- **R1: Cross-Domain Interaction Research & Teardown**
  - >=5 detailed interaction teardowns (at least 3 non-finance: e.g., Google/Apple Maps dynamic route recalculation, MacroFactor/MyFitnessPal diet & weight projection, AWS/GCP pricing & capacity sliders, Civilization/Stellaris tech tree & logistics simulation, InsurEasy/CarrotFin financial simulations).
  - Mobile ergonomics analysis (one-thumb reach zones, 390px viewport visual hierarchy, live calculation debouncing, tactile/visual feedback).
- **R2: End-to-End Scenario Lifecycle Framework**
  - 5 stages: (1) Introduction & Discovery, (2) Parameter Tuning / Sandbox, (3) Comparison & Trade-off Analysis, (4) Decision Commitment & Lock-in, (5) Drift & Lifecycle Monitoring.
  - Matrix mapping: User State, System State, Primary Interaction Modality, Cognitive Load Strategy, Mobile UI Affordances.
- **R3: Mobile Component Pattern Specifications & Stitch Prompts**
  - >=3 reusable mobile component patterns (e.g., Elastic Parameter Drawer, Delta Comparison Diff-Sheet, Scenario Fork & Branch Navigator, Multi-Variable Sensitivity Radar/Tension Bar).
  - Component specs with invariants, states, adaptive behaviors, touch ergonomics.
  - Production-ready Google Stitch prompts following the 2 diverse examples + invariants template.
- **R4: Standalone Interactive Mobile Prototype (HTML/CSS/JS)**
  - Self-contained in `~/teamwork_projects/scenario_what_if_mobile_ux/prototype/` (or root).
  - Interactive simulator frame (iOS/Android mobile shell with 390px preview, theme toggle light/dark, orientation/preset switches).
  - Live scenario manipulation: sliders, branching cards, delta visualizers, real-time recalculation, commitment flows, drift trackers. Zero external backend dependencies.
- **Verification & Audit**:
  - Reviewers, Challengers, and Forensic Auditor for validation and zero-tolerance integrity enforcement.

## Execution Phases
1. **Phase 0: Survey & Exploration** (3 parallel Explorers: Domain Teardowns, Lifecycle & Architecture, Prototype UI/UX Engine)
2. **Phase 1: Milestone M1 (Research) & M2 (Framework)**
3. **Phase 2: Milestone M3 (Component Specs & Stitch Prompts)**
4. **Phase 3: Milestone M4 (Interactive Mobile Prototype Build & Assets)**
5. **Phase 4: Milestone M5 (Review, Adversarial Verification, Forensic Audit & Handoff)**
