---
title: "Event Handling and Response"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, detect, respond]
related: [../../06-decisions/ADR-004-evidence-and-observability-model.md, ./runtime-authorization-context.md, ./kernel-observability.md]
---

# Event Handling and Response

## Purpose

Event Handling and Response closes the Detect → Respond part of the GovOps loop (see
`../architecture/README.md#4-the-govops-loop`). It consumes the joined Runtime Authorization
Context and Kernel Observability data to identify drift, anomalies, or policy violations, and
triggers a remediation action — automated or human.

## Responsibilities

- Detect drift between what was authorized (Runtime Authorization Context) and what actually
  executed (Kernel Observability), keyed on `capability_id`.
- Trigger response actions when a violation or anomaly is identified.
- Feed findings back into the Govern step, closing the loop (e.g., a repeatedly-denied capability
  prompts a policy review; a capability observed executing without any authorization record
  prompts a catalog/policy gap review).

## Non-responsibilities

- Does not make the original authorization decision — that is the Policy Decision Point.
- Does not itself generate execution evidence — that is Kernel Observability.
- Does not define what "revoked" means for work already in flight — that is a named open gap (see
  below), not something this component currently resolves.

## Event context fields

An event handled by this component is built from the joined decision and execution records, and
typically carries:

- `capability_id` — the correlation key joining decision and execution data.
- `decision` — what was authorized (allow/deny/challenge), from the Runtime Authorization Context.
- Execution evidence — what actually happened, from Kernel Observability.
- `event_type` and `timestamp` — classify and place the finding in time.

A formal `event_id` is not yet defined as part of the information model — see
`../information-model/README.md` for the current state of identifier definitions.

## Example responses

| Trigger | Example response |
|---|---|
| Anomalous execution following a `deny` decision | Terminate the execution; alert security |
| Credential revoked mid-execution | Revoke associated sessions/tokens where possible (see gap below) |
| Capability observed executing with no matching decision record | Quarantine the workload; escalate to governance review |
| Repeated denials for a capability | Escalate to policy review (Govern step) |

## Known open gap: credential revocation during execution

A credential backing an in-flight action may be revoked mid-execution. Today, GovOps does not
define a formal contract for what "revoked" means for work already underway: whether the
execution is expected to stop, what evidence proves it did or did not, and how that gets recorded
against the original decision record. This is tracked as an explicit, unresolved gap — see
`../../06-decisions/ADR-004-evidence-and-observability-model.md` and
`../../01-explanation/illustrative-use-cases/governance-scenarios.md#credential-revocation-during-execution`.

## Alignment with the Shared Signals Framework (SSF)

SSF's model of standardized security event transmission between systems is a natural
complementary layer for this component's event-emission side, even though GovOps does not
currently specify SSF as its transport. See
`../../01-explanation/positioning/README.md#relationship-to-the-shared-signals-framework-ssf`.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| Decision record | Runtime Authorization Context | Half of the join for detecting drift | Yes |
| Execution evidence | Kernel Observability | Other half of the join | Yes |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| Findings/events | Capability Catalog, Policy Management | Feed the Govern step, closing the loop |
| Response actions | Runtime infrastructure | Automated or human remediation |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| Event/finding records | Event Handling and Response | Runtime Plane | No formal `event_id` defined yet. |

## Dependencies

- Runtime Authorization Context and Kernel Observability, as joint inputs.

## Interfaces

- Not yet formally specified — see `../interfaces/README.md`.

## Security and trust considerations

This component is a natural target for detection-evasion concerns: if an attacker can suppress or
delay the Kernel Observability half of the join, drift may go undetected. Kernel-level emission is
intended to be harder to blind than application-level logging (see `../security/README.md`).

## Observability and evidence

Findings generated here are themselves evidence, feeding both Continuous Compliance and
Governance Metrics.

## Related decisions

- `../../06-decisions/ADR-004-evidence-and-observability-model.md`
