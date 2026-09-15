---
title: "Project and Roadmap"
status: draft
document_type: roadmap
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, roadmap]
related: [../06-decisions/README.md, ../04-how-to-and-process/README.md, ../05-tutorials/README.md]
---

# Project and Roadmap

This page holds project-management information for the GovOps working group: where the
documentation set stands (maturity model), what remains genuinely unresolved (open questions and
terminology decisions), what is planned but not built (reference scenarios), and the release
timeline the group has committed to.

## Maturity model

The documentation set is not yet at a single maturity level — different sections are further along
than others:

| Phase | Covers | Status |
|---|---|---|
| 1. Explanation and positioning | `01-explanation/` | Mostly drafted; not yet reviewed by the working group as a whole |
| 2. Reference and component boundaries | `02-reference/` | Partially drafted; several open gaps remain (see ADR-002, ADR-003) |
| 3. ACC normative spec | Capability Catalog schema and `capability_id` convention | In progress; planned to reach normative status before the rest of the architecture (see ADR-006) |
| 4. Broader architecture conformance | A conformance statement for the architecture as a whole | Deferred, per ADR-006 |

## Open questions

Genuinely unresolved as of this writing:

1. **Who is the primary audience for outreach?** RSA-attending CISOs, CSA/CRA-facing regulatory
   bodies, authorization vendors, or OpenSSF specifically — raised in working-group discussion, not
   decided. Current lean is toward GRC/compliance leads and OpenSSF as sharper targets than a
   generic CISO audience, but this is not settled.
2. **Audience scope tension.** The project's purpose statement (see
   `../00-foundations/purpose.md`) names "software architects, CISOs, and other tech leaders" —
   narrower than the six-function cross-functional audience (audit, compliance, security,
   engineering, finance, legal) described in
   `../01-explanation/govops-thesis/README.md`. Whether the architecture document itself should
   address the full cross-functional set, or whether that belongs in separate outreach material, is
   unresolved.
3. **Document organization.** This Diátaxis-inspired but consolidated structure is itself a
   proposal, not yet formally ratified by the working group as the final shape (see ADR-008).
4. **Which reference scenario to build first** — `agentgateway`/Cedarling/OpenSearch, or the GitHub
   Actions self-governance dogfooding scenario (see
   `../04-how-to-and-process/README.md#planned-reference-scenarios`). Both are recommended; the
   sequencing between them is not decided.
5. **The five-vs-nine "core services" inconsistency** — the architecture names five core services in
   some places and nine in the full reference model. Not resolved; see ADR-002.
6. **`trace_execution_id` and other undefined information-model fields** — see ADR-003 and
   `../02-reference/information-model/README.md#core-identifiers`. Not resolved.

## Terminology decisions needed

- **"Governor"** — used in the project's purpose statement (`../00-foundations/purpose.md`), not yet
  formally defined in `../02-reference/terms-and-definitions/glossary.md` beyond an informal
  gloss.
- **"Governance decision" vs. "governance evidence" vs. "runtime evidence"** — used loosely and
  inconsistently across earlier drafts; needs tightening against the authorization-evidence /
  execution-evidence split established in ADR-004 and `../03-metrics-and-compliance/evidence/README.md`.
- **Cross-standard vocabulary gap.** Beyond Zero, Meta Muse, AuthZen, and SSF each name similar
  concepts differently (see `../01-explanation/positioning/README.md`); no shared glossary
  exists across these efforts yet.

## Planned reference scenarios

See `../04-how-to-and-process/README.md#planned-reference-scenarios` for the two candidate
end-to-end scenarios (`agentgateway`/Cedarling/OpenSearch, and GitHub Actions self-governance) and
why both are recommended.

## Release and versioning policy

The working group has committed to the following release timeline (per architecture coordination
call, Aug 27 2026):

| Milestone | Target date | Notes |
|---|---|---|
| v0.0.1 | Released Aug 31 2026 | Established the formal release process for architecture documents and metrics. |
| Release cadence | Monthly, following v0.0.1 | Adopted to keep the documentation set moving in fixed increments rather than as one large periodic revision. |
| v0.0.2 | End of Sept 2026 | Next monthly increment. |
| v1.0 | Apr 15 2027 | Timed to support conference presentations (e.g. EIC, Identiverse). |

No formal versioning scheme for the documentation set's internal structure (as opposed to release
dates) has been proposed yet. The meeting notes state the v1.0 target as "mid-April" without
specifying a year; Apr 2027 is the only date consistent with a monthly cadence starting from the
Aug 31 2026 v0.0.1 release — this should be confirmed with the working group rather than treated as
settled.

**Editor requirement** (per architecture coordination call, Aug 27 2026): project editors are
required to hold an active OWASP membership, to support the project's organizational goals as an
OWASP project. Editors otherwise retain autonomy over their own preferred communication and
collaboration methods.
