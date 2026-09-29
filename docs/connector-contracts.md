# Connector Contracts

SemRCA should connect to many enterprise systems without embedding vendor-specific assumptions in the RCA engine.

The connector contract therefore needs to be small, typed, and capability-based.

## Connector categories

A connector can implement one or more categories:

- **Source connector** — operational databases, APIs, files, streams, SaaS systems.
- **Transformation connector** — dbt, SQL repositories, Spark, ETL tools, orchestration systems.
- **Target connector** — warehouses, lakehouses, databases, files, serving stores.
- **Consumption connector** — BI tools, metric systems, notebooks, APIs, applications.
- **Metadata connector** — catalogs, observability platforms, governance systems, ownership directories.

A product can implement multiple categories. The categories describe capabilities, not deployment boundaries.

## Core object identity

Every object exposed to SemRCA should have a stable identity.

A minimal form could be:

```json
{
  "system": "snowflake",
  "instance": "prod",
  "kind": "table",
  "namespace": "analytics.finance",
  "name": "daily_revenue"
}
```

A connector should also provide its native identifier when one exists.

Stable identity matters because the graph must merge observations from different systems without treating every name match as the same object.

## Minimum capabilities

Not every connector needs every capability. It should advertise what it supports.

Possible capabilities include:

- list objects
- describe object
- list fields
- fetch schema
- fetch lineage
- fetch query or transformation definition
- fetch ownership
- fetch freshness
- fetch history
- fetch change events
- execute safe read-only query
- resolve native URL
- subscribe to change events

## Normalized records

Connectors should return normalized records even when the underlying APIs differ.

Examples:

```text
Object
Field
Edge
Transformation
ChangeEvent
Owner
Metric
Consumer
Evidence
```

Vendor-specific metadata can be preserved in an extension field rather than leaking into every downstream interface.

## Lineage edge contract

A basic edge should identify:

- upstream object
- downstream object
- edge type
- scope
- discovery source
- confidence
- observed time
- provenance

For example:

```json
{
  "from": "snowflake:prod:raw.orders",
  "to": "snowflake:prod:analytics.daily_revenue",
  "type": "transforms_to",
  "source": "dbt_manifest",
  "confidence": 1.0
}
```

Deterministic lineage should normally have confidence 1.0. Inferred or semantic relationships should be clearly labeled as inferred.

## MCP and API

MCP can be useful as an interaction layer, but it should not become the domain model.

The recommended direction is:

```text
Vendor SDK / API
       ↓
Connector implementation
       ↓
Canonical SemRCA contract
       ↓
REST / gRPC / Events / MCP
```

This allows the same connector to support programmatic ingestion, batch graph building, interactive agents, and local development.

## Security

Connector permissions should be least-privilege.

Read-only metadata access should be separated from query execution. Query execution should be separately governed, logged, and limited. Secrets should remain in the connector boundary rather than being copied into model context.

## Versioning

Connector responses should be versioned because the graph and RCA engine will evolve independently.

A connector should be replaceable without changing the core RCA workflow, and a graph migration should not require rewriting every vendor adapter.
