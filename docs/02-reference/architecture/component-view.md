---
title: "Component View"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, architecture, c4]
related: [./README.md, ./system-context.md, ../components/README.md]
---

# Component View

This view sits between the system-context boundary (`./system-context.md`) and the individual
component reference pages (`../components/`). It shows how the services relate to one another and
traces a single capability's data through the full lifecycle.

## Services and relationships

```mermaid
C4Container
    title GovOps — Component View

    Container(catalog, "Capability Catalog", "Governance Plane", "Assigns and stores capability_id + metadata")
    Container(policy, "Policy Management", "Governance Plane", "Authors, versions, distributes policy")
    Container(schema, "Schema Management", "Governance Plane", "Governs catalog/policy data structure")
    Container(federation, "Federation Management", "Governance Plane", "Issuer trust decisions")
    Container(compliance, "Continuous Compliance", "Governance Plane", "Gemara/OSCAL export")
    Container(pdp, "Policy Decision Point", "Runtime Plane", "Stateless, atomic runtime decision")
    Container(runtimeCtx, "Runtime Authorization Context", "Runtime Plane", "Minimal decision-record correlation")
    Container(kernelObs, "Kernel Observability", "Runtime Plane", "Independent execution evidence")
    Container(event, "Event Handling and Response", "Runtime Plane", "Detect/Respond")
    Container(metrics, "Governance Metrics", "Governance Plane", "Continuous quantitative signals")

    Rel(catalog, policy, "capability_id referenced by policy")
    Rel(schema, catalog, "Defines catalog structure")
    Rel(schema, policy, "Defines policy store structure")
    Rel(federation, pdp, "Trusted-issuer list feeds evaluation")
    Rel(policy, pdp, "policy_store_id, policy_store_version")
    Rel(pdp, runtimeCtx, "Emits decision record")
    Rel(runtimeCtx, kernelObs, "Joined on capability_id")
    Rel(runtimeCtx, event, "Feeds Detect step")
    Rel(kernelObs, event, "Feeds Detect step")
    Rel(runtimeCtx, compliance, "Decision evidence")
    Rel(kernelObs, compliance, "Execution evidence")
    Rel(runtimeCtx, metrics, "Decision stream")
    Rel(event, catalog, "Findings close the loop back to Govern")
```

## The `capability_id` data-flow trace

The single most important property of this architecture is that one identifier —
`capability_id` — is traceable end-to-end without ever requiring the intermediate systems to share
anything else:

```text
Capability Catalog (capability_id assigned at Register)
    │
    ▼
Policy Management (policy references capability_id; policy_store_id/version assigned)
    │
    ▼
Policy Decision Point (evaluates request; decision made)
    │
    ▼
Runtime Authorization Context (capability_id, decision, decision_id, policy_store_id,
                                policy_store_version — minimal record)
    │
    ├──▶ Kernel Observability (independent execution evidence, joined on capability_id)
    │
    ├──▶ Event Handling and Response (Detect/Respond, keyed on capability_id)
    │
    └──▶ Continuous Compliance (Gemara → OSCAL export, keyed on capability_id)
```

No token, policy text, or full request payload crosses these boundaries — only the identifiers
themselves (see `../components/runtime-authorization-context.md`). This is what makes the
correlation mechanism safe to use as a cross-cutting join key: it is minimal by design (see
`../security/README.md`).

## Relationship to the nine-service model

This view intentionally shows ten boxes (nine GovOps services plus the two-plane grouping),
consistent with `../components/README.md`'s enumeration. See
`../../06-decisions/ADR-002-nine-service-reference-model.md` for the unresolved question of why
only five of these are sometimes called "core."
