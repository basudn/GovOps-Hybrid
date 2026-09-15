---
title: "Security"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, security]
related: [../components/runtime-authorization-context.md, ../components/kernel-observability.md, ../components/federation-management.md, ../../06-decisions/ADR-005-federation-boundary-model.md]
---

# Security

This section consolidates the security and trust rationale that is otherwise scattered across
individual component pages.

## Minimal-correlation-record rationale

The Runtime Authorization Context deliberately carries only five fields (`capability_id`,
`decision`, `decision_id`, `policy_store_id`, `policy_store_version`) and never the token, policy
text, or full request payload. This is not an oversight — it is what makes the record safe to fan
out to multiple downstream consumers (Kernel Observability, Event Handling and Response,
Continuous Compliance, Governance Metrics), some of which may sit at different trust levels,
without each consumer becoming a new place sensitive data could leak. See
`../components/runtime-authorization-context.md`.

## Kernel observability is harder to blind than application logs

Kernel-level or equivalent OS-level event emission is chosen over relying solely on
application-level logging because it is independent of the application's own (possibly
compromised or buggy) self-reporting. An attacker who can suppress or falsify application logs
cannot as easily suppress OS-level syscall or network evidence. This is the basis for the "kernel
observability proves execution, not just what the application claims happened" framing in
`../components/kernel-observability.md`. It is also why detection-evasion concerns are directly
relevant to Event Handling and Response: if the kernel-observability half of a join can be
delayed or suppressed, drift detection degrades.

## Policy-store integrity as a supply-chain problem

Policy content that reaches production determines every subsequent runtime decision. GovOps
treats policy-store integrity the way software supply chains treat build artifacts: required
review before publication, versioned and attributable changes (`policy_store_id` /
`policy_store_version`), and — per the IETF draft-cabanillas-nmop-authz-policy-sharing-model
precedent — signed provenance envelopes around policy content. See `../components/policy-management.md`.

## Data classification (gap)

GovOps does not currently define a data-classification scheme of its own for the metadata
attached to capabilities (`data-sensitivity` is a per-capability field in the catalog schema, but
there is no organization-wide classification policy this specification defines or requires). This
is a gap, not a resolved design choice.

## Identity and credential boundary pattern

GovOps deliberately keeps identity/credential validation out of the governance plane: Federation
Management decides *which issuers to trust*, while actual token validation happens at runtime
(see `../../06-decisions/ADR-005-federation-boundary-model.md`). This mirrors Meta Muse's
`authd` service plus surrogate-token pattern, where a dedicated trust-decision component is kept
separate from the runtime path that validates individual credentials — see
`../../01-explanation/positioning/README.md#relationship-to-metas-muse`.

## Federation trust boundaries

Federation Management's trust decisions are treated as third-party risk management: an issuer is
onboarded (or removed) as a bounded, auditable governance exercise, not a runtime configuration
change. See `../components/federation-management.md`.

## Related decisions

- `../../06-decisions/ADR-004-evidence-and-observability-model.md`
- `../../06-decisions/ADR-005-federation-boundary-model.md`
