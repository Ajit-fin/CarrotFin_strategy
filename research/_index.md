# Research — Index & Freshness Tracker

> **Purpose:** Index of deep research artifacts. Each entry includes a freshness date and staleness threshold.  
> **Last updated:** 2025-04-14  
> **Staleness threshold:** 30 days (for this index)

---

## Active Research

| File | Domain | Topic | Last Updated | Stale After | Status |
|---|---|---|---|---|---|
| *(no research artifacts yet)* | | | | | |

---

## Subdirectory Structure

| Directory | Contents |
|---|---|
| `ux-patterns/` | UX pattern research — adaptive UI, conversational UI, generative interfaces |
| `market/` | Market research — sizing, trends, consumer behavior data |
| `competitors/` | Competitor deep-dives — teardowns, UX analyses, feature comparisons |
| `technology/` | Technology research — AI/ML capabilities, frontend frameworks, API integrations |

## Freshness Rules

- Research artifacts older than **90 days** should be moved to `_archive/` within each subdirectory (Phase 5 creates these)
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
