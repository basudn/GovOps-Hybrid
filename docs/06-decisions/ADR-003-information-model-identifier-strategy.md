---
title: "ADR-003: Information Model Identifier Strategy"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, information-model]
related: [../02-reference/information-model/README.md]
---

# ADR-003: Information Model Identifier Strategy

## Status
Proposed

## Context
`capability_id`, `decision_id`, `policy_store_id`, and `policy_store_version` are defined in the current draft. `trace_execution_id`, formal credential references, runtime identifiers, and event identifiers are not, despite being needed for the authorization-to-execution contract (item 6) and for metrics like Trace Completeness.

## Decision
Not yet made. Candidate: define `trace_execution_id` as a per-execution identifier minted at Execute-step start, analogous to a distributed trace id, joined to `capability_id` and `decision_id`.

## Consequences
Leaving these undefined blocks item 6 (authorization-to-execution contract), item 7 (evidence separation), and several proposed metrics (Trace Completeness, Capability Drift Rate, Credential Revocation Response Time).

## Alternatives considered
Not yet evaluated.

## Links
`../02-reference/information-model/core-identifiers.md`, `../03-metrics-and-compliance/governance-metrics/trace-completeness.md`
