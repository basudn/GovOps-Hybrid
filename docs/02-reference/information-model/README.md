---
title: "Information Model"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, information-model]
related: [../../06-decisions/ADR-003-information-model-identifier-strategy.md, ../components/capability-catalog.md, ../components/runtime-authorization-context.md]
---

# Information Model

This is the consolidated normative-in-spirit reference for GovOps's core identifiers, decision
model, and policy model — the data GovOps actually defines and passes between planes. See
`../architecture/component-view.md#the-capability_id-data-flow-trace` for how these identifiers
flow through the system end-to-end.

## Core identifiers

| Identifier | Defined? | Owner | Description |
|---|---|---|---|
| `capability_id` | Yes | Capability Catalog | SHA-256 hash of `<group-slug>|<action-slug>|<resource-slug>`. See `../components/capability-catalog.md#the-capability_id-convention`. |
| `decision_id` | Yes | Runtime Authorization Context | Unique per PDP decision instance. |
| `policy_store_id` | Yes | Policy Management | Identifies which policy store/bundle produced a decision. |
| `policy_store_version` | Yes | Policy Management | Identifies which revision of that store was in force. |
| `trace_execution_id` | **No — gap** | — | Would link a decision to the specific execution it authorized. Candidate: minted at Execute-step start, analogous to a distributed trace ID. See `../../06-decisions/ADR-003-information-model-identifier-strategy.md`. |
| Credential reference | **No — gap** | — | No formal identifier for "which credential backed this decision," needed for the credential-revocation-during-execution scenario. |
| Runtime/workload identifier | **No — gap** | — | No formal identifier for the process/workload that executed an authorized action, beyond what Kernel Observability's underlying tools happen to capture. |
| Event identifier (`event_id`) | **No — gap** | — | Event Handling and Response currently only has `event_type` and `timestamp`, no unique event ID. |

Leaving `trace_execution_id` and the credential/runtime/event identifiers undefined currently
blocks: the authorization-to-execution contract (see `../interfaces/README.md`), the
authorization-vs-execution evidence separation (`../../06-decisions/ADR-004-evidence-and-observability-model.md`),
and several planned metrics (Trace Completeness, Capability Drift Rate, Credential Revocation
Response Time — see `../../03-metrics-and-compliance/governance-metrics/planned-metrics.md`).

## The `capability_id` hash formula

```text
capability_id = SHA-256( "<group-slug>|<action-slug>|<resource-slug>" )
```

Only the group/action/resource slugs are hashed — optional metadata (risk-tier,
business-impact, etc.) is never part of the preimage, so a capability's identity is stable even
as its risk assessment changes. Full detail and a worked example: `../components/capability-catalog.md#the-capability_id-convention`.

## Decision model

A Policy Decision Point decision is one of three outcomes:

| Outcome | Meaning |
|---|---|
| `allow` | The request is permitted. |
| `deny` | The request is refused. |
| `challenge` | Neither — the PDP requires additional evidence before it can decide. Carries `challenge_id`, `reason`, `required_evidence`, `expires_at`. See `../components/policy-decision-point.md#decision-outcomes-allow-deny-and-challenge`. |

Every decision is recorded as a Runtime Authorization Context record with the five defined core
fields (`capability_id`, `decision`, `decision_id`, `policy_store_id`, `policy_store_version`) —
deliberately minimal by design. See `../components/runtime-authorization-context.md`.

## Policy model

Policy is authored and published by Policy Management as a versioned artifact, identified by
`policy_store_id` + `policy_store_version`. Policy statements reference capabilities by
`capability_id` rather than restating action/resource strings, and conditions are expressed using
the entity/attribute vocabulary defined by Schema Management. See `../components/policy-management.md`
and `../components/schema-management.md`.

## Related decisions

- `../../06-decisions/ADR-003-information-model-identifier-strategy.md`
- `../../06-decisions/ADR-004-evidence-and-observability-model.md`
