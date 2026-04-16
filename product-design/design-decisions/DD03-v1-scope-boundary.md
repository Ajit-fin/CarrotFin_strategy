# DD03: V1 Scope Boundary — Discovery Through Contribution Plan

> **Date:** 2026-04-16
> **Phase:** Phase 1 — Product Scoping
> **Tension resolved:** T5 (Speed vs. Deliberation) — partially. V1 defers monitoring and crisis flows to reduce scope complexity.
> **Assumptions depended on:** EF-11, C4 (adaptive rendering viable for key surfaces)
> **Source decision:** D1 + D3 in `research/market/emergency-fund-philosophy.md §10` + Phase 1 deliberation

---

## Context

The full Emergency Fund lifecycle has 8 phases: Discovery, Assessment, Target Setting, Fund Architecture, Contribution Planning, Monitoring & Nudging, Crisis Mode (Withdrawal + Rebuild), and Recalibration. Building all 8 phases for V1 is too broad given the pre-funding stage and the principle that this is a private beta to learn, not a market launch.

The question: where exactly does V1 end?

Two viable boundaries were considered:
1. V1 ends at **Target Setting** (Phase 3) — user knows their number, but hasn't been told where to put the money or how to build it. Fast to ship, but leaves the user with no clear action path.
2. V1 ends at **Contribution Plan confirmed** (Phase 5) — user has a target, an allocation strategy (instrument type), and a monthly saving plan. Maximum actionable advisory value without entering transaction territory.

A third option — V1 includes monitoring — was considered but rejected on grounds that: (a) monitoring requires time-series data (contribution history) that doesn't exist at first launch, and (b) it adds significant UI complexity without being critical to the learning goal of V1.

---

## Options Evaluated

| Option | V1 End Point | Pros | Cons | Risk |
|---|---|---|---|---|
| **A: End at Target Setting (Phase 3)** | User knows their number | Fastest to ship. Core advisory value delivered. | No action path — user is left with a number and no guidance on what to do with it. High abandonment after target reveal likely. | High — incomplete user experience. |
| **B: End at Contribution Plan (Phase 5) — chosen** | User has a plan | Full advisory cycle complete. User has number + allocation + monthly plan. Actionable. Monitoring and crisis flows can wait for real usage data. | Slightly larger scope than A. | Low — the right scope for a meaningful first use case. |
| **C: Include Monitoring Surface (Phases 1–6)** | User can track progress | More complete product feel. Home surface has a live EF tracker. | Monitoring is hollow at first launch (no contribution data yet). Adds significant UI surface complexity. Requires self-reporting or AA integration. | Medium — premature for V1. |

---

## Decision

**Option B: V1 covers Phases 1–5 (Discovery → Assessment → Target Setting → Fund Architecture → Contribution Plan).**

This is the minimum viable advisory cycle — the user completes V1 knowing:
1. Whether/how much they need (assessed, personalized)
2. Why their number is what it is (attributed, transparent)
3. Where to put the money (instrument-type allocation)
4. How to get there (monthly contribution plan + milestone architecture)

What is explicitly deferred:
- **Monitoring & Nudging (Phase 6):** V2. Requires real contribution data over time.
- **Crisis Mode — Withdrawal + Rebuild (Phase 7):** V2. High design complexity; better designed after real usage pattern is observed.
- **Recalibration on Life Event (Phase 8, active UX):** V2. Trigger logic is embedded in V1 data model; the active UX flow is deferred.
- **Insurance Gap dedicated flow:** Separate product feature. V1 flags the gap conversationally (advisory), no dedicated flow.
- **Archetype shortcut ("You seem like a Metro Professional"):** Deferred. Full 8-dimension assessment builds a richer initial profile. Shortcuts risk oversimplification at first launch.

---

## Consequences

**Enables:**
- Engineering has a clean, bounded scope: 5 journey phases + app shell.
- Phase 6 (monitoring) can be designed fresh with real user data — no premature design decisions.
- Private beta can generate the most important early signal: do users set up a plan? Do they follow through?

**Constrains:**
- V1 cannot demonstrate EF growth or progress over time — monitoring is a post-launch feature. Retention in V1 depends on the ambient nudge layer (push notifications, home surface EF card) — which must be designed even if the full monitoring surface is deferred.
- The "Rebuild Mode" (after crisis withdrawal) is significant behavioral design work — the research's design for this (automate replenishment, celebrate rebuilding) is important but V2 scope.
- The recalibration trigger data model must be built in V1 even though the active UX is V2 — engineering cannot defer the life-event tracking schema.

---

## Reversal Trigger

Reverse the monitoring deferral if: Private beta users explicitly ask "Is my fund growing?" within the first 30 days — this signals the monitoring surface is a retention driver, not a nice-to-have. Move it to V1.5 if so.
