# Research — Index & Freshness Tracker

> **Purpose:** Index of deep research artifacts. Each entry includes a freshness date and staleness threshold.  
> **Last updated:** 2026-04-18  
> **Staleness threshold:** 30 days (for this index)

---

## Active Research

| File | Domain | Topic | Last Updated | Stale After | Status |
|---|---|---|---|---|---|
| [emergency-fund-philosophy.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/research/market/emergency-fund-philosophy.md) | Market | Emergency Fund — adaptive sizing philosophy, India-specific structural realities, 3-layer liquidity architecture, behavioral design principles, 9 user archetypes | 2026-04-16 | 2026-07-16 | ✅ Active |
| [voice-tts-architecture-options.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/research/technology/voice-tts-architecture-options.md) | Technology | Voice/TTS architecture scenarios — edge SLM vs. Gemini unified vs. hybrid, evaluated against CarrotFin latency/Indian English/privacy constraints. Recommends cloud V1 → hybrid V2. | 2026-04-18 | 2026-07-18 | ✅ Active |

---

## Subdirectory Structure

| Directory | Contents |
|---|---|
| `ux-patterns/` | UX pattern research — adaptive UI, conversational UI, generative interfaces |
| `market/` | Market research — sizing, trends, consumer behavior data |
| `competitors/` | Competitor deep-dives — teardowns, UX analyses, feature comparisons |
| `technology/` | Technology research — AI/ML capabilities, frontend frameworks, API integrations |

## Freshness Rules

- Research artifacts older than **90 days** should be moved to `_archive/` within each subdirectory
- **One-in-one-out:** When a new research artifact on the same topic is created, the old one moves to `_archive/`
- All research must carry inline `[as of YYYY-MM-DD]` annotations on data points
- Total active research files should stay under ~20. More signals insufficient synthesis.

## Quality Standards

When adding research via `/research`:

| Tier | Source Type | Reliability | Use |
|---|---|---|---|
| **Tier 1** | Primary data (surveys, financial filings, government stats) | High | Cite directly, use for quantitative claims |
| **Tier 2** | Expert analysis (McKinsey, CB Insights, industry reports) | Medium-High | Cross-validate with Tier 1 if available |
| **Tier 3** | Secondary sources (blog posts, news articles, social media) | Low-Medium | Directional only, flag as "needs validation" |

- Minimum **2 sources** for any quantitative claim, or flag as single-source
- Cross-validate competitor claims (don't take marketing materials at face value)

---

*Updated by `/research` and `/feedback` workflows.*
