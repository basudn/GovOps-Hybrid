---
title: "Conformance"
status: draft
document_type: reference
source_of_truth: true
normativity: non-normative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, conformance]
related: [../../06-decisions/ADR-006-conformance-postponed.md, ../architecture/README.md]
---

# Conformance

## Status: non-normative

This specification does not currently define conformance criteria — there is no MUST/SHOULD
requirements language for implementers to satisfy. Everything in `../` (architecture, components,
information model) is **informative**: it describes a reference model, not a certifiable
implementation target.

## Why conformance is postponed

Writing binding conformance requirements now would lock in decisions the working group has not
actually made. Three specific blockers are tracked as open ADRs:

- The five-vs-nine-service naming inconsistency is unresolved — a conformance section would need
  to specify per-service requirements, and it is not yet decided which services are mandatory for
  a minimal deployment versus which are extensions. See
  `../../06-decisions/ADR-002-nine-service-reference-model.md`.
- The information model is incomplete — `trace_execution_id` and other identifiers needed for a
  full authorization-to-execution contract are not yet defined. See
  `../../06-decisions/ADR-003-information-model-identifier-strategy.md`.
- Several components (Federation Management, Policy Management, Schema Management) still lack
  fully agreed non-responsibility boundaries.

See `../../06-decisions/ADR-006-conformance-postponed.md` for the full reasoning.

## What this means for readers

- Nothing in this documentation set should be read as "an implementation must do X to be GovOps
  conformant." Read it as a reference architecture and vocabulary, not a certification standard.
- The intended AI-agent readability of this documentation (structured front matter, explicit
  status fields) should not be mistaken for normative authority — `status: draft` and
  `normativity: informative` mean exactly what they say.

## Planned exception: the Authorization Capability Catalog

The Capability Catalog (`../components/capability-catalog.md`) is expected to be the first part
of GovOps to receive a normative specification — its schema (`#AuthorizationCapability`),
`capability_id` hash convention, and lint rules are the most mature and stable part of the model.
This is planned, not yet done, and is gated on resolving the open questions tracked against
GovOpsWG/GovOps issue #17.
