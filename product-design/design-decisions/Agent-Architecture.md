# Agent Architecture — Consolidated Design Decisions

> **Consolidates:** DD13, DD14  
> **Date range:** 2026-07-21 to 2026-07-28  
> **Last updated:** 2026-07-29  
> **Full architecture:** [buildspecs/_index.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/_index.md)

---

## DD13: Split Planner Architecture

**Decision:** Decompose monolithic `pro_planning_v1.xml` into two specialized agents — **Overview Planner** (goal-level reasoning, EF target sizing, scenario comparison) and **Detail Planner** (step-level construction, UI component assembly, contribution plan generation). Both share modules via `<moduleRef>` injection.

**Rationale:** Single planner had competing objectives (strategy vs. micro-steps) causing quality degradation and token bloat. Split enables independent optimization per agent.

**Assumptions:** (none — architectural decision based on prompt quality, not user behavior assumptions)

**Reversal trigger:** Routing overhead or module sync burden outweighs quality gains, or a future model handles full planning scope efficiently in one call.

---

## DD14: flash_extraction v1 → v2 (Group-Aware)

**Decision:** Deprecate and delete `flash_extraction_v1.xml`. Rebuild as `flash_extraction_v2.xml` with native group-aware entity resolution — outputs entity-scoped field extractions (entity ID + field + value), not flat key-value pairs.

**Rationale:** v1 assumed individual-centric flat output; bolting on entity resolution would be architectural debt, not a native capability. Clean rebuild avoids extraction errors from retrofitting.

**Assumptions:** GP-01

**Reversal trigger:** Entity resolution accuracy <80% on representative test set, or Flash model latency for v2 exceeds real-time conversation bounds.
