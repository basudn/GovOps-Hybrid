---
title: "ADR-004: Evidence and Observability Model"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, evidence]
related: [../02-reference/components/kernel-observability.md]
---

# ADR-004: Evidence and Observability Model

## Status
Proposed

## Context
The Observe step joins three streams on `capability_id`: authorization context (correlation IDs only), application telemetry (self-reported, can be silent or wrong), and kernel observability (OS-level syscall proof, independent of app self-reporting — "authorization proves permission, not execution"). The published draft does not name "authorization evidence" and "execution evidence" as separate concepts, using looser terms ("governance evidence," "runtime evidence") instead, and doesn't define the mechanics of the one edge case it does name: "a revoked credential still associated with active execution."

## Decision
Formally separate authorization evidence (why a decision was made) from execution evidence (what actually happened), as two distinct record types with different timing and truth semantics — especially important after credential revocation or a policy change, where the authorization evidence is fixed at decision time but execution evidence continues to accrue.

## Consequences
Requires defining what happens to an authorization-evidence record when the underlying policy or credential later changes — does it get invalidated, annotated, or left as a historical fact with a separate revocation event pointing at it?

## Alternatives considered
- **Merge both into one evidence record**: rejected — conflates two things with genuinely different truth semantics (a decision was correct given what was known at the time; execution can violate that even without the decision being "wrong").

## Links
`../02-reference/components/kernel-observability.md`, `../01-explanation/06-illustrative-use-cases/credential-revocation-during-execution.md`, `../01-explanation/07-positioning/relationship-to-muse.md` (Meta Muse's continuous taint tracking as a related but more advanced pattern)
