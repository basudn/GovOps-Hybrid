---
title: "Denial Ratio Trend"
status: draft
document_type: metric-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, metric]
related: [./README.md, ../../02-reference/components/runtime-authorization-context.md]
---

# Denial Ratio Trend

## Status

Draft, for working-group comment. The first fully worked metric in the set — published first
because it is the cheapest complete entry and exercises every field of the metric-definition
template (see `../../_templates/metric-template.md`).

## Purpose

Is the rule governing this capability refusing more or less than it was, and did anything the
organization changed move it?

## Governance activity

Observability, supporting risk management.

## Definition

The **change** in the share of authorization decisions on a capability that resulted in a deny,
measured across consecutive windows. The share itself is reported as a qualifier, never as the
headline (see `./README.md#what-counts-as-a-govops-metric`).

A capability with meaningful decision volume and no denials at all is not necessarily a
well-protected capability — it may be one where the governing rule does not discriminate, and
would allow any request that reached it.

The architecture defines a three-valued decision (allow, deny, challenge — see
`../../02-reference/components/policy-decision-point.md#decision-outcomes-allow-deny-and-challenge`),
but most engines in the field do not emit the third value. Deny-by-default engines never return a
challenge: a step-up flow records as a deny, possibly followed by a fresh allow when the caller
returns with evidence. **v1 treatment: count outcomes as recorded — a deny is a deny.**
Challenge-aware counting is parked until challenge is commonly emitted; the engine-model
difference is a stated limitation below.

## Calculation

```text
denial_ratio(capability, window) =
    count(decisions where outcome = deny) / count(all decisions)

denial_ratio_trend(capability, w1, w2) =
    denial_ratio(capability, w2) - denial_ratio(capability, w1)
```

Windows are consecutive fixed calendar periods of equal length. Both counts are restricted to one
capability and to decisions carrying a `capability_id`. The headline is the change between the two
windows; each window's ratio and decision count travel with it as qualifiers.

A ratio computed from a finite decision count moves with sampling variation even when nothing has
changed. The reported change carries a check: a two-proportion comparison of the two windows at
95% confidence. Where the observed change is within the range sampling variation alone would
produce, it is published as the change together with "no evidence of movement." The check treats
decisions as independent; where one request produces several evaluations (see the denominator
limitation below), the effective sample is smaller than the decision count and the check
overstates confidence.

Reported alongside each window's ratio:

- The absolute decision count — a ratio over a handful of decisions is not meaningful.
- The number of policy versions in force during the window — a ratio spanning a rule change
  averages the behavior of two different rules.
- The challenge count, where the engine emits challenges.

Where the decision count falls below a stated floor (suggested: 100 decisions in the window),
report the count and suppress the ratio.

Where the denial count is zero in a window with meaningful volume, report the upper bound on the
true denial rate using the rule of three: at 95% confidence the true rate is at most `3/n`. For a
capability with 13,400 decisions and zero denials, the upper bound is 0.00022 — this belongs in
the reported output alongside the zero, so a reader can distinguish a genuinely clean record from
insufficient data.

## Data required

| Field | Source |
|---|---|
| `capability_id` | Runtime Authorization Context decision record |
| decision outcome | Runtime Authorization Context decision record |
| timestamp | Runtime record |
| `policy_store_version` | Runtime Authorization Context decision record |
| `risk-tier` | Capability Catalog |

The decision-record fields are within the required minimum of the Runtime Authorization Context
(see `../../02-reference/components/runtime-authorization-context.md#minimum-fields`).

## Segmentation

`risk-tier` is required in all reporting — a low ratio on a low-risk capability is unremarkable;
the same figure on a critical capability is the finding. Also useful: requester class, `org-unit`,
third-party exposure, policy version.

## Worked example

Meridian Finance, a fictional mid-size lender. Window: March. Capability `approve:payment-over-threshold`
(display form; the record carries the catalog's SHA-256 `capability_id`), risk tier: critical.

| | |
|---|---|
| Decisions | 13,400 |
| Denials | 0 |
| Denial ratio | 0.000 |
| Rule-of-three upper bound (95%) | 0.00022 |
| Policy versions in force | 2 |
| Instrumentation coverage | 42% |

The governing rule allowed any request originating inside the corporate network, and every
request in the window did.

**The version split changes the reading.** On 14 March the rule was tightened to require a fresh
identity check. Propagation was staggered — both versions were deciding until 2 April, and one
legacy payments service stayed on the old version throughout.

| Period | Rule version | Decisions | Denials | Ratio |
|---|---|---|---|---|
| 1–14 March | v4 only | 6,100 | 0 | 0.000 |
| 14–31 March, under v5 | v5 | 5,900 | 0 | 0.000 |
| 14–31 March, still under v4 | v4 | 1,400 | 0 | 0.000 |

The control was strengthened and the ratio did not move, because every requester already satisfied
the new condition. Without the version split, this would read as one flat number and the
strengthening would be invisible; with it, a deliberate control improvement produced no observable
change in behavior — itself the finding.

For contrast, `deploy:production-service` in the same window:

| | |
|---|---|
| Decisions | 4,200 |
| Denials | 126 |
| Denial ratio | 0.030 |

A rule that is at least separating some requests from others.

**Movement across windows** (April, the following window):

| Capability | March | April | Change | Movement check (95%) |
|---|---|---|---|---|
| `approve:payment-over-threshold` | 0.000 (13,400 decisions) | 0.000 (13,100 decisions) | 0.000 | No evidence of movement |
| `deploy:production-service` | 0.030 (4,200 decisions) | 0.022 (4,450 decisions) | -0.008 | Movement larger than chance |

For the payment capability, a second clean window tightens the reading: the rule has now been
observed over 26,500 decisions without refusing one, and the rule-of-three upper bound falls to
0.00011. For the deployment capability, no policy version changed in April — the decline traces to
a batch migration completed at end of March that had been generating a steady stream of denied
requests. The movement is real, and it is a change in the request population, not the rule.

**Interpretation a governor would draw.** The payment approval control is present, versioned,
referenced by the capability, and in force for the whole period — nothing a conventional audit
would flag as misconfigured. It has simply never distinguished one request from another, before or
after tightening. This is a finding about a rule, not about any person or team.

## Interpretation guidance

- **At or near zero**, on a critical capability with real volume: the signal this metric exists to
  surface. The rule is not discriminating.
- **Moderate**: the rule is separating requests. It says nothing about whether it is separating
  them correctly.
- **High**: not automatically good — may indicate a rule too restrictive for the work people are
  trying to do, which tends to produce workarounds.
- **A change in the ratio** is more informative than its level. A ratio dropping to zero after a
  rule change usually means the change was more permissive than intended.
- **A rising denial ratio on a deny-by-default estate with step-up flows** may be friction moving
  rather than protection moving, because step-ups record as denies there.

## What it does not support

Not a conclusion that a capability is secure, that access to it is appropriate, or that no misuse
has occurred — a correctly formed request using stolen credentials produces an allow and
contributes to a low ratio exactly as a legitimate request would. Not any statement about who
holds a capability or how they came to hold it. Not a conclusion that a rule is well written — it
reports only that the rule produced different outcomes for different requests.

## Limitations

A low ratio has two possible causes this metric cannot separate: a permissive rule, or a
well-behaved population that never submits a request that should be refused. The metric identifies
which rules to read; it does not say what is wrong with them.

The ratio is undefined for capabilities never exercised and unstable for those exercised rarely.

**The denominator counts evaluations, not requests.** A single logical request can traverse
multiple enforcement points and produce multiple decisions on the same capability. A capability
behind a service mesh with five enforcement points has a 5x multiplied count relative to one
behind a single gateway. The ratio is not directly comparable across capabilities with different
enforcement-point depths. Report the evaluation-to-request multiplier as a qualifier where known.

**The denominator problem carries into the trend.** If enforcement topology changes between the
windows being compared, a new enforcement point in the request path multiplies the evaluation
count and moves the ratio with no change in decision behavior. Treat a topology change as a break
in the series.

**The ratio is not comparable across engine decision models.** A deny-by-default engine records a
step-up as a deny followed by a later allow; a challenge-emitting engine records the same journey
as challenge then allow. Estates on deny-by-default engines read a higher ratio for identical
behavior — report the engine decision model as a qualifier where estates are mixed.

Requests refused upstream, before reaching the decision point, do not appear in the denominator.

## Instrumentation level required

Computable from any decision log carrying a `capability_id` and a decision outcome. The version
split additionally requires `policy_store_version` on each decision, which the Runtime
Authorization Context specifies as a required field.

## Gaming

Easy to move without improving anything: adding a clause that refuses obviously malformed requests
lifts a capability off zero while leaving the permissive path untouched. The metric is a
diagnostic, not a target. When a capability moves off zero, check what is now being refused. Fixed
calendar windows also remove the option of choosing window boundaries that flatter the trend.

## Qualifiers that travel with the result

Instrumentation coverage, observation window as fixed dates, catalog version, each window's ratio
and total decision count, number of policy versions in force per window, enforcement point count
per window, challenge count where the engine emits it, engine decision model where estates are
mixed.

## Open questions

- **Challenge-aware counting is parked** until challenge is commonly emitted (see
  `./README.md#open-questions-on-this-front-matter`).
- **Whether token-issuance-time decisions belong in the denominator** — proposed v1 default:
  exercise-time decisions only.
- **The movement check is a proposal.** A two-proportion comparison at 95% confidence is the
  lightest check that separates movement from sampling noise on two windows; control-chart
  treatments over longer series could replace it where longer histories exist.
