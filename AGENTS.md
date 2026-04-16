# CarrotFin Strategy Workspace — Agent Instructions

> This file tells Antigravity how to behave in this workspace.

## What This Workspace Is

Strategic brainstorming and product design workspace for **CarrotFin** — an AI-powered personal finance advisor for Indian consumers. CarrotFin is a mobile-first, AI-native app that integrates conversational AI advisory with adaptive, generative UI to create a fundamentally new personal finance paradigm. Stage: pre-funding, two co-founders (product/strategy + tech/dev), with an independent FIRE calculator experiment (fi.insureasy.in) — a vibe code thought experiment not yet publicly marketed. This workspace supports both product UX/UI design (the primary competitive bet) and strategic thinking (market, growth, fundraising).

## Workspace Structure

```
CarrotFin_strategy/
├── AGENTS.md                          # This file — constitutional rules for the AI agent
├── .antigravityignore                 # Files excluded from Antigravity context
├── .gitignore                         # Git ignore rules
│
├── .agent/                            # Agent configuration layer
│   ├── workflows/                     # Persona-based workflow definitions
│   │   ├── analyst.md                 # MECE gap analysis and data sizing
│   │   ├── founder.md                 # Prioritization and strategic tradeoffs
│   │   ├── product.md                 # Feature prioritization and CX design
│   │   ├── ux-designer.md             # Interaction design and adaptive UX
│   │   ├── growth.md                  # Acquisition and positioning strategy
│   │   ├── research.md                # Deep web research with source quality
│   │   ├── feedback.md                # KB updates, workspace health audits
│   │   ├── mockup.md                  # UI mockup generation
│   │   ├── buildspec.md               # Technical handoff compiler for dev workspace
│   │   └── prune.md                   # Post-phase artifact pruning & bloat review
│   ├── skills/                        # Reusable capability modules
│   │   ├── frontend-design/           # Frontend implementation patterns
│   │   ├── ux-design-system/          # GenUI, adaptive layout, component patterns
│   │   ├── journey-mapper/            # User journey mapping methodology
│   │   ├── competitive-teardown/      # Competitor analysis templates
│   │   └── build-spec-compiler/       # BuildSpec compilation for dev handoff
│   └── rules/
│       └── grounding-rules.md         # Anti-hallucination protocol
│
├── knowledge-base/                    # Curated, append-only domain knowledge
│   ├── _index.md                      # KB reading guide and file inventory
│   ├── company-context.md             # Product definition, stage, constraints
│   ├── market-intel.md                # Indian personal finance landscape
│   ├── user-insights.md               # Target segments and user signals
│   ├── assumptions-tracker.md         # Tracked assumptions with validation status
│   ├── prior-learnings.md             # Distilled insights from InsurEasy era
│   └── design-principles.md           # Governing design axioms
│
├── product-design/                    # Product UX/UI design layer
│   ├── _index.md                      # Design layer reading guide
│   ├── ux-philosophy.md               # Hyperpersonalization thesis
│   ├── interaction-model.md           # Conversation + visual integration model
│   ├── screen-taxonomy.md             # Generative, Static, Hybrid screen types
│   ├── tension-log.md                 # Unresolved design tensions
│   ├── journey-catalog/               # User journey definitions
│   │   └── _index.md                  # Journey catalog index
│   ├── component-patterns/            # Adaptive component specifications
│   │   └── _index.md                  # Component patterns index
│   └── design-decisions/              # Immutable design decision records
│       └── _index.md                  # Design decisions index
│
├── strategy/                          # Strategic documents and analyses
│   ├── README.md                      # Strategy directory guide
│   └── buildspecs/                    # Compiled BuildSpec handoff artifacts
│       └── _index.md                  # BuildSpec inventory and status tracker
├── decision-log/                      # Immutable strategic decision records
│   └── README.md                      # Decision log guide
├── research/                          # Deep research artifacts
│   ├── _index.md                      # Research index and freshness tracker
│   ├── ux-patterns/                   # UX pattern research
│   ├── market/                        # Market research
│   ├── competitors/                   # Competitor analyses
│   └── technology/                    # Technology research
├── mockups/                           # UI mockup outputs
└── workspace-files/                   # Drop zone for external files
```

## Rules of Engagement

### Before any conversation:
1. Read relevant knowledge-base files before answering (minimum: `_index.md`)
2. Check `assumptions-tracker.md` for validated vs. speculative
3. Check `decision-log/` for recent decisions

### During any conversation:
4. Be explicit about assumptions — flag untested ones
5. Cross-reference new insights to existing knowledge-base entries
6. Challenge existing thinking — don't just agree

### After any session:
7. Offer to run `/feedback` to update KB and track assumptions
8. Never overwrite context files — append or update specific sections
9. If a significant decision was made, offer to create a decision-log entry

### Domain-specific:
10. When discussing UX/UI, read relevant `product-design/` files first
11. Use inline date annotations `[as of YYYY-MM-DD]` for quantitative claims
12. After sessions that modify KB files, suggest git commit

### When files are dropped into workspace-files/:
13. Acknowledge, offer to summarize into relevant KB file
14. After extraction, source file can be deleted (KB entry keeps provenance)

### When committing multi-phase ideation output to workspace files:
15. Compress before writing — this is automatic, not optional:
    - Strip deliberation traces ("we considered X but rejected it because…") — retain only the decision + one-line rationale
    - Move rejected alternatives to a `## Considered & Rejected` appendix at the end of the same file (max 3 lines each)
    - Remove bridge sentences, restated context, and verbose introductions
    - Target 40–60% reduction from raw session output

## Antigravity Behavioral Preferences

### Context budget
- ~6 files loaded per workflow at conversation start
- On-demand loading for: missing assumptions, contradictions, dependency chains,
  user references, cross-domain questions, skill invocations

### Brain hygiene
- Quarterly: delete empty brain dirs, clean old scratch files, review orphaned artifacts
- Do NOT delete .pb files in ~/.gemini/antigravity/conversations/

### Output discipline
- State session goal as header in artifacts
- Follow workflow output format strictly
- Flag ungrounded assumptions explicitly

### Workflow evolution
- LLM may propose workflow/skill improvements. Never auto-apply without user approval.

## Available Workflows

| Command | Persona | Primary use | Minimum read-path |
|---|---|---|---|
| `/analyst` | 🔍 MECE Analyst | Gap analysis, data sizing | `company-context.md`, `market-intel.md`, `assumptions-tracker.md`, `_index.md` |
| `/founder` | 🎯 Founder/CEO | Prioritization, tradeoffs | All `knowledge-base/` core files, latest `decision-log/` entry |
| `/product` | 🛒 Product & CX | Feature prioritization, UX | `company-context.md`, `user-insights.md`, `product-design/_index.md`, `design-principles.md` |
| `/ux-designer` | 🎨 UX Designer | Interaction design, adaptive UX | `product-design/ux-philosophy.md`, `product-design/interaction-model.md`, `design-principles.md` |
| `/growth` | 📣 Growth | Acquisition, positioning | `user-insights.md`, `market-intel.md`, `company-context.md` |
| `/research` | 🔬 Researcher | Deep web research | `_index.md`, `research/_index.md` |
| `/feedback` | 🔄 Self-improvement | Update KB, audit freshness | All `knowledge-base/` files, workspace health metrics |
| `/mockup` | 🖼️ Visual Designer | UI mockups | `product-design/ux-philosophy.md`, `design-principles.md` |
| `/buildspec` | 📦 Spec Compiler | Dev workspace handoff | `company-context.md`, `design-principles.md`, `product-design/_index.md`, `strategy/buildspecs/_index.md` |
| `/prune` | ✂️ Artifact Pruner | Post-phase bloat review | Thread artifacts, `assumptions-tracker.md`, `grounding-rules.md` |

## Supplemental Rules

Anti-hallucination protocol: `.agent/rules/grounding-rules.md` — all workflows should reference this.

## New Conversation Bootstrap

1. This file (AGENTS.md)
2. `knowledge-base/_index.md`
3. `knowledge-base/company-context.md`
