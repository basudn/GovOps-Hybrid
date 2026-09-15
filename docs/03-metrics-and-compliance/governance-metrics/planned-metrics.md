---
title: "Planned Metrics"
status: proposed
document_type: metric-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, metric, roadmap]
related: [./README.md, ./denial-ratio-trend.md, ../traceability/traceability-matrix.md]
---

# Planned Metrics

## Status

Proposed / parked. Each entry below has passed the admissions test in
`./README.md#what-counts-as-a-govops-metric` (states a change across two windows) but has not yet
received a full worked example, a limitations section, or a decided calculation — the bar this
directory sets for `Accepted` (see `./README.md#status-labels`). None of the five is fit to publish
as-is; each is recorded here so the working group has a stable list to draft against, following the
lighter template in `../../_templates/metric-template.md`.

Only [Denial Ratio Trend](./denial-ratio-trend.md) is fully specified today.

## Policy Decision Latency

**Purpose.** Is the time between a policy change being committed and that change taking effect at
every enforcement point moving in the right direction?

**Formula.** Not yet defined. The candidate shape is a change in the distribution (e.g. p50/p95) of
`decision_time - policy_commit_time`, computed per policy store and compared across two windows —
consistent with this set's requirement that a metric report a change, not a level.

**Scope.** `policy_store_id` / `policy_store_version` are Runtime Authorization Context fields
already defined (see `../../02-reference/components/runtime-authorization-context.md`); the missing
piece is a reliable "policy became current" timestamp, which ADR-003's open question on what makes a
policy version "current" (commit, release, or distribution-confirmed) directly blocks.

**Data sources**

| Source | Fields used | Collection component |
|---|---|---|
| Policy Management | `policy_store_id`, `policy_store_version`, commit/publish timestamp | Policy Management |
| Runtime Authorization Context | `policy_store_version`, decision timestamp | PDP |

**Interpretation.** Read together with Denial Ratio Trend where both exist: a control that
propagated quickly and refused nothing is a different finding from one that propagated slowly and
refused meaningfully (see `./README.md#design-rules`).

**Limitations.** Depends on a "current" definition for policy versions that is not yet settled (ADR-003).
Enforcement points that cache policy add a layer of propagation delay this metric would need to
distinguish from authoring delay.

**Linked controls and workflows.** Policy Management; the propagation half of charter question 7
(`./README.md#charter-coverage`).

## Credential Revocation Response Time

**Purpose.** When a credential is revoked, how long until every capability it could exercise stops
being usable — not just how long until the identity provider reports the revocation as accepted?

**Formula.** Not yet defined. The candidate shape is a change in
`last_denied_or_terminated_decision_time - revocation_event_time`, aggregated per revocation and
compared across windows.

**Scope.** Depends on Event Handling and Response emitting a revocation event
(`../../02-reference/components/event-handling-and-response.md`) and on the named open gap there —
how a revoked credential still associated with an in-flight execution is tracked — being resolved
first (see ADR-004's open consequences). This metric cannot be drafted correctly ahead of that gap
closing.

**Data sources**

| Source | Fields used | Collection component |
|---|---|---|
| Event Handling and Response | revocation event, timestamp | Event Handling and Response |
| Kernel Observability | execution termination timestamp | Kernel Observability |

**Interpretation.** A partial answer to charter question 8 (how quickly the organization detects and
responds to capability risk) — see `./README.md#charter-coverage`. Detection time itself (the
first half of that question) is not covered by this or any current metric.

**Limitations.** Cannot be computed until the credential-revocation-during-execution gap
(`../evidence/README.md`, ADR-004) has a settled model for what "stopped being usable" means for an
execution already in flight.

**Linked controls and workflows.** Event Handling and Response; charter question 8.

## Capability Drift Rate

**Purpose.** Are capabilities in the catalog diverging from what is actually deployed and exercised
at runtime, and is that divergence growing or shrinking?

**Formula.** Not yet defined. The candidate shape is a change in the count of `govops drift`
findings (see `../../02-reference/components/capability-catalog.md#tooling`) — Type A
(catalog references a capability no policy or runtime decision ever exercises), Type B (a runtime
decision carries a `capability_id` absent from the catalog), Type C (a capability's declared
metadata, e.g. risk-tier, no longer matches its observed usage pattern) — compared across two
catalog-sync windows.

**Scope.** Requires linking a runtime decision back to the capability declaration that should have
produced it, which in turn needs `trace_execution_id` (see
`../../02-reference/information-model/README.md#core-identifiers`) — a named gap, not yet defined.

**Data sources**

| Source | Fields used | Collection component |
|---|---|---|
| Capability Catalog | catalog entries, `capability_id` | `govops lint` / `govops drift` |
| Runtime Authorization Context | `capability_id` as observed | PDP |
| Kernel Observability | execution-level capability usage | Kernel Observability |

**Interpretation.** Answers charter question 2 (which capabilities are expanding fastest) by
construction, since it is already a change metric — see `./README.md#charter-coverage`.

**Limitations.** Type B and Type C findings both depend on kernel observability tooling being able
to attribute an execution to a `capability_id`, which is the same gap named in
`../../02-reference/components/kernel-observability.md#known-gap-no-kernel-tool-emits-capability_id-natively`.

**Linked controls and workflows.** Capability Catalog; charter question 2.

## Trace Completeness

**Purpose.** Of the capability exercises that should have produced a full evidence chain
(authorization evidence + execution evidence), what share actually did?

**Formula.** Not yet defined. The candidate shape is a change in the ratio of decisions with a
matching execution-evidence record to total decisions, per capability, across two windows.

**Scope.** This metric is, in effect, a direct measurement of the kernel-observability
`capability_id`-emission gap (`../../02-reference/components/kernel-observability.md#known-gap-no-kernel-tool-emits-capability_id-natively`)
and of instrumentation coverage generally (`./README.md#qualifiers-that-travel-with-every-result`).
It cannot be drafted until execution evidence reliably carries a correlating identifier back to the
decision that authorized it — the same `trace_execution_id` gap named above.

**Data sources**

| Source | Fields used | Collection component |
|---|---|---|
| Runtime Authorization Context | `decision_id`, `capability_id` | PDP |
| Kernel Observability | execution evidence, correlating identifier (gap) | Kernel Observability |

**Interpretation.** Answers the "missing" half of charter question 7 (which controls are missing) —
see `./README.md#charter-coverage`.

**Limitations.** Circular with instrumentation coverage: a capability with low coverage will show
low trace completeness for the same reason it shows unreliable ratios elsewhere in this set — the
population able to report is not a random sample (see `./README.md#what-this-framework-cannot-see`).

**Linked controls and workflows.** Kernel Observability; charter question 7 (missing controls).

## Control Evidence Freshness

**Purpose.** How stale is the compliance evidence backing each mapped control, and is that staleness
improving as more of the estate becomes instrumented?

**Formula.** Not yet defined. The candidate shape is a change in the age of the most recent evidence
artifact per OSCAL control implementation (see
`../compliance-path/gemara-to-oscal.md#the-three-layer-compliance-architecture`), compared across two
review windows.

**Scope.** Depends on the OSCAL control mapping existing at all — currently `Planned, not yet built`
per `../compliance-path/framework-mappings.md`. This metric targets the point-in-time-compliance
problem directly: a control that was evidenced correctly six months ago and has not been checked
since is the failure mode continuous compliance is meant to replace.

**Data sources**

| Source | Fields used | Collection component |
|---|---|---|
| Compliance interoperability layer | control implementation, evidence timestamp | OSCAL / Trestle |
| Evidence | authorization evidence, execution evidence timestamps | see `../evidence/README.md` |

**Interpretation.** Answers the "stale" half of charter question 7 — see
`./README.md#charter-coverage`.

**Limitations.** Not computable before the OSCAL control mapping exists, and retention windows for
the underlying evidence are themselves an open gap (`../evidence/README.md#open-gaps`).

**Linked controls and workflows.** Compliance path; charter question 7 (stale controls).

## Summary table

| Metric | Blocked on | Charter question(s) |
|---|---|---|
| Policy Decision Latency | "Current" policy version definition (ADR-003 open question) | 7 (unenforced) |
| Credential Revocation Response Time | Credential-revocation-during-execution model (ADR-004 open consequence) | 8 (response speed) |
| Capability Drift Rate | `trace_execution_id` (information-model gap) | 2 (fastest-expanding capabilities) |
| Trace Completeness | `trace_execution_id`; kernel `capability_id` emission gap | 7 (missing controls) |
| Control Evidence Freshness | OSCAL control mapping (currently planned, not built) | 7 (stale controls) |
