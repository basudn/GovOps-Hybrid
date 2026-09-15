---
title: "Framework Mappings"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, compliance, oscal]
related: [./gemara-to-oscal.md, ./README.md]
---

# Framework Mappings

Consolidated status of GovOps's mapping work from the OSCAL compliance-interoperability layer to
specific frameworks and to OSCAL's own control-implementation model. All entries below are
**planned, not yet built** — see `./README.md#what-govops-can-and-cannot-claim` for the boundary
this implies.

| Target | Status | Notes |
|---|---|---|
| OSCAL control mapping | Planned | Would map specific GovOps information-model fields (`capability_id`, decision records, execution evidence) to specific OSCAL control-implementation statements, via Trestle. No mapping exists yet. |
| ISO 27001 | Planned | Would consume an existing OSCAL ISO 27001 profile once the underlying Gemara-to-OSCAL mapping exists. Not yet mapped. |
| SOC 2 | Planned | Would consume an existing OSCAL SOC 2 profile once the underlying Gemara-to-OSCAL mapping exists. Not yet mapped. |
| EU Cyber Resilience Act (CRA) | Planned | Not yet mapped. OpenSSF has already anchored related work to the CRA — worth evaluating whether their mapping can be reused rather than re-derived, though this has not yet been formally investigated or agreed. |

No timeline is committed for any of the above. See `../../07-project-and-roadmap/README.md` for
the broader roadmap this work sits within.
