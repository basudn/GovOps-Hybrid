---
title: "Runtime Authorization Context"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, runtime, observability]
related: [../../06-decisions/ADR-003-information-model-identifier-strategy.md, ../../06-decisions/ADR-004-evidence-and-observability-model.md, ./policy-decision-point.md, ./kernel-observability.md]
---

# Runtime Authorization Context

## Purpose

The Runtime Authorization Context is the minimal decision record that crosses the boundary
between the Governance Plane and the Runtime Plane. It exists so that a single Policy Decision
Point decision can be correlated against independent execution evidence (Kernel Observability),
detection/response tooling (Event Handling and Response), compliance evidence (Continuous
Compliance), and governance metrics — all without those downstream consumers needing to share
anything else about the original request.

## Responsibilities

- Emit exactly one decision record per PDP decision (allow, deny, or challenge — see
  `./policy-decision-point.md#decision-outcomes-allow-deny-and-challenge`).
- Carry only the minimum fields needed for cross-plane correlation.
- Serve as the single join key — `capability_id` — that Kernel Observability, Event Handling and
  Response, and Continuous Compliance all correlate against independently.

## Non-responsibilities

- **Never carries the token itself.**
- **Never carries policy text.** Only `policy_store_id` and `policy_store_version` are included —
  the content they refer to is fetched separately, if needed, from Policy Management.
- **Never carries the full request payload.** No request body, headers, or arbitrary attributes
  are included beyond what the minimal field set below specifies.

This is a deliberate minimality choice, not an oversight: keeping the record minimal is what
makes it safe to fan out to multiple downstream consumers (some of which may have different trust
levels) without each one becoming a new place sensitive data can leak. See
`../security/README.md` for the fuller rationale.

## Minimum fields

| Field | Purpose | Status |
|---|---|---|
| `capability_id` | The capability the decision was about | Defined (see `./capability-catalog.md`) |
| `decision` | `allow` \| `deny` \| `challenge` | Defined |
| `decision_id` | Unique identifier for this specific decision instance | Defined |
| `policy_store_id` | Which policy store produced the decision | Defined (see `./policy-management.md`) |
| `policy_store_version` | Which version of that store was in force | Defined |

Additional identifiers that would extend this record — a `trace_execution_id` linking a decision
to the specific execution it authorized, a credential reference, or a runtime/workload identifier
— are named but not yet defined. See
`../../06-decisions/ADR-003-information-model-identifier-strategy.md` for the open question.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| Decision outcome | Policy Decision Point | The record's `decision` field | Yes |
| `capability_id` | Policy Decision Point (originally from Capability Catalog) | Correlation key | Yes |
| `policy_store_id` / `policy_store_version` | Policy Decision Point (originally from Policy Management) | Reproducibility of the decision | Yes |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| Decision record | Kernel Observability | Joined on `capability_id` for independent execution proof |
| Decision record | Event Handling and Response | Feeds the Detect step |
| Decision record | Continuous Compliance | Decision evidence for Gemara/OSCAL export |
| Decision record | Governance Metrics | Input to the decision stream metrics are computed from |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| `decision_id` | Runtime Authorization Context | Runtime Plane | Unique per decision instance, never reused. |

## Dependencies

- Policy Decision Point, as the sole producer of decision records.

## Interfaces

- Emitted as an event/record consumed by Kernel Observability, Event Handling and Response, and
  Continuous Compliance. The exact wire format/transport is not yet standardized — see
  `../interfaces/README.md`.

## Security and trust considerations

Because this record is deliberately minimal, it can be treated as safe to distribute more broadly
than the request that produced it — no secret, token, or policy logic is exposed even if the
record itself is later widely queried for metrics or audit purposes.

## Observability and evidence

This record *is* the primary observability artifact of the Runtime Plane's Authorize step. Its
completeness (is a record emitted for every decision, with no gaps) is itself a candidate metric
— see `../../03-metrics-and-compliance/governance-metrics/planned-metrics.md` (Trace Completeness).

## Related decisions

- `../../06-decisions/ADR-003-information-model-identifier-strategy.md`
- `../../06-decisions/ADR-004-evidence-and-observability-model.md`
