---
title: "ADR-007: Gemara for Compliance Path"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, compliance, gemara]
related: [../03-metrics-and-compliance/compliance-path/gemara-to-oscal.md]
---

# ADR-007: Gemara for Compliance Path

## Status
Proposed

## Context
At a CSA webinar, credible identity figures (Dick Hardt, Imran) could not answer how an authorization architecture ties to compliance. GovOps needs a concrete, defensible answer, not a bespoke one invented from scratch.

## Decision
Capability-level governance and execution evidence maps into Gemara (an existing open compliance-content vocabulary), which maps to OSCAL (NIST's machine-readable compliance format, via Trestle tooling), which already has profiles for real frameworks (EU CRA, SOC 2, ISO 27001).

## Consequences
GovOps does not need to build its own compliance taxonomy or maintain its own framework mappings — any framework already mapped to OSCAL is reachable. This also means GovOps's compliance credibility is partly dependent on Gemara/OSCAL's own adoption and correctness, an external dependency.

## Alternatives considered
- **Invent a bespoke GovOps compliance taxonomy**: rejected — more work, less credible, no existing tooling/adoption to lean on.
- **Map directly to each framework without an intermediate vocabulary**: rejected — doesn't scale, requires a new mapping per framework instead of reusing OSCAL's existing profiles.

## Links
`../03-metrics-and-compliance/compliance-path/gemara-to-oscal.md`, `../01-explanation/01-problem/csa-compliance-gap.md`
