---
title: "Traceability"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, traceability]
related: [./traceability-matrix.md, ../governance-metrics/README.md, ../evidence/README.md]
---

# Traceability

This section answers a question none of the individual reference or metric pages answer on their
own: for a given architecture field or service, which Authorization Capability Catalog (ACC)
workflow does it support, which metric (if any) reads it, and is there a named gap in the chain?
Reading the components, the metrics, and the evidence model separately hides where they connect and,
more usefully, where they don't yet.

## Contents

- [Traceability Matrix](./traceability-matrix.md) — the architecture-field → ACC-workflow → metric →
  gap table, the service-to-metric mapping, and the evidence-chain model.

## Why this exists

Several gaps named individually elsewhere in this documentation are the same gap viewed from
different angles:

- `trace_execution_id` is named as undefined in
  `../../02-reference/information-model/README.md#core-identifiers`.
- The same absence blocks two planned metrics — Capability Drift Rate and Trace Completeness — in
  `../governance-metrics/planned-metrics.md`.
- The same absence is why Kernel Observability cannot yet emit `capability_id` natively
  (`../../02-reference/components/kernel-observability.md#known-gap-no-kernel-tool-emits-capability_id-natively`).

The matrix exists so a reader hits this once, in one table, rather than re-discovering it three
times in three different sections.
