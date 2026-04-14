# CarrotFin Strategy Workspace — Implementation Blueprint

> **Product:** CarrotFin — AI-powered personal finance advisor  
> **Stage:** Pre-funding, 2 co-founders (product/strategy + tech/dev)  
> **Purpose:** Founder's second brain for product design + strategy  
> **Phases:** 5 (execute sequentially, one session each)

---

## Preamble: Shared Context

### Product Context

CarrotFin is a mobile-first, AI-native personal finance advisor for Indian consumers. The workspace must support:
- **Product UX/UI decisions** — adaptive, generative UI paradigms (not incremental improvements over existing apps)
- **Novel user journey design** — conversational AI integrated with visual surfaces
- **Strategic thinking** — product, growth, market, and fundraising

This is a **product design + strategy** hybrid workspace. UX innovation is the primary competitive bet.

### Design Principles

1. **Separation of concerns** — Strategic context, product design context, and technical reference live in distinct zones.
2. **Read-path optimization** — Every workflow has a defined read-path. Extensible as files are added.
3. **Append-only knowledge, versioned decisions** — Knowledge files grow via append. Decisions are immutable snapshots.
4. **Version-controlled evolution** — The workspace is a git repo. Every session ends with a commit.

### Configuration Model (2 files)

| File | Contents | When loaded |
|---|---|---|
| **`AGENTS.md`** (~250 lines) | Constitutional rules, Antigravity preferences, workflow table | Always — system prompt injection |
| **`.agent/rules/grounding-rules.md`** (~50 lines) | Anti-hallucination protocol | On-demand — referenced by workflows |

Domain-scoped rules (KB write rules, research quality, cleanup protocol, design conventions) are embedded in the workflow files that use them.

### Directory Structure

```
CarrotFin_strategy/
├── AGENTS.md
├── .antigravityignore
├── .gitignore
│
├── .agent/
│   ├── workflows/
│   │   ├── analyst.md
│   │   ├── founder.md
│   │   ├── product.md
│   │   ├── ux-designer.md
│   │   ├── growth.md
│   │   ├── research.md
│   │   ├── feedback.md
│   │   └── mockup.md
│   ├── skills/
│   │   ├── frontend-design/
│   │   ├── ux-design-system/
│   │   ├── journey-mapper/
│   │   └── competitive-teardown/
│   └── rules/
│       └── grounding-rules.md
│
├── knowledge-base/
│   ├── _index.md
│   ├── company-context.md
│   ├── market-intel.md
│   ├── user-insights.md
│   ├── assumptions-tracker.md
│   ├── prior-learnings.md
│   └── design-principles.md
│
├── product-design/
│   ├── _index.md
│   ├── ux-philosophy.md
│   ├── interaction-model.md
│   ├── screen-taxonomy.md
│   ├── tension-log.md
│   ├── journey-catalog/
│   │   └── _index.md
│   ├── component-patterns/
│   │   └── _index.md
│   └── design-decisions/
│       └── _index.md
│
├── strategy/
│   └── README.md
├── decision-log/
│   └── README.md
├── research/
│   ├── _index.md
│   ├── ux-patterns/
│   ├── market/
│   ├── competitors/
│   └── technology/
├── mockups/
└── workspace-files/
```

### Workflow Catalog

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

### File Header Standard

Every knowledge-base and research file uses:
```markdown
# [Title]
> **Domain:** [taxonomy domain]  
> **Last updated:** YYYY-MM-DD  
> **Staleness threshold:** 30 days  
> **Related assumptions:** [IDs]  
> **Related decisions:** [filenames]  
```

Inline date annotations for individual claims: `[as of YYYY-MM-DD]` or `[source: name, as of YYYY-MM-DD]`

---
---

## PHASE 1: Scaffold & Configuration

> **Cognitive mode:** Architect — structural, template-following  
> **Files to create:** ~5 substantive files + directories  
> **Read:** Preamble + this section

### Deliverables

- [ ] Create all directories per structure above (empty subdirectories like `journey-catalog/`, `component-patterns/`, `ux-patterns/`, etc.)
- [ ] Write `AGENTS.md` (~250 lines) using skeleton below
- [ ] Write `.agent/rules/grounding-rules.md` using content below
- [ ] Write `.antigravityignore`
- [ ] Write `.gitignore` (exclude `workspace-files/`, `_archive/`, `.DS_Store`)
- [ ] `git init && git add -A && git commit -m "Phase 1: scaffold and config"`

**Not in scope:** Knowledge-base content, product-design content, workflows, skills.

### AGENTS.md Skeleton

```markdown
# CarrotFin Strategy Workspace — Agent Instructions

> This file tells Antigravity how to behave in this workspace.

## What This Workspace Is
[3-4 sentences: Strategic brainstorming and product design workspace for CarrotFin — 
an AI-powered personal finance advisor for Indian consumers.
Stage: pre-funding, 2 co-founders (product/strategy + tech/dev).
InsurEasy MVP live (~300 users). FIRE calculator built as a thought experiment (not yet marketed).
Supports both product UX/UI design and strategic thinking.]

## Workspace Structure
[Copy the directory tree from Preamble, with one-line descriptions per directory]

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
[Copy workflow catalog table from Preamble]

## Supplemental Rules
Anti-hallucination protocol: `.agent/rules/grounding-rules.md` — all workflows should reference this.

## New Conversation Bootstrap
1. This file (AGENTS.md)
2. `knowledge-base/_index.md`
3. `knowledge-base/company-context.md`
```

### grounding-rules.md Content

```markdown
# Grounding Rules — Anti-Hallucination Protocol
> Referenced by all workflows.

1. **Cite or flag:** Every factual claim must cite a source or be flagged as
   "Ungrounded assumption — needs validation"
2. **Assumption awareness:** Check `assumptions-tracker.md` before recommending.
   State which untested assumptions your recommendation depends on.
3. **Self-correction:** After a recommendation, briefly play devil's advocate.
4. **No hallucinated data:** Say "I don't have data. Want me to research via `/research`?"
5. **Cross-reference mandate:** Reference at least one existing KB entry per recommendation.
6. **Conflict detection:** Flag contradictions with existing KB entries. Never silently overwrite.
```

---
---

## PHASE 2: Knowledge Base

> **Cognitive mode:** Strategist/Analyst — content synthesis, careful framing  
> **Files to create:** 7 files with substantive content  
> **Read:** Preamble + this section

### Deliverables

- [ ] Write `knowledge-base/_index.md` — list all KB files with descriptions and dates
- [ ] Write `knowledge-base/company-context.md` — CarrotFin product definition, current state
- [ ] Write `knowledge-base/market-intel.md` — Indian personal finance landscape (with inline dates)
- [ ] Write `knowledge-base/user-insights.md` — target segments, existing user signals
- [ ] Write `knowledge-base/assumptions-tracker.md` — initial assumptions table
- [ ] Distill `knowledge-base/prior-learnings.md` from InsurEasy workspace
- [ ] Write `strategy/README.md` and `decision-log/README.md` and `research/_index.md`
- [ ] `git add -A && git commit -m "Phase 2: knowledge base"`

### Content Guidance

| File | What to write |
|---|---|
| `company-context.md` | CarrotFin product definition (AI-native personal finance advisor, not a robo-advisor). Current stage (pre-funding, 2 co-founders). Existing products: InsurEasy MVP (~300 users), FIRE calculator (thought experiment, not yet marketed). Key constraints (zero marketing budget, pre-funding). Immediate goals. |
| `market-intel.md` | Indian personal finance app landscape with inline date annotations. Key competitors (CRED, ET Money, Kuvera, INDmoney, Groww). Market sizing. What existing apps get right and wrong. Use `[as of YYYY-MM-DD]` for all metrics. |
| `user-insights.md` | Target segments for CarrotFin. Existing signals from InsurEasy users (~300 users). User behavior patterns from InsurEasy. Unmet needs in personal finance advisory. No direct CarrotFin user data yet. |
| `assumptions-tracker.md` | Table: ID, Assumption, Status (Untested/Partially Validated/Validated/Invalidated), Evidence, Date Added. Seed with 5-8 initial assumptions about the product and market. |

### Prior Learnings Distillation

Read these files from `~/Documents/Insureasy_strategy/knowledge-base/`:
- `company-context.md`, `market-intel.md`, `user-insights.md`, `assumptions-tracker.md`

Write `prior-learnings.md` — **interpret for CarrotFin context, don't copy InsurEasy content:**

| Extract | Content |
|---|---|
| **Product stickiness mechanisms** | The 7 mechanisms catalog — universally applicable |
| **User behavior insights** | B1 (human validation need), B2 (confidence > information), B3 (network ≠ acquisition) |
| **Validated assumptions** | A2 (users trust AI — partially), A12 (AI-first differentiates) |
| **Growth learnings** | LinkedIn failure (push ≠ pull), SEO decline (AI Overviews), intent > broadcast |
| **Product design lessons** | FIRE calculator thought experiment (progressive disclosure, variant analysis UX). InsurEasy conversational AI (conversation-first, shareability) |
| **Do NOT carry forward** | Insurance-specific constraints, policy upload mechanics, InsurEasy brand |

---
---

## PHASE 3: Product Design Foundation

> **Cognitive mode:** Designer — creative, opinionated, deep UX thinking  
> **Files to create:** 8 files requiring design rigor  
> **Read:** Preamble + this section + **Appendix A (Product Design Brief)** — read Appendix A first

> [!IMPORTANT]
> **Before writing any file in this phase, read Appendix A at the bottom of this document.** It contains the design thesis, paradigm definitions, and anti-patterns that must inform every file. Without it, the product-design files will be generic.

### Deliverables

- [ ] Write `knowledge-base/design-principles.md` — governing axioms (informed by Appendix A)
- [ ] Write `product-design/_index.md` — reading guide for the design layer
- [ ] Write `product-design/ux-philosophy.md` — the hyperpersonalization thesis (deep, not generic)
- [ ] Write `product-design/interaction-model.md` — how conversation + visuals integrate
- [ ] Write `product-design/screen-taxonomy.md` — screen types and when each applies
- [ ] Write `product-design/tension-log.md` — initial design tensions (seed 3-5)
- [ ] Write `product-design/journey-catalog/_index.md` and `component-patterns/_index.md` and `design-decisions/_index.md`
- [ ] `git add -A && git commit -m "Phase 3: product design foundation"`

### Content Depth Expectations

These are NOT stub files. Each should be a substantive, opinionated design document:

| File | Depth expected | Key questions it must answer |
|---|---|---|
| `design-principles.md` | ~80-120 lines | What are the 5 governing axioms? Why each one? How do they resolve conflicts with each other? |
| `ux-philosophy.md` | ~100-150 lines | What IS CarrotFin's UX thesis? Why is hyperpersonalization architectural, not a feature? How does this differ from every existing finance app? What does "adaptive" mean concretely? |
| `interaction-model.md` | ~80-120 lines | How do conversational AI and visual surfaces actually integrate? Not "chat + dashboard" — what does "woven together" look like? What are the surface types? When does conversation lead vs. visualization lead? |
| `screen-taxonomy.md` | ~60-100 lines | What are Generative, Static, and Hybrid screens? When is each appropriate? How does the AI decide which to render? What are the constraints for each? |
| `tension-log.md` | ~30-50 lines | What are the 3-5 unresolved design tensions? Both forces articulated. Why they can't be resolved yet. |

---
---

## PHASE 4: Workflows & Skills

> **Cognitive mode:** System builder — template-following, rule-embedding  
> **Files to create:** 8 workflows + 4 skill scaffolds  
> **Read:** Preamble + this section

### Deliverables

- [ ] Create 8 workflow files in `.agent/workflows/` using template below
- [ ] Embed domain-scoped rules in relevant workflows (see distribution table)
- [ ] Create `ux-design-system/` skill (SKILL.md + subdirectory structure)
- [ ] Create `journey-mapper/` skill (SKILL.md + methodology stub)
- [ ] Create `competitive-teardown/` skill (SKILL.md + template stub)
- [ ] Upgrade `frontend-design/` skill for CarrotFin context
- [ ] `git add -A && git commit -m "Phase 4: workflows and skills"`

### Workflow Template

```yaml
---
last-modified: YYYY-MM-DD
---
```
```markdown
# /[command] — [Persona Name]
## Role
[Who you are, what you do for CarrotFin]
## Read-Path
[Minimum files to load — from workflow catalog]
## Embedded Rules
[Domain-scoped rules — see distribution table]
## Output Format
[Required structure]
## Principles
[3-5 guiding principles]
```

### Embedded Rules Distribution

| Rules | Embed in | Content summary |
|---|---|---|
| **KB write rules** | `/feedback` | Append-only, freshness dates, compaction at ~500 lines, cross-references |
| **Cleanup protocol** | `/feedback` | Workspace health audit: file count (>~25), freshness (>~30 days stale), volume. Brain hygiene reminders. One-in-one-out for research. |
| **Research quality** | `/research` | Source tiers (Tier 1/2/3), cross-validation (≥2 sources or flag), staleness thresholds, archival (>90 days → archive) |
| **Design doc conventions** | `/ux-designer` | File templates, journey format, component pattern format |
| **Decision record format** | `/founder` | Immutability, required fields (context, options, decision, assumptions, reversal trigger) |

### `/ux-designer` Spec

**Role:** UX Design Lead for CarrotFin. Radical innovation, not incremental improvement.

**Principles:**
1. Adaptive-first — "How does this change for a different user context?"
2. Conversational + visual integration — never separate surfaces
3. Hyperpersonalization is architecture, not a feature
4. Generative UI thinking — design constraint systems, not static screens

**Output:** Screen concept → adaptive behavior spec → component decomposition → conversational integration points → design decision justification

**Invokes** `ux-design-system/` skill for pattern lookups and methodology.

### Skill Architecture

```
.agent/skills/[name]/
├── SKILL.md       ← Router (~50-80 lines)
├── patterns/      ← Domain content
├── examples/      ← Reference implementations
├── methodology/   ← Process and checklists
└── resources/     ← Templates and assets
```

| Skill | What SKILL.md covers | Subdirectory content |
|---|---|---|
| `ux-design-system/` | When to look up patterns vs. freeform, navigation guide | GenUI, adaptive layout, confidence indicators, streaming UI, disposable interfaces. Component templates. Annotated examples. Methodology checklists. |
| `journey-mapper/` | When to map vs. skip | User state models, emotional arcs, decision points, AI intervention opportunities |
| `competitive-teardown/` | Full vs. partial teardown criteria | Screen analysis templates, interaction extraction, UX gap checklists |

### Evolution Rules

- Workflow line budget: ~80-120 lines. Compress when approaching.
- `/feedback` checks workflow sizes alongside KB freshness. Flags >~120 lines.
- Rewrite sections, don't append amendments. Git preserves history.

---
---

## PHASE 5: Validation & Operational Maturity

> **Cognitive mode:** QA — systematic testing, gap identification  
> **Read:** Preamble + this section

### Deliverables

- [ ] Create `_archive/` subdirectories in `research/`, `strategy/`, `mockups/`
- [ ] Verify all KB files have complete metadata headers
- [ ] Verify inline date annotations use correct format
- [ ] Test `/founder` — verify read-path loads correctly
- [ ] Test `/ux-designer` — verify skill invocation works
- [ ] Test `/feedback` — verify workspace health audit runs, cleanup rules embedded
- [ ] Test `/research` — verify source quality rules load
- [ ] Verify `AGENTS.md` loads via `<user_rules>` (constitutional rules followed)
- [ ] Update all `_index.md` files to reflect actual file inventory
- [ ] `git add -A && git commit -m "Phase 5: validation complete"`
- [ ] Create a walkthrough document

### Validation Criteria

| Test | Pass condition |
|---|---|
| AGENTS.md injection | New conversation knows to check KB before answering |
| Workflow read-paths | Each workflow loads its minimum files |
| Embedded rules | `/feedback` applies cleanup. `/research` applies source tiers. |
| Grounding rules | Recommendations cite sources or flag as ungrounded |
| Skill invocation | `/ux-designer` invokes `ux-design-system/` SKILL.md |
| Git workflow | Session ends with suggested commit |
| Inline annotations | New metrics include `[as of YYYY-MM-DD]` |

### Monitoring Metrics (for ongoing `/feedback`)

| Metric | Healthy range | Alert |
|---|---|---|
| KB freshness (avg days since update) | ~14 days | ~30 days |
| Assumption validation rate | ~40%+ | ~20% |
| Decision velocity (per month) | ~2-5 | 0 or ~10+ |
| Research freshness (% within threshold) | ~70%+ | ~50% |
| Workspace files (non-archive) | ~80 | ~120+ |
| Workflow sizes | ~80-120 lines | ~120+ |

### Promotion Logic (ongoing reference)

| Condition | Action |
|---|---|
| New market insight with sourced data | Append to `market-intel.md` |
| Assumption stated or validated/invalidated | Update `assumptions-tracker.md` |
| UX pattern designed and approved | Create/update in `product-design/` |
| Strategic decision made | Immutable entry in `decision-log/` |
| Design decision made | Immutable entry in `design-decisions/` |
| Deep research conducted | Archive in `research/` with freshness date |
| No promotable insights | No writes — conversation artifact suffices |

Default path is discard.

### Decision Record Template (ongoing reference)

```markdown
# YYYY-MM-DD — [Decision Title]
## Context
[What prompted this decision]
## Options Evaluated
| Option | Pros | Cons | Risk |
## Decision
[What was chosen and why]
## Assumptions Made
[Cross-reference assumptions-tracker.md]
## Reversal Trigger
[What would cause us to revisit]
```

---
---

## APPENDIX A: Product Design Brief

> **Purpose:** This appendix preserves the deep UX thinking from the original design sessions. Read this BEFORE writing any product-design file in Phase 3. Without this context, the files will be generic.

### The Core Thesis

CarrotFin is not building a better finance dashboard. It's building an **AI-native financial advisor** where:
- The UI is **generated**, not pre-designed — the AI assembles screens from components based on user context
- The experience is **adaptive** — the same "screen" looks fundamentally different for a 25-year-old first-time investor vs. a 45-year-old portfolio optimizer
- Conversation and visualization are **woven**, not separated — there is no "chat tab" and "dashboard tab"; the AI conversation IS the interface, with visual elements appearing inline

### Why This Matters (the anti-patterns to avoid)

Every existing Indian personal finance app (CRED, ET Money, INDmoney, Groww) follows the same paradigm:
- **Static dashboard** with pre-designed screens showing portfolio, transactions, goals
- **Chat/support** as a separate surface (if it exists at all)
- **One-size-fits-all** — a novice and an expert see the same UI
- **Feature accumulation** — more features = more menu items = more complexity

CarrotFin rejects all of this. The design files must articulate WHY and HOW.

### The Five Design Axioms (for `design-principles.md`)

1. **Adaptive-first** — Every touchpoint must ask: "How does this change for a different user?" This isn't responsive design (screen sizes). It's behavioral adaptation (financial literacy, risk tolerance, life stage, goals, emotional state). If a screen looks the same for two different users, it's a design failure.

2. **Conversational + visual integration** — The AI doesn't live in a chat window. It speaks through the UI itself. A chart appears because the AI decided this user needs to see their spending trend right now. A suggestion card appears because the AI identified a savings opportunity. The conversation IS the navigation.

3. **Hyperpersonalization is the architecture** — Personalization isn't a feature layer on top of a generic app. The entire UI stack is built to reason about the user. Data models include user state. Components accept user context as input. The AI decides what to show, when, and how.

4. **Generative UI thinking** — Don't design static screens. Design **systems of constraints** — component libraries, composition rules, and context triggers that the AI uses to assemble the right interface. Ask: "What does the AI need to compose this dynamically?"


### Screen Taxonomy (for `screen-taxonomy.md`)

| Type | Definition | When to use | Example |
|---|---|---|---|
| **Generative** | AI assembles the screen from components based on user context. No two users see the same layout. | Primary experience screens — home, financial health, recommendations | Home screen showing personalized insights: spending alert for User A, investment opportunity for User B |
| **Static** | Pre-designed, fixed layout. Same for all users (though content may vary). | Regulatory, legal, settings, account management | Terms of service, KYC flow, profile settings |
| **Hybrid** | Fixed structure, but key sections are AI-generated. | Transitional screens where some elements must be consistent. | Goal planning: fixed form structure, but AI-generated suggestions and projections inline |

### Interaction Model (for `interaction-model.md`)

The interaction model defines how conversation and visual surfaces integrate:

- **AI-led flow:** The AI proactively surfaces information, asks questions, and guides the user. Visuals appear as the AI "speaks." The user responds conversationally or by interacting with visual elements.
- **User-led flow:** The user asks a question or navigates to a topic. The AI responds with a mix of text and visual elements (charts, cards, comparisons).
- **Ambient awareness:** The AI notices patterns and surfaces nudges without being asked. A spending spike triggers a gentle notification with a visualization attached.

**Key question each file must address:** When does conversation lead vs. visualization lead? Answer: Conversation leads for discovery, exploration, education. Visualization leads for comparison, tracking, decision-making.

### Initial Design Tensions (for `tension-log.md`)

| Tension | Force A | Force B | Why unresolved |
|---|---|---|---|
| **Simplicity vs. financial rigor** | Users want 1-tap answers, jargon-free language | Financial accuracy requires nuance, caveats, and context | Resolved differently per user segment — need validated user segments first |
| **Conversational vs. visual** | Some decisions need dialogue (exploration, education) | Some insights need charts (portfolio performance, spending trends) | The right balance depends on the specific user action, not a global setting |
| **Personalization vs. privacy** | Deep personalization requires deep data access | Users are wary of sharing financial data with AI | Trust must be earned progressively — but where's the threshold? |
| **AI confidence vs. transparency** | Users trust confident recommendations | Responsible AI requires showing uncertainty | Need to design confidence indicators that don't undermine trust |
| **Speed vs. thoughtfulness** | Users expect instant responses (modern app UX) | Financial decisions benefit from deliberate pacing | Need to distinguish "fast information" from "slow decisions" |

### Component Pattern Philosophy (for `component-patterns/`)

Components are **not static UI widgets**. They are adaptive building blocks:
- Each component accepts **user context** as input (financial literacy level, risk tolerance, emotional state)
- Each component has **adaptive behavior specs** — how it changes appearance, content density, and interaction model based on context
- Components are designed to be **composed by AI** — the AI selects and arranges components, it doesn't just fill in templates

Example: A "spending insight card" shows a headline + emoji for a novice user, but shows headline + sparkline chart + category breakdown for an experienced user. Same component, different rendering.
