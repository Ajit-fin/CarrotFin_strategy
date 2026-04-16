# Chart & Data Visualization Guidelines

> **Skill type:** Design intent resource  
> **Invoked by:** `/ux-designer`, `/mockup`, `/buildspec` (on-demand)  
> **Last updated:** 2026-04-16  
> **Scope:** Design intent only — no technology or library prescriptions

---

## Purpose

CarrotFin is a personal finance app — charts and financial data visualizations are core to the experience. This file defines how financial data should be visualized within CarrotFin's adaptive surfaces. It covers chart type selection, adaptive rendering by user context, accessibility requirements, and mobile-specific constraints.

**Not covered:** General visual identity (see `visual-identity-guidelines.md`), component adaptive behavior specs (see `ux-design-system/`), or implementation technology (dev workspace concern).

---

## 1. Chart Type Selection

Match chart type to data shape. Financial data has recurring patterns — use the right visual for each.

| Data Type | Recommended Chart | When Used in CarrotFin | Avoid |
|---|---|---|---|
| **Trend over time** (portfolio value, spending trajectory) | Line chart | Portfolio performance, spending tracking, SIP projection | Bar chart for time series — loses continuity |
| **Comparison** (this month vs. last, fund A vs. fund B) | Grouped bar chart | Spending comparison, fund benchmark comparison | Pie chart for comparison — hard to compare arc lengths |
| **Proportion/breakdown** (asset allocation, spending categories) | Donut chart | Portfolio allocation, spending breakdown by category | Pie chart with >5 categories — becomes unreadable. Switch to horizontal bar |
| **Progress toward target** (goal funded %, emergency fund %) | Progress bar / gauge | Goal trackers, savings milestones | Full chart — overpowered for a single metric |
| **Projection/scenario** (SIP growth, retirement corpus) | Area chart or line chart with confidence bands | FIRE projections, goal target projections, "what-if" scenarios | Stacked bar — misrepresents continuous future values |
| **Distribution** (spending across categories, income sources) | Horizontal bar chart | Spending categories ranked by amount, income source breakdown | Vertical bar on mobile — horizontal labels are unreadable |
| **Single key metric** (net worth, monthly savings rate) | Large number with delta indicator | Home surface headline metrics, at-a-glance views | Chart — when a number is the answer, show the number |

---

## 2. Adaptive Chart Rendering

Charts adapt to user context, just like every other component in CarrotFin.

### By Financial Literacy Level

| Literacy Level | Chart Treatment | What Changes |
|---|---|---|
| **Level 1-2** (novice) | Simplified: single data series, large labels, embedded explanation | No axis labels with financial jargon. The AI provides a text explanation *before* the chart ("Here's how your savings have grown"). Color coding uses obvious metaphors |
| **Level 3** (intermediate) | Standard: labeled axes, legend, comparison data points | Benchmark comparisons shown. Brief AI annotation on notable patterns |
| **Level 4-5** (advanced) | Data-dense: multi-series, delta metrics, percentage annotations, trendlines | Full axis labels, time-scale toggle, exportable data. Minimal explanatory text — the data speaks |

### By Emotional State

| Emotional State | Chart Adaptation |
|---|---|
| **Anxious** (market downturn, overspending) | Longer time horizons for portfolio charts (emphasize recovery trajectory, not today's dip). Spending charts use neutral color for overspend zones (not alarming red). AI annotation leads with reassurance |
| **Excited** (milestone, positive returns) | Highlight the achievement — annotated milestone marker on growth chart. Brief celebration moment before showing full context |
| **Overwhelmed** | Simplify: show only the single most important data point. Offer "show more detail" rather than presenting dense charts |

---

## 3. Accessibility Requirements

Financial data must be accessible to all users. These are non-negotiable.

| Rule | Standard | CarrotFin Application |
|---|---|---|
| **Data/background contrast** | ≥3:1 for data lines, bars, areas against background | Portfolio charts must be readable on budget Android AMOLED and LCD screens |
| **Color + pattern** | Color is never the sole differentiator between data series | Asset allocation donut slices must use patterns or textures in addition to color (colorblind users) |
| **Legend always visible** | Legend positioned near chart, not below a scroll fold | On mobile, legend should be integrated into or directly adjacent to the chart — not requiring a scroll |
| **Tooltip on interaction** | Tap (mobile) or hover (web future) shows exact value | Chart data points must have ≥44pt touch targets. If the data point is small, expand the tap area |
| **Screen reader summary** | Every chart has an `aria-label` or text summary describing the key insight | "Your portfolio grew 12% over the past year" — the chart's purpose, not a raw data dump |
| **Animation respects reduced motion** | Chart entrance animations disable with reduced-motion preference. Data readable immediately | No waiting for a bar to grow or a line to draw before the user can see their numbers |
| **Data table alternative** | For complex charts, offer a table view toggle | Particularly important for multi-series portfolio and spending charts |

---

## 4. Mobile-Specific Constraints

Charts on phones have physical limitations. Designing for these constraints prevents frustrating experiences.

| Constraint | Guideline |
|---|---|
| **Horizontal space** | Prefer horizontal bar charts over vertical bar charts when category labels are text (spending categories, fund names). Avoids rotated labels |
| **Touch targets** | Interactive chart elements (data points, pie slices, toggle buttons) must have ≥44pt tap area. If the element is smaller, expand the touch zone invisibly |
| **Axis label density** | Auto-skip axis ticks when spacing would fall below readable thresholds. Show fewer ticks with clear intervals rather than cramped labels |
| **Responsive simplification** | Charts designed for larger screens should simplify on small phones — fewer data points, condensed legends, abbreviated labels |
| **Orientation** | Charts should remain functional and readable in both portrait and landscape. Complex charts may offer landscape-optimized presentation |
| **Scroll context** | Charts within conversational streams should not create nested scroll regions. Scrolling the chart should be the same gesture as scrolling the conversation |

---

## 5. Financial-Specific Rules

Finance data has its own visual language. These rules ensure CarrotFin's charts feel financially literate.

| Rule | Why | Example |
|---|---|---|
| **Tabular/monospaced figures for amounts** | Prevents numbers from shifting as values change, keeps columns aligned | ₹1,23,456 must align vertically with ₹98,765 in a comparison view |
| **Indian number formatting** | Indian lakh/crore system, not million/billion | ₹1,23,45,678 not ₹123,456,78. Currency symbol always present |
| **Time-scale clarity** | Financial projections span years — time axis must clearly label granularity and allow switching | "Monthly → Yearly → Total" toggle for SIP projections. Current view always labeled |
| **Inflation context** | Large future numbers without inflation context are misleading | When showing "you'll have ₹2Cr in 2050," annotate with real (inflation-adjusted) value |
| **Benchmark framing** | Performance without benchmark is meaningless | Portfolio returns should always show benchmark comparison (Nifty 50, FD rate) when relevant |
| **Negative value treatment** | Losses need careful visual treatment — not just "red goes down" | Use visual weight, not just color, to indicate negative values. AI annotation should contextualize ("Your portfolio is down 3% this week — markets overall are down 5%") |

---

## 6. Empty & Error States

Every chart must have a defined experience for when data isn't available or fails to load.

| State | Experience |
|---|---|
| **No data yet** (new user, new goal) | Encouraging message + visual placeholder suggesting what the chart *will* show. "As you start saving, this chart will show your progress." Not a blank axis frame |
| **Insufficient data** (too few data points for meaningful trend) | Show what's available with a note: "We'll be able to show you a trend after 2 more months of data." |
| **Data load failure** | Clear error message with retry action. "Couldn't load your portfolio data. Tap to retry." Not a broken chart frame or spinner that never resolves |
| **Stale data** (cached from last session, not live) | Show cached data with timestamp: "Last updated 2 hours ago." Provide refresh action |

---

*This file defines visualization intent for financial data. Library selection, rendering technology, and chart component implementation are dev workspace decisions. BuildSpec documents should carry these guidelines as data visualization constraints.*
