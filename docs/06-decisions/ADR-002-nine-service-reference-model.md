---
title: "ADR-002: Nine-Service Reference Model"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, services]
related: [../02-reference/components/README.md]
---

# ADR-002: Nine-Service Reference Model

## Status
Proposed — flags an unresolved inconsistency rather than proposing a final answer.

## Context
The published architecture draft states "the core GovOps services are the Capability Catalog, Policy Management, Schema Management, Federation Management, and Continuous Compliance" (five), but its table of contents documents nine service sections in total (also Reference Architecture, Runtime Authorization Context, Governance Metrics, Kernel Observability, Event Handling and Response), without explaining the exclusion of the other four from "core."

## Decision
Not yet made. Candidate options: (a) declare all nine equally "core" and drop the five-item claim; (b) keep a "core five" as the minimum viable deployment and treat the other four as "extended" services; (c) reorganize around a different grouping entirely (e.g. Governance Plane services vs. Runtime Plane services).

## Consequences
Whichever option is chosen affects how Conformance (`ADR-006`) eventually gets scoped — a normative spec would likely need to specify requirements per service, and "core vs. extended" changes what's mandatory.

## Alternatives considered
Not yet evaluated in detail — this ADR exists to make the inconsistency visible and force a decision, per the group's item 3 agenda ("stabilize the core reference model").

## Links
`../02-reference/components/README.md`, `../07-project-and-roadmap/open-questions.md`
