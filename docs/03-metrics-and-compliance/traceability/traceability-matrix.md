---
title: "Traceability Matrix"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, traceability]
related: [./README.md, ../governance-metrics/README.md, ../governance-metrics/planned-metrics.md, ../evidence/README.md]
---

# Traceability Matrix

## Architecture field / service → ACC workflow → metric → gap

| Architecture field / service | ACC workflow it supports | Metric that reads it | Gap? |
|---|---|---|---|
| Capability Catalog (`capability_id`, risk-tier, business-impact) | Capability authoring, compliance mapping | Denial Ratio Trend (segmentation), Capability Drift Rate | No |
| Policy Management (`policy_store_id`, `policy_store_version`) | Policy authoring and versioning | Denial Ratio Trend (version-split qualifier), Policy Decision Latency | No |
| Runtime Authorization Context (decision record) | Decision-time evaluation | Denial Ratio Trend, Policy Decision Latency | No |
| Kernel Observability (execution verification) | Execution verification, drift detection | Trace Completeness, Capability Drift Rate | **Yes** — no kernel tool emits `capability_id` natively |
| Federation Management (issuer trust) | Third-party / federated trust review | — | **Yes** — no formal interface defined between Federation Management and the rest of the estate |
| Event Handling and Response (revocation, termination) | Detect → Respond | Credential Revocation Response Time | **Yes** — mechanics of a revoked credential still associated with an in-flight execution are undefined (ADR-004) |
| Compliance interoperability layer (OSCAL control implementation) | Continuous compliance evidencing | Control Evidence Freshness | **Yes** — no OSCAL control mapping exists yet (`../compliance-path/framework-mappings.md`) |

Every row marked **Yes** traces back to one of the six named architecture gaps in
`../../07-project-and-roadmap/README.md#open-questions` or to an ADR's open consequences; none of
these gaps is newly discovered here — this table exists to show that they are the *same* gaps
recurring across the catalog, the runtime record, and the metrics, not independent problems.

## Service → metric mapping

| Service | Metric(s) it feeds |
|---|---|
| Runtime Authorization Context | Denial Ratio Trend, Policy Decision Latency |
| Kernel Observability | Trace Completeness, Capability Drift Rate |
| Event Handling and Response | Credential Revocation Response Time |
| Compliance interoperability layer (OSCAL / Trestle) | Control Evidence Freshness |

Two services in the reference model — Schema Management and Federation Management — currently feed
no metric in this set. Schema Management's absence is consistent with it being the least
fleshed-out service (see `../../02-reference/components/schema-management.md`). Federation
Management's absence is the same interface gap flagged in the matrix above.

## Evidence chain model

The chain a single capability exercise should produce, end to end:

```text
authorization evidence  →  execution evidence  →  aggregated event  →  compliance evidence
(Runtime Authorization      (Kernel               (Event Handling       (OSCAL control
 Context decision record)    Observability)         and Response)         implementation)
```

- **Authorization evidence** answers why a request was allowed, denied, or challenged, fixed at
  decision time (see `../evidence/README.md`).
- **Execution evidence** answers what actually happened, continuing to accrue after the decision.
- **Aggregated event** is what Event Handling and Response produces when execution evidence
  diverges from what the decision authorized (e.g. a revoked credential associated with an
  in-flight execution) — see
  `../../02-reference/components/event-handling-and-response.md`.
- **Compliance evidence** is the framework-specific rendering an auditor consumes, produced by the
  OSCAL/Trestle layer described in
  `../compliance-path/gemara-to-oscal.md#the-three-layer-compliance-architecture`.

Today, the first two links are defined (per the Runtime Authorization Context and Kernel
Observability component pages); the third depends on the event-handling gap named above, and the
fourth depends on the OSCAL control mapping, which is planned but not built. The chain is
documented here as a target shape, not a working pipeline — see
`../../07-project-and-roadmap/README.md` for what is committed and what is not.
