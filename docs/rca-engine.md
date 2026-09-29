# RCA Engine

The SemRCA RCA engine coordinates investigation. It does not own every fact and it should not ask a single model to solve the whole incident in one prompt.

## Investigation lifecycle

```text
1. Symptom
2. Scope
3. Lineage neighborhood
4. Candidate generation
5. Semantic narrowing
6. Deep investigation
7. Evidence collection
8. Verification
9. Explanation
```

## 1. Symptom

An investigation starts from a concrete observation.

Examples:

- metric dropped 18%
- dashboard tile is empty
- target table is stale
- row count changed sharply
- source schema changed
- consumer query started failing

The symptom should include time, environment, affected object, and observed behavior whenever possible.

## 2. Scope

The engine first resolves the symptom to canonical graph objects.

It should identify:

- affected consumer or target
- relevant time window
- environment
- metric or field where applicable
- initial evidence

## 3. Lineage neighborhood

Deterministic traversal walks upstream and laterally through known dependencies.

The objective is not to return the entire graph. It is to build a bounded investigation neighborhood.

Bounds can include:

- hop count
- time window
- object type
- ownership domain
- deployment window
- anomaly window

## 4. Candidate generation

Candidates are possible causal events or objects.

Examples:

- recent code change
- schema change
- failed job
- source freshness problem
- data anomaly
- dependency change
- semantic definition change

Candidate generation should prefer explicit events over generic objects.

## 5. Semantic narrowing

A decision model can score or classify candidates against the observed symptom.

Example decision:

```text
Given:
- failing metric
- transformation summary
- code diff
- observed anomaly

Decide:
Could this candidate plausibly explain the symptom?
```

The result is a prioritization signal, not proof.

## 6. Deep investigation

A stronger reasoning model can investigate the smaller candidate set.

Its tasks may include:

- form hypotheses
- understand complex transformation logic
- choose additional evidence to collect
- compare multiple candidate causes
- identify missing verification steps

## 7. Evidence collection

The engine calls deterministic tools to collect facts.

Examples:

- execute a read-only query
- retrieve a commit diff
- compare schemas
- inspect job logs
- inspect row counts
- retrieve deployment times
- traverse exact lineage edges

## 8. Verification

A root-cause claim should have explicit supporting evidence.

The engine should distinguish:

```text
Hypothesis
Supported hypothesis
Verified root cause
Rejected candidate
Unknown
```

A confidence score alone should not promote a hypothesis to verified root cause.

## 9. Explanation

The final RCA should explain:

- what happened
- where the issue originated
- why it propagated
- what evidence supports the conclusion
- which consumers were affected
- what remains uncertain
- possible remediation and prevention actions

## Investigation state

The RCA engine should persist investigation state separately from the semantic graph.

A useful record may contain:

- incident
- hypotheses
- candidate set
- semantic scores
- tool calls
- collected evidence
- rejected candidates
- final conclusion
- citations to raw artifacts

This creates an auditable investigation trail and allows an analyst to resume or challenge an RCA without reconstructing the entire session from model chat history.
