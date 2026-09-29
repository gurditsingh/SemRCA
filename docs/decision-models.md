# Decision Models in SemRCA

SemRCA treats semantic decision models as operators inside a larger deterministic system.

JEV is one example of this style.

## The useful pattern

A decision model takes structured or unstructured state and returns a constrained judgment.

```text
Context
   ↓
Semantic decision model
   ↓
Choice / score / probability
   ↓
Code decides what happens next
```

This differs from asking a generative model to produce the entire investigation result.

## Good uses

Decision models are attractive when the task is fuzzy but bounded.

Examples:

- Is this transformation relevant to the incident?
- Could this commit affect the failing metric?
- Which of these lineage branches should be explored first?
- Are these two fields semantically related?
- Is this anomaly consistent with the observed symptom?
- Should this evidence item be kept in the reasoning context?

These decisions can reduce the number of objects sent to a larger reasoning model.

## Poor uses

A semantic decision model should not replace deterministic facts.

Do not use it to guess:

- whether a table exists
- the exact schema
- the current row count
- Git commit timestamps
- exact lineage when the catalog already provides it
- query results
- deployment status

If a fact can be retrieved exactly, retrieve it.

## Typed decisions

The output should be constrained.

Examples:

```text
RELEVANT | NOT_RELEVANT | UNSURE
```

```json
{
  "choice": "RELEVANT",
  "score": 0.84
}
```

or:

```json
{
  "candidate_id": "commit:abc123",
  "causal_plausibility": 0.73
}
```

Typed outputs are easier to test, cache, compare, and compose.

## Calibration

Scores should not be treated as universal truth.

A score of 0.8 from one model or prompt version may not mean the same thing after a model upgrade.

SemRCA should evaluate decision models using incident-like datasets and track:

- precision
- recall
- ranking quality
- calibration
- latency
- cost
- failure modes

The optimization target is often not maximum classification accuracy. For RCA candidate pruning, missing the true root cause may be much more expensive than allowing a few extra candidates through.

## Provenance

If a decision changes investigation behavior, record:

- model
- model version
- decision specification
- input object references
- output
- score
- timestamp

This makes the decision explainable and allows later re-evaluation.

## Relationship to deeper reasoning

Decision models and reasoning models serve different roles.

```text
Graph traversal
    ↓
Decision model pruning
    ↓
Focused candidate set
    ↓
Reasoning model investigation
    ↓
Deterministic verification
```

The goal is not to replace large reasoning models everywhere. It is to use them after the search space has been reduced and the relevant evidence has been assembled.
