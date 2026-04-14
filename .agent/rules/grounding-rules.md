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
