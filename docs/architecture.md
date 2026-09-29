# SemRCA Architecture

SemRCA is organized around a simple enterprise data flow:

```text
SOURCE
   ↓
TRANSFORMATION
   ↓
TARGET
   ↓
SEMANTIC
   ↓
CONSUMPTION
```

The RCA engine sits across these layers rather than replacing them.

## Why the layers are separate

The layers describe different responsibilities.

### Source

A Source is where data originates.

Examples include operational databases, SaaS systems, APIs, files, object storage, event streams, and external feeds.

Source integrations should expose facts such as schemas, objects, freshness, ownership, and upstream identifiers. They should not contain RCA-specific reasoning.

### Transformation

Transformation is where data changes meaning or shape.

Examples include SQL, dbt, Spark, Python, stored procedures, notebooks, ETL tools, and orchestration jobs.

Transformation is important enough to be a first-class layer because it contains the logic that explains how an upstream state becomes a downstream state. Git history, deployment metadata, parsing, and code analysis belong close to this layer.

### Target

A Target is where transformed data lands.

Examples include warehouses, lakehouses, databases, files, feature stores, analytical stores, and serving tables.

Target is intentionally separate from Consumption. A Snowflake table may be a target even when ten dashboards, two APIs, and an analyst notebook consume it.

### Semantic

The Semantic layer connects physical data structures to meaning.

It may contain metric definitions, business entities, column meaning, ownership, transformation semantics, lineage, schema history, code references, and dependency relationships.

This is not only a BI semantic layer. In SemRCA it is the shared knowledge layer used to connect technical facts with business impact.

### Consumption

Consumption is where people or applications experience the result.

Examples include dashboards, reports, metrics, notebooks, APIs, applications, alerts, and ad-hoc analysis.

Keeping Consumption separate allows SemRCA to answer questions such as:

- Which reports are affected by this target table?
- Which business metric depends on this column?
- Is an incident isolated to one dashboard or shared across many consumers?

## Cross-cutting services

The five layers describe the data estate. SemRCA adds cross-cutting services around them.

```text
Connectors
   ↓
Metadata + Lineage Extraction
   ↓
Semantic Lineage Graph
   ↓
Candidate Narrowing
   ↓
Deep Investigation
   ↓
Evidence Verification
   ↓
RCA / Report / Ad-hoc Analysis
```

### Connector services

Adapters translate vendor-specific systems into a small common contract. They can expose APIs, MCP tools, or both.

### Semantic graph service

The graph is the reusable memory of the platform. It should be queryable without invoking an LLM.

### RCA engine

The RCA engine orchestrates graph traversal, semantic narrowing, evidence collection, queries, and deeper reasoning.

### Reporting and investigation

Reporting is an output mode, not a new data layer. It can consume the same graph and RCA evidence used by ad-hoc investigation.

## Boundary rule

A useful implementation rule is:

> Store facts in the systems that own them, normalize relationships in the semantic graph, and keep probabilistic judgments in the investigation layer unless they are explicitly persisted with provenance.

This keeps the architecture auditable and prevents model output from silently becoming ground truth.
