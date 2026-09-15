# Contributing to GovOps

GovOps is an OWASP project developed as a Community Specification. This document explains how to
propose changes to it.

## Before you start

- Read `docs/00-foundations/` for the project's scope and purpose.
- Check `docs/06-decisions/` — if your proposal touches something already decided in an ADR,
  reference it rather than reopening it silently.
- If you are proposing a new metric, start from the admissions test in
  `docs/03-metrics-and-compliance/governance-metrics/README.md#what-counts-as-a-govops-metric`
  before drafting.

## Issue types

| Type | Use it for |
|---|---|
| **Discussion** | Support or functionality questions worth a written record. May turn into a Spec Change issue. |
| **Proposal** | New ideas or functionality requiring broader discussion. Title and label as `Proposal: <title>`. Can evolve into a Spec Change; does not require a milestone on its own. |
| **Spec Change** | Tracks a specific change from proposal to completion. May evolve from a Proposal or Discussion, or be submitted directly for small changes. Each Spec Change should be placed in a milestone. |

## Pull request workflow

1. **Open a PR**, ideally tied to an existing issue. Work-in-progress PRs are welcome — prefix the
   title with `WIP:` and remove the prefix once ready for review.
2. **Triage.** An Editor applies labels: at minimum a size label, a milestone, and `awaiting review`.
3. **Review.**
   - A `Comment` review is for questions that don't require spec changes and does not count as
     approval.
   - A `Changes Requested` review means changes are needed before merge.
   - Reviewers add `LGTM` once satisfied.
   - Final approval requires sign-off from a designated Editor; merging is blocked without it.
4. **Stay responsive** to review comments — answer questions or update the text.
5. **Merge or close.** A PR stays open until a Maintainer marks it approved. The author may close
   it without merging; a Maintainer may close it if it will not be merged.

## Commit sign-off (DCO)

Every commit needs a Developer Certificate of Origin sign-off:

```bash
git commit -s -m "docs: clarify challenge semantics"
```

This appends a `Signed-off-by:` trailer. If you forget, `git commit --amend -s` fixes the last
commit.

## Document conventions

- **Front matter is required.** This differs from the original GovOps repository, which used plain
  Markdown with no front matter. GovOps-Hybrid documents carry YAML front matter
  (`title`, `status`, `document_type`, `source_of_truth`, `normativity`, `owner`, `last_reviewed`,
  `tags`, `related`) per `docs/_templates/README.md` — include it in any new or edited file.
- **Relative links between documents** — e.g. `../02-reference/components/capability-catalog.md`,
  not an absolute URL, so links resolve both on GitHub and on any published site.
- **Diagrams** use fenced Mermaid or plain-text blocks, matching the style already used in
  `docs/02-reference/architecture/`.
- **British or American spelling** — match the document you are editing rather than converting it.

## Good first contributions

- **Propose a metric** using the template in
  `docs/_templates/metric-template.md`, following the admissions rule above. Several candidates are
  already parked in `docs/03-metrics-and-compliance/governance-metrics/planned-metrics.md`.
- **Answer an open question.** `docs/07-project-and-roadmap/README.md#open-questions` and most
  component reference pages close with named gaps.
- **Report a gap.** If the architecture does not cover a system you need to govern, open a
  Discussion issue describing it.

Target audiences for feedback include application security teams, identity practitioners,
authorization vendors, auditors, GRC professionals, cloud security teams, and AI governance
practitioners.

## Governance

See `GOVERNANCE.md` for how decisions are made, and `CODE_OF_CONDUCT.md` for the standards that
apply to all project spaces.
