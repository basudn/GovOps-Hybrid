---
title: "Policy Management"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, policy]
related: [../../06-decisions/ADR-005-federation-boundary-model.md, ./policy-decision-point.md, ./capability-catalog.md]
---

# Policy Management

## Purpose

Policy Management authors, versions, and distributes the policy content that Policy Decision
Points evaluate at runtime. It treats policy as a versioned software artifact — subject to
review, testing, and controlled release — rather than as configuration that can change silently.

## Responsibilities

- Author and maintain policy content, referencing capabilities by `capability_id` rather than by
  ad hoc action/resource strings.
- Assign and increment a `policy_store_id` and `policy_store_version` for every published policy
  bundle, so any runtime decision can be traced back to the exact policy content that produced it.
- Require security approval before a policy reaches production — "shared responsibility for
  push-to-prod," not a unilateral engineering or security decision.
- Distribute published policy to the Policy Decision Points that enforce it.
- Maintain provenance for policy changes (who authored, who approved, when published) so
  policy-store integrity can be treated as a supply-chain-style trust problem — analogous to
  signed software artifacts. IETF's draft-cabanillas-nmop-authz-policy-sharing-model describes one
  approach to this, using COSE-signed provenance envelopes around policy content.

## Non-responsibilities

- **Does not decide which token issuers are trusted** — that is Federation Management (see
  `../../06-decisions/ADR-005-federation-boundary-model.md`).
- **Does not compute runtime authorization decisions** — that is the Policy Decision Point. Policy
  Management ships the policy; it does not evaluate it against live requests.
- Does not define the entity/attribute schema that policy is written against — that is Schema
  Management.

## The PAP / PDP / PEP roles

Policy Management plays the role commonly called the Policy Administration Point (PAP) in
policy-based access control literature: authoring and publishing policy for PDPs to evaluate and
PEPs to enforce.

| Role | Owned by | Responsibility |
|---|---|---|
| PAP (Policy Administration Point) | Policy Management | Author, version, approve, publish policy |
| PDP (Policy Decision Point) | Policy Decision Point component | Evaluate a request against published policy |
| PEP (Policy Enforcement Point) | The requesting application itself | Enforce the PDP's decision — see `./policy-decision-point.md#pdp-vs-pep` |

## Policy store identity and versioning

Every published policy bundle carries a `policy_store_id` (identifying which policy store/bundle)
and a `policy_store_version` (identifying which revision of it). Both are recorded in the Runtime
Authorization Context alongside every decision (see `./runtime-authorization-context.md`), which
is what makes a past decision reproducible: given `policy_store_id` + `policy_store_version`, the
exact policy content in force at decision time can be retrieved.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| `capability_id` | Capability Catalog | Anchor policy statements to governed capabilities | Yes |
| Entity/attribute schema | Schema Management | Structure the conditions policy can express | Yes |
| Security approval | Security reviewer | Gate production publication | Yes |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| Published policy bundle (`policy_store_id`, `policy_store_version`) | Policy Decision Point | Runtime evaluation |
| Policy change provenance | Continuous Compliance | Evidence of review/approval process |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| `policy_store_id` / `policy_store_version` | Policy Management | Governance working group | Referenced by every decision record; never mutated in place. |

## Dependencies

- Capability Catalog, for `capability_id` values policy statements reference.
- Schema Management, for the entity/attribute vocabulary policy conditions are expressed against.

## Interfaces

- Policy distribution channel to Policy Decision Points (mechanism not yet standardized — see
  `../interfaces/README.md`).

## Security and trust considerations

Policy content that reaches production determines every subsequent runtime decision, so its
integrity is treated as a supply-chain concern: signed provenance, required review, and
versioning that makes any change attributable and reversible.

## Observability and evidence

Publication events (author, approver, timestamp, resulting `policy_store_version`) are the
evidence trail for "why was this policy in force" reviews and feed Continuous Compliance.

## Related decisions

- `../../06-decisions/ADR-005-federation-boundary-model.md`
