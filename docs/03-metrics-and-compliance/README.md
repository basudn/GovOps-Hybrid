---
title: "Metrics and Compliance"
status: draft
document_type: overview
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, metrics, compliance]
related: [../02-reference/README.md, ../06-decisions/ADR-004-evidence-and-observability-model.md, ../06-decisions/ADR-007-gemara-for-compliance-path.md]
---

# Metrics and Compliance

This is GovOps's evidence layer: how governance behavior is measured over time, how technical
events map to assurance workflows, and where the limits of what GovOps can claim about compliance
are drawn. It sits downstream of the Runtime Plane — every metric and every compliance artifact
here is derived from the same `capability_id`-anchored decision stream used for authorization
itself (see `../02-reference/architecture/component-view.md#the-capability_id-data-flow-trace`).

- [Governance Metrics](./governance-metrics/README.md) — the admissions test, design rules, and
  the one fully worked metric (Denial Ratio Trend), plus a roadmap of planned metrics.
- [Traceability](./traceability/README.md) — which architecture fields and services support each
  metric, and where the gaps are.
- [Compliance Path](./compliance-path/README.md) — the Gemara → OSCAL → framework mapping path,
  and the explicit boundary on what GovOps does and does not claim about compliance.
- [Evidence](./evidence/README.md) — the authorization-evidence / execution-evidence split, and
  open questions on retention and auditability.
