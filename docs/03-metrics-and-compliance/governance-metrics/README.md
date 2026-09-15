---
title: "Governance Metrics"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, metrics]
related: [./denial-ratio-trend.md, ./planned-metrics.md, ../../02-reference/components/capability-catalog.md]
---

# Governance Metrics

This defines the measures GovOps uses to show governors where authorization risk sits and which
way it is moving. It instruments the Observe → Detect segment of the GovOps loop (see
`../../02-reference/architecture/README.md#4-the-govops-loop`). It is not a dashboard
specification, a tool, or a maturity model, and it does not tell an organization what its numbers
should be — it defines each number, the conclusions the number cannot support, and the context
that must be published with it.

All worked examples in this section use the same fictional company, Meridian Finance, a mid-size
lender, over the same period, so entries can reference each other's findings.

## The set at a glance

| Entry | Kind | Status |
|---|---|---|
| [Denial Ratio Trend](./denial-ratio-trend.md) | Operational metric | Draft — complete, in this directory |
| [Planned metrics](./planned-metrics.md) (5 entries) | Operational metrics | Proposed / parked — not yet fully specified |

## What counts as a GovOps metric

> **A GovOps metric requires at least two observation windows and reports the change between
> them. Levels are admitted as denominators, qualifiers, and segmentation — never as headline
> metrics.**

Levels are already well served elsewhere: coverage percentages, pass rates, threshold scores, and
the share of capabilities with an owner are worth publishing, and most tools already produce them.
They do not show whether a position is improving or deteriorating, or whether a given change made
any difference — that is the gap this metric set exists to fill.

Consequences:

- A candidate that cannot be stated as a change does not enter the set. It may still belong here
  as a base count or a segmentation, labelled as such.
- Every entry reports at least two windows. A figure from a single window is a point-in-time
  reading and does not qualify.
- Where a level is genuinely useful, it is published as a qualifier attached to a metric, not as a
  metric of its own.

The set deliberately contains no standalone risk metric: the Capability Catalog's risk-tier ×
business-impact scoring (see `../../02-reference/components/capability-catalog.md`) already ranks
capabilities, and a ranking is a level. Every metric here is instead segmented by `risk-tier` as a
requirement (below) — risk tier determines which capabilities a governor reads first; the metrics
report what is changing on those capabilities.

## Design rules

- **Method stays in the calculation field.** A metric is named for what it measures, not for how
  it is computed; any statistical technique appears in the calculation section only. A governor
  should be able to read every metric's name and interpretation without knowing the technique
  behind them.
- **Limitations and "what it does not support" are separate fields.** A limitation is a weakness
  in the number itself. "What it does not support" lists conclusions the number cannot justify but
  that readers tend to draw anyway. Merging the two tends to lose the second, which is the one
  that causes damage in practice.
- **No composite scores.** Every measure here is decomposable. A weighted average of several
  measures hides its weighting, and when it moves, the reader cannot tell why.
- **Read propagation-time-style and denial-ratio metrics together, where both exist.** A control
  that propagated quickly and refused nothing is a different finding from one that propagated
  slowly and refused meaningfully — neither metric alone produces that finding.
- **Small scope, stated limits.** A small set of well-specified measures with stated limits is
  more useful than a larger set that implies more than it can deliver.

## Required segmentation

**Risk tier is required on every metric** (`risk-tier`, from the Authorization Capability
Profile). Interpretation changes sharply with it — a figure published without it invites the
wrong conclusion. Segmentation names follow the profile: `risk-tier` and `data-sensitivity` are
enums; `business-impact`, `geography`, and `org-unit` are organization-defined strings.

Requester class and third-party exposure are named as important segmentations but are not yet
typed fields in the published capability schema — see `../../02-reference/information-model/README.md`
for the current state of what is and isn't defined.

## Qualifiers that travel with every result

A metric published without these is not readable and should not be published:

- Observation window, as fixed dates.
- Catalog version.
- Instrumentation coverage for the capability, and its direction.
- Total decision count for the window.

When coverage moved materially between the windows being compared, part of any reported change
reflects the population that entered or left observation rather than a change in behavior. Read
the change within segments whose coverage held steady, or publish it flagged as not comparable
across the windows.

## Status labels

| Label | Meaning |
|---|---|
| Draft | Written up, open for working-group comment |
| Proposed | Recommended for v1 |
| Accepted | Ratified, in v1 |
| Parked | Held for a later version, with the reason recorded |
| Base count | Not a metric — a denominator the metrics rely on |
| Segmentation | Not a metric — a way of cutting the others |

## Charter coverage

The working group charter lists eight operational questions the metrics must answer. Not all of
them belong in the operational metric set:

| # | Charter question | Answered by | Why it sits there |
|---|---|---|---|
| 1 | Which capabilities create the most risk? | Risk-tier × business-impact scoring in the Capability Catalog | A ranking is a level, not a change — excluded from the operational set by the admissions test. |
| 2 | Which capabilities are expanding fastest? | Planned metric: Rate Shift | A change across windows by construction. |
| 3 | Which teams own the most critical capabilities? | Catalog query: accountable owner | Static metadata, answerable from the catalog alone. |
| 4 | Which capabilities lack clear accountability? | Catalog query: accountable owner, checked for completeness | Same field, checked for gaps rather than value. |
| 5 | Which high-risk capabilities are exposed to third parties or autonomous actors? | Catalog attribute + segmentation by requester class | Catalog answers the static question; segmentation adds the movement dimension. |
| 6 | Which policies control each capability? | Catalog query: capability-annotated policy store | Static relationship, no runtime measurement needed. |
| 7 | Which controls are missing, stale, or unenforced? | Planned metrics: Trace Completeness (missing), Control Evidence Freshness (stale), Policy Decision Latency-adjacent propagation metric (unenforced) | Three failure modes, three metrics. |
| 8 | How quickly does the organization detect and respond to capability risk? | Planned metric: Credential Revocation Response Time (partial); pure detection time is not directly measured | Detection speed is only partly covered — a named gap. |

Three questions (3, 4, 6) are catalog queries; one (1) is answered by risk-tier scoring; one (5)
straddles catalog and segmentation; three (2, 7, 8) are answered by operational metrics, with a
partial gap on detection time. Of the operational metrics named, only Denial Ratio Trend is fully
published — the rest are planned (see `./planned-metrics.md`).

## What this framework cannot see

These apply to every metric in this set and are not repeated per entry:

- **Access that is never used.** The record contains decisions that happened. A dormant capability
  held by a departed contractor reads clean on every measure here.
- **Anything not in the catalog.** The metrics only see capabilities the catalog contains, so
  coverage of an incomplete catalog can still read 100%.
- **Who holds a capability.** GovOps does not maintain principal-to-capability holdings. How
  access is granted, approved, reviewed, and certified stays in identity governance.
- **What was at stake.** Every decision counts once, whatever the value of the transaction behind
  it.
- **A correctly formed attack.** Stolen credentials used properly produce a clean allow and an
  unremarkable record.
- **Separation of duties.** Whether one person performed two conflicting roles is a question about
  principals, which GovOps does not maintain.
- **Which capabilities carry the most risk, as a ranking.** Answered by risk-tier × business-impact
  scoring, not the operational set.
- **Compliance pipeline health.** Evidence freshness, OSCAL coverage, and control-mapping
  completeness draw on a different data source — see `../compliance-path/README.md`.
- **Whether accountability is being exercised.** The catalog tracks who owns each capability; the
  metrics track governance findings, not whether findings receive a response.
- **The systems that cannot report.** Instrumentation coverage is not a random sample — the estate
  able to emit capability-tagged decisions skews modern and well-run, and the older estate is
  usually where the problems are. Every result carries the coverage figure for this reason.

## How to propose a metric

- One issue per candidate.
- The admissions test above is the first question asked of any candidate.
- A candidate without a worked example and a limitations section is not ready for discussion.

## Open questions on this front matter

- **Challenge outcomes.** The architecture defines a three-valued decision (allow, deny,
  challenge — see `../../02-reference/components/policy-decision-point.md#decision-outcomes-allow-deny-and-challenge`),
  but deny-by-default engines never emit the third value; a step-up records as deny then allow. v1
  counts outcomes as recorded; challenge-aware counting is parked until challenge is commonly
  emitted.
- **Whether decisions produced at token issuance**, as opposed to at the point of capability
  exercise, belong in the denominator of decision-count metrics. Proposed v1 default: the
  denominator counts exercise-time decisions only; issuance-time decisions are reported as a
  separate count where instrumented.
- **What makes a policy version "current"**: commit, release, or the point the distribution
  system reports it published. Affects every propagation-style measure.
