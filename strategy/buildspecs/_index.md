# BuildSpec Inventory

> **Domain:** Handoff  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** N/A (event-driven updates)

---

## What This Directory Contains

Compiled BuildSpec artifacts — self-contained product intent specifications for handoff to the dev workspace. Each BuildSpec is assembled by the `/buildspec` workflow from existing journey definitions, component patterns, research outputs, and design decisions.

BuildSpecs are consumed by an external Antigravity agent with zero CarrotFin context, as input to its HLD→LLD process.

---

## Inventory

| Spec ID | Flow Name | Status | Date | Supersedes |
|---|---|---|---|---|
| — | *No BuildSpecs compiled yet* | — | — | — |

---

## Status Values

| Status | Meaning |
|---|---|
| **Draft** | Compiled but not yet reviewed/approved by the user |
| **Ready for Build** | Reviewed, approved, and ready to hand off to dev workspace |
| **In Dev** | Handed off and actively being built |
| **Built** | Dev agent completed the build |
| **Superseded** | Replaced by a newer BuildSpec (link to successor in the spec) |

---

## Conventions

- **Naming:** `BS-[NNN]-[flow-name].md` — sequential numbering, kebab-case flow name
- **Immutability:** Once status is "Ready for Build", the file is immutable. Product changes create a new BuildSpec that supersedes the old one.
- **Dependency tracking:** BuildSpecs that depend on other BuildSpecs note this in §5.3 (Adjacent Flows) and §6.1 (Dependencies).

---

*Updated by `/buildspec` workflow after each compilation. Update status manually when the dev workspace begins/completes a build.*
