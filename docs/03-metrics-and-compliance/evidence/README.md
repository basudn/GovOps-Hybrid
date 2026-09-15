---
title: "Evidence"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, evidence]
related: [../../06-decisions/ADR-004-evidence-and-observability-model.md, ../../02-reference/components/runtime-authorization-context.md, ../../02-reference/components/kernel-observability.md]
---

# Evidence

GovOps formally separates two kinds of evidence, per
`../../06-decisions/ADR-004-evidence-and-observability-model.md`:

| Evidence type | Source | Answers | Truth semantics |
|---|---|---|---|
| **Authorization evidence** | Runtime Authorization Context decision record | Why was this allowed/denied/challenged, at decision time? | Fixed at decision time — a decision was correct given what was known then, even if execution later violates it. |
| **Execution evidence** | Kernel Observability | What actually happened? | Continues to accrue after the decision, independent of the application's own self-reporting. |

Neither is a "copy" of the other. They are recorded separately because they answer different
questions and can diverge — most visibly when a credential is revoked or a policy changes after a
decision was made but while the authorized execution is still in flight (see
`../../01-explanation/illustrative-use-cases/governance-scenarios.md#credential-revocation-during-execution`).
What happens to an authorization-evidence record when the underlying policy or credential later
changes — whether it is invalidated, annotated, or left as a historical fact with a separate
revocation event pointing at it — is not yet decided; see ADR-004's open consequences.

## Open gaps

- **Auditability requirements.** What an auditor needs to be able to query and reconstruct from
  the evidence chain (see `../traceability/traceability-matrix.md#evidence-chain-model`) without
  direct system access is not yet specified.
- **Retention.** Retention windows per evidence type are not yet defined. They will likely be
  driven by whichever compliance framework (CRA, SOC 2, ISO 27001) is in scope for a given
  deployment — see `../compliance-path/framework-mappings.md`.

These are named gaps, not settled design choices, consistent with how the rest of this
documentation flags unresolved items rather than inventing resolutions.
