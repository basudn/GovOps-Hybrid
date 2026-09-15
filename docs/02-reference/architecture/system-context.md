---
title: "System Context (C4 Level 1)"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, architecture, c4]
related: [./README.md, ./component-view.md]
---

# System Context (C4 Level 1)

This is the highest-level view of GovOps: who uses it, what it depends on, and where its boundary
sits relative to systems it does not itself define. See `./README.md` for the loop and layer model
this diagram implements, and `../../00-foundations/scope.md` for the precise boundary statement in
prose form.

## Actors and external systems

| Actor / system | Relationship to GovOps |
|---|---|
| Organizational governors (security, audit, compliance, engineering, finance, legal) | Consume and act on GovOps's catalog, decisions, and metrics — see `../../01-explanation/stakeholder-goals/README.md`. |
| Policy decision engines (OPA, Cedar, Cerbos, XACML, custom) | External to GovOps; make the actual runtime allow/deny call. GovOps requires only that decisions can be tagged with a `capability_id`. |
| Kernel/process observability tools (Cilium, Falco, Sysdig, Tetragon, Linux Audit, Windows ETW, macOS Endpoint Security) | External; supply execution evidence. GovOps does not implement or replace them — see `../components/kernel-observability.md`. |
| GRC / audit tooling | Consumes compliance evidence exported through the Gemara/OSCAL path. |
| OSCAL / Trestle | External compliance-interoperability layer that GovOps's Continuous Compliance service targets, rather than reinventing. |
| Identity providers / token issuers | External; Federation Management governs which of these an organization trusts, but does not implement token issuance or validation itself. |

## Diagram

```mermaid
C4Context
    title GovOps — System Context (C4 Level 1)

    Person(governor, "Organizational Governor", "Security, audit, compliance, engineering, finance, legal")

    System_Boundary(govops, "GovOps") {
        System(govopsSystem, "GovOps", "Capability-centric governance: catalog, correlation, metrics, compliance extension")
    }

    System_Ext(pdp, "Policy Decision Engine", "OPA, Cedar, Cerbos, XACML, custom — makes the runtime decision")
    System_Ext(kernelObs, "Kernel/Process Observability", "Cilium, Falco, Sysdig, Tetragon, OS audit subsystems")
    System_Ext(grc, "GRC / Audit Tooling", "Consumes compliance evidence")
    System_Ext(oscal, "OSCAL / Trestle", "Compliance interoperability layer")
    System_Ext(idp, "Identity Providers / Token Issuers", "External trust sources")

    Rel(governor, govopsSystem, "Defines capabilities, reviews metrics, consumes evidence")
    Rel(govopsSystem, pdp, "Tags decisions with capability_id (does not replace)")
    Rel(kernelObs, govopsSystem, "Supplies execution evidence, joined on capability_id")
    Rel(govopsSystem, oscal, "Exports capability-level evidence via Gemara")
    Rel(oscal, grc, "Framework-specific renderings (ISO 27001, SOC 2, EU CRA)")
    Rel(idp, govopsSystem, "Issues tokens trusted per Federation Management decisions")
```

GovOps's boundary is deliberately narrow: it owns the capability data model, the correlation
identifiers, the metrics, and the compliance extension point. It does not own policy evaluation,
token issuance/validation, or kernel-level instrumentation — those remain external systems that
GovOps integrates with via `capability_id` correlation. See `../../00-foundations/scope.md` for the
full "what GovOps does not (yet) define" list.
