---
title: "Components"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, components]
related: [../architecture/component-view.md, ../../06-decisions/ADR-002-nine-service-reference-model.md]
---

# Components

This section describes the major services of the GovOps reference architecture. See
`../architecture/component-view.md` for how they relate to one another and
`../architecture/README.md` for the two-plane model and GovOps loop these services implement.

| Service | Plane | Purpose |
|---|---|---|
| [Capability Catalog](./capability-catalog.md) | Governance | Inventories every governed action-resource pair and assigns `capability_id`. |
| [Policy Management](./policy-management.md) | Governance | Authors, versions, and distributes policy content. |
| [Schema Management](./schema-management.md) | Governance | Governs the structure of catalog and policy data. |
| [Federation Management](./federation-management.md) | Governance | Decides which external issuers/token sources are trusted. |
| Continuous Compliance | Governance | Exports capability-level evidence via Gemara/OSCAL. Documented under `../../03-metrics-and-compliance/compliance-path/README.md` rather than duplicated here. |
| [Policy Decision Point](./policy-decision-point.md) | Runtime | Evaluates a request against published policy; stateless, atomic. |
| [Runtime Authorization Context](./runtime-authorization-context.md) | Runtime | Minimal decision-record correlation that crosses the plane boundary. |
| [Kernel Observability](./kernel-observability.md) | Runtime | Independent, OS-level proof of what actually executed. |
| [Event Handling and Response](./event-handling-and-response.md) | Runtime | Detect/Respond steps of the GovOps loop. |
| Governance Metrics | Governance | Continuous quantitative signals of governance health. Documented under `../../03-metrics-and-compliance/governance-metrics/README.md` rather than duplicated here. |

## Known inconsistency: five core services vs. nine total

Earlier GovOps material describes "the core GovOps services" as five (Capability Catalog, Policy
Management, Schema Management, Federation Management, Continuous Compliance) while also
documenting nine services in total (adding Runtime Authorization Context, Governance Metrics,
Kernel Observability, and Event Handling and Response), without explaining why the other four are
excluded from "core." This inconsistency has not been resolved — see
`../../06-decisions/ADR-002-nine-service-reference-model.md`.
