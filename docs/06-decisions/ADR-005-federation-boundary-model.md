---
title: "ADR-005: Federation Boundary Model"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, federation]
related: [../02-reference/components/federation-management.md]
---

# ADR-005: Federation Boundary Model

## Status
Proposed

## Context
Federation Management and Policy Management both sit in the GovOps (governance) service group, but neither is capability-centric in the same sense as the Capability Catalog. Open question raised by Vatsal Gupta (2026-08-19): is the four-petal/nine-service boundary a clean architectural split, or a "needs formal governance treatment" grouping?

## Decision
Federation Management owns organizational trust decisions (which issuers to trust for which token types) — a TPRM/onboarding concern. It does not perform runtime token validation, which is a Runtime Plane concern. Policy Management owns policy authoring/versioning/distribution, with security approval required before production, treating policy-store integrity as a supply-chain-style trust problem. Both are grouped with the Capability Catalog not because they are capability-centric themselves, but because they need the same formal governance lifecycle treatment (versioning, review, audit), and because token claims ("JWT tokens = truth") are critical input data to capability-based decisions even in an action/resource-first model.

## Consequences
This boundary needs an explicit non-responsibility statement in both components' reference pages (done, see links below) — previously missing entirely from the published draft.

## Alternatives considered
- **Treat Federation Management as an Identity-petal concern instead**: rejected per Mike Schwartz's reasoning — TPRM/onboarding is a governance-lifecycle task even though its subject matter (issuer trust) is identity-adjacent.

## Links
`../02-reference/components/federation-management.md`, `../02-reference/components/policy-management.md`, `../01-explanation/07-positioning/relationship-to-muse.md` (Meta Muse's authd/Sentinel split as a working precedent for this exact separation)
