---
title: "Federation Management"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, federation]
related: [../../06-decisions/ADR-005-federation-boundary-model.md, ../../01-explanation/positioning/README.md]
---

# Federation Management

## Purpose

Federation Management owns the organizational decision of which external issuers and token
sources are trusted, and for which token types. It is a third-party risk management (TPRM) and
onboarding concern, not a runtime one: it decides *whether* an issuer is trusted at all, well
before any individual token from that issuer is evaluated at request time.

## Responsibilities

- Maintain the registry of trusted issuers per token type (an organization may reasonably trust
  different issuers for, e.g., workforce identity tokens vs. workload identity tokens vs.
  partner-API tokens — the landscape includes roughly two dozen emerging token types across these
  categories).
- Define the onboarding and offboarding process for an issuer: what evidence is required before
  an issuer is trusted, and what triggers removal of trust.
- Define required claims, rotation cadence, and revocation expectations for each trusted issuer,
  as a condition of onboarding.
- Periodically review the trusted-issuer list as a bounded, auditable exercise (see
  `../../01-explanation/illustrative-use-cases/governance-scenarios.md#third-party-and-federated-trust-review`).

## Non-responsibilities

- **Does not perform runtime token validation.** Verifying a specific token's signature,
  expiration, or claims against the trusted-issuer list at request time is a Runtime Plane
  concern, carried out by the Policy Decision Point or its supporting infrastructure — not by
  Federation Management itself.
- Does not compute or influence a specific authorization decision (allow/deny) for a request.
- Does not define the capability being requested — that is the Capability Catalog.

## Why this is a governance-plane concern

Federation Management is grouped with the Capability Catalog and Policy Management under the
Governance Plane not because it is capability-centric in the same sense as the catalog, but
because issuer trust needs the same formal lifecycle treatment — versioning, review, audit — as
policy and catalog changes. See `../../06-decisions/ADR-005-federation-boundary-model.md` for the
full reasoning and the open question of whether this four-service, non-capability-centric
grouping needs a cleaner architectural boundary. Token claims are nonetheless critical *input
data* to capability-based runtime decisions, which is why the trust boundary matters even in an
action/resource-first model.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| Issuer onboarding request | Engineering/security team | Propose a new trusted issuer | Yes |
| Issuer evidence (rotation policy, revocation mechanism, claim set) | Prospective issuer | Basis for onboarding decision | Yes |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| Trusted-issuer list | Policy Decision Point / Runtime Plane infrastructure | Basis for runtime token validation |
| Required-claims specification per issuer | Schema Management, Policy Management | Ensures policy can rely on claim presence |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| Trusted-issuer registry | Federation Management | Governance working group | Reviewed periodically, not just at onboarding. |

## Dependencies

- Identity providers / token issuers (external, per `../architecture/system-context.md`).

## Interfaces

- Trusted-issuer list consumed by runtime token-validation infrastructure.
- Onboarding/review process (currently undefined as a formal interface — see
  `../interfaces/README.md`).

## Security and trust considerations

Treating issuer trust as an onboarding/TPRM decision, separate from runtime validation, mirrors
Meta Muse's `authd` service and surrogate-token pattern: a dedicated component owns "who do we
trust" so that runtime paths only ever need to answer "is this specific token valid right now"
against an already-vetted list. See `../../01-explanation/positioning/README.md#relationship-to-metas-muse`
for the full comparison.

## Observability and evidence

Issuer onboarding/offboarding events and periodic trust-review outcomes should be evidenced
alongside other governance decisions; a formal evidence contract for this is not yet defined (see
`../security/README.md`).

## Related decisions

- `../../06-decisions/ADR-005-federation-boundary-model.md`
