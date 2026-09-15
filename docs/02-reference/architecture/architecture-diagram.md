---
title: "Architecture Diagram"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, architecture]
related: [./README.md, ./component-view.md, ./system-context.md, ../components/README.md]
---

# Architecture Diagram

This is the high-level, single-glance view of the GovOps architecture: the two planes, the nine
services, and how a decision and its evidence flow between them. For the C4 Level 1 boundary (what
is inside GovOps vs. external), see `./system-context.md`. For the full C4 Level 2 container
diagram with every relationship labeled, see `./component-view.md`.

```mermaid
graph TD
    subgraph "Governance Plane"
        CAT["Capability Catalog"]
        POL["Policy Management"]
        SCH["Schema Management"]
        FED["Federation Management"]
    end

    subgraph "Runtime Plane"
        PDP{"Policy Decision Point"}
        APP["Application / PEP"]
        RAC["Runtime Authorization Context"]
        KO["Kernel Observability"]
        EHR["Event Handling and Response"]
    end

    subgraph "Cross-cutting"
        CC["Continuous Compliance"]
        GM["Governance Metrics"]
    end

    SCH -- "Defines structure" --> CAT
    SCH -- "Defines structure" --> POL
    CAT -- "capability_id referenced by policy" --> POL
    FED -- "Trusted issuers" --> PDP
    POL -- "policy_store_id / policy_store_version" --> PDP
    APP -- "Authorization request" --> PDP
    PDP -- "Decision" --> APP
    PDP -- "Decision record" --> RAC
    RAC -- "Joined on capability_id" --> KO
    RAC -- "Feeds Detect" --> EHR
    KO -- "Feeds Detect" --> EHR
    EHR -- "Findings close the loop" --> CAT
    RAC -- "Decision evidence" --> CC
    KO -- "Execution evidence" --> CC
    RAC -- "Decision stream" --> GM

    style CAT fill:#f9f,stroke:#333,stroke-width:2px
    style POL fill:#f9f,stroke:#333,stroke-width:2px
    style SCH fill:#f9f,stroke:#333,stroke-width:2px
    style FED fill:#f9f,stroke:#333,stroke-width:2px
```

This diagram intentionally shows all nine services named in `../components/README.md` rather than
only the five sometimes called "core" — see
`../../06-decisions/ADR-002-nine-service-reference-model.md` for that unresolved naming question.
Continuous Compliance and Governance Metrics are drawn as cross-cutting rather than inside either
plane, consistent with `./README.md#5-the-four-functional-layers`.

The loop this diagram implements, in words, is `./README.md#4-the-govops-loop`:
`Govern → Authorize → Execute → Observe → Detect → Respond → Govern`.
