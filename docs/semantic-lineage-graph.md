# Semantic Lineage Graph

The semantic lineage graph is the central reusable knowledge structure in SemRCA.

Its purpose is to connect technical lineage with the information needed to investigate impact and causality.

## Graph scope

Traditional lineage often answers:

> What depends on this table?

SemRCA needs to answer richer questions:

- Which metric depends on this column?
- Which transformation changed before the incident?
- Which consumer is affected?
- Who owns the relevant upstream object?
- Which code path produced this target?
- What deployment introduced the changed logic?
- Which anomaly appeared first in the dependency chain?

That requires more than dataset-to-dataset edges.

## Candidate node types

Initial node types could include:

- system
- dataset
- field
- transformation
- job
- code artifact
- commit
- deployment
- metric
- business concept
- dashboard
- report
- API
- owner
- incident
- anomaly
- evidence item

The graph should start small. New node types should be added only when they support a concrete query or RCA workflow.

## Candidate edge types

Examples include:

```text
READS_FROM
WRITES_TO
TRANSFORMS_TO
DERIVES_FIELD
IMPLEMENTS
CHANGED_BY
DEPLOYED_AS
DEFINES
CONSUMES
OWNED_BY
OBSERVED_IN
AFFECTS
SUPPORTS
CONTRADICTS
```

Edge type is not enough. Provenance and time matter.

## Provenance

Every graph fact should answer:

- Where did this relationship come from?
- When was it observed?
- Is it deterministic or inferred?
- What raw artifact can verify it?

A useful edge envelope is:

```json
{
  "type": "CHANGED_BY",
  "valid_from": "2026-09-20T15:02:00Z",
  "observed_at": "2026-09-20T15:05:10Z",
  "source": "git",
  "source_ref": "commit:abc123",
  "confidence": 1.0,
  "inference": false
}
```

## Temporal behavior

RCA is inherently temporal.

The relevant question is often not "what depends on this today?" but "what depended on this when the incident happened?"

The graph should therefore preserve enough history to reconstruct relationships around an incident window. Full bitemporal modeling may be unnecessary at first, but overwriting historical edges will eventually make incident reconstruction unreliable.

## Semantic annotations

Some relationships cannot be extracted deterministically.

For example, a code block may be semantically related to a business metric even when no catalog exposes the relationship.

These annotations can be useful, but they should be stored differently from hard lineage. Persist:

- model name/version
- input references
- prompt or decision specification version
- output
- score/probability
- timestamp

This makes semantic enrichment reproducible and replaceable.

## Query patterns

The graph should support fast queries such as:

```text
consumer → metric → target → transformation → source
```

and:

```text
changed commit
    ↓
affected transformations
    ↓
downstream targets
    ↓
metrics / dashboards
```

During an incident, the graph should produce a bounded neighborhood for investigation instead of sending the entire estate to a reasoning model.

## Storage choice

The logical graph is more important than the initial database choice.

A relational implementation can work first if edges and node identities are modeled explicitly. A native graph database becomes attractive when traversal patterns, temporal queries, and neighborhood expansion dominate.

The architecture should keep the graph API stable enough that storage can change later.
