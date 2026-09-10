# Original User Request

## 2026-08-28T12:13:47Z

Comprehensive cross-domain research, interaction design framework, and interactive mobile prototypes for presenting and manipulating "Scenarios & What-If Simulations" across their entire lifecycle on mobile screens, without editing existing strategy workspace files.

Working directory: ~/teamwork_projects/scenario_what_if_mobile_ux
Integrity mode: development

## Requirements

### R1. Cross-Domain Interaction Research & Teardown
Perform an in-depth UX teardown of how complex multi-variable scenarios and what-if simulations are handled on mobile screens across diverse non-finance and finance domains (e.g. navigation route recalculation in Google/Apple Maps, fitness/macro projection calculators, cloud resource pricing/capacity sliders, strategy gaming tech trees, logistics simulation). Identify cognitive load reduction techniques, progressive disclosure tactics, and single-thumb touch affordances.

### R2. End-to-End Scenario Lifecycle Framework
Define the end-to-end UX lifecycle for what-if scenarios on mobile from introduction to commitment:
1. **Introduction & Discovery:** Nudging the user into exploration without overwhelming them.
2. **Parameter Tuning / Sandbox:** Intuitive multi-variable manipulation on small viewports (handling interdependent variables, sensitivity analysis, delta previews).
3. **Comparison & Trade-off Analysis:** Side-by-side vs. toggle vs. diff views on mobile constraints.
4. **Decision Commitment & Lock-in:** Converting simulated states into actionable plans.
5. **Drift & Lifecycle Monitoring:** Tracking real-life divergence from the chosen scenario over time.

### R3. Mobile Component Pattern Specifications & Stitch Prompts
Design standalone, reusable mobile UI component patterns for scenario exploration:
- Provide detailed component specs (states, invariants, adaptive behaviors, touch ergonomics).
- Produce Google Stitch-ready prompt templates (showing stacked storyboard states, two diverse data examples, and explicit invariants).

### R4. Standalone Interactive Mobile Prototype (HTML/CSS/JS)
Build interactive, standalone mobile HTML/CSS/JS prototypes in the working directory that showcase the scenario exploration lifecycle with live interactive sliders, branching cards, delta visualizers, and state transitions, viewable in a responsive mobile simulator frame.

## Acceptance Criteria

### Research & Analysis
- [ ] At least 5 detailed teardowns of scenario/what-if interactions documented with clear interaction diagrams/mechanics, with at least 3 from non-finance domains.
- [ ] Explicit analysis of mobile ergonomics (thumb-reach zones, live calculation debouncing/feedback, visual hierarchy on 390px-wide viewports).

### Framework & Component Specifications
- [ ] Complete 5-stage lifecycle framework detailing user state, system state, and primary interaction modality at each stage.
- [ ] At least 3 component specifications with corresponding production-ready Google Stitch prompts following the standard 2-example + invariants template.

### Working Interactive Prototype
- [ ] Self-contained interactive HTML/CSS/JS prototype in `~/teamwork_projects/scenario_what_if_mobile_ux/` demonstrating the what-if lifecycle with real-time UI updates, smooth animations, and touch-friendly controls.
- [ ] Clean visual design system suitable for dark/light mobile themes with zero external backend dependencies.
