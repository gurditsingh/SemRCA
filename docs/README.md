# SemRCA Design Notes

This directory turns the high-level SemRCA idea into smaller design documents that can evolve independently as the project moves from architecture exploration to implementation.

## Documents

- [Architecture](architecture.md) — boundaries between Source, Transformation, Target, Semantic, Consumption, and the RCA engine.
- [Connector Contracts](connector-contracts.md) — a vendor-neutral contract for source, target, transformation, and consumption adapters.
- [Semantic Lineage Graph](semantic-lineage-graph.md) — the canonical graph that joins technical lineage, semantics, code, ownership, and operational evidence.
- [RCA Engine](rca-engine.md) — the investigation lifecycle from symptom to verified explanation.
- [Decision Models](decision-models.md) — where small semantic decision models such as JEV fit, and where they should not be used.

## Working Principle

SemRCA separates three kinds of work:

1. **Deterministic retrieval and computation** for facts.
2. **Semantic decisions** for fuzzy relevance, classification, and prioritization.
3. **Deep reasoning** for hypothesis formation, evidence synthesis, and explanation.

The architecture should keep these responsibilities explicit. A model score is not lineage, a hypothesis is not evidence, and a generated explanation is not a verified root cause.

## Status

These documents are design notes, not frozen specifications. Interfaces and graph schemas should remain versioned and replaceable while experiments establish which abstractions are useful in production.
