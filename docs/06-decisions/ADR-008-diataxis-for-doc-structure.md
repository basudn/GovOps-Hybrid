---
title: "ADR-008: Diátaxis for Doc Structure"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, documentation]
related: [../02-reference/README.md]
---

# ADR-008: Diátaxis for Doc Structure

## Status
Proposed — Diátaxis itself was agreed by the group (2026-09-09) as the starting approach; this ADR documents the reasoning and the resulting tier structure, which is Vatsal Gupta's own proposal, not yet reviewed by the group.

## Context
Mike Schwartz's item 1/2 agenda items flag that the current draft mixes a normative spec, an implementation guide, and an explanatory overview in one document, making it unclear what's required. Mike also wants the material formatted so AI agents can consume it, since the target audience uses AI heavily.

## Decision
Adopt the Diátaxis framework (Tutorial/How-to/Reference/Explanation, split by reader intent) as the organizing principle, implemented as a five-tier structure (`00-foundations` through `05-tutorials`) plus two cross-cutting additions: `06-decisions` (ADRs, for rationale) and `07-project-and-roadmap` (for tracking what's unsettled), borrowed from a Perplexity-assisted refinement and cross-checked against this session's actual content decisions.

## Consequences
Requires splitting content across multiple documents rather than one long doc with sections — more files to maintain, but each one serves a single reader intent and is easier for both humans and AI agents to retrieve precisely.

## Alternatives considered
- **One long document with sections**: rejected — doesn't resolve the mixed-intent problem Mike flagged, just reorders it.
- **Generic company-engineering-wiki template** (foundations/product/architecture/guides/processes/decisions/reference, from the initial Perplexity answer): rejected as the primary structure — it's context-free and doesn't encode any of GovOps's actual content decisions (CSA gap, Gemara defense, nine services, item 9 scenarios). Its ADR and front-matter-metadata mechanics were adopted; its folder taxonomy was not.

## Links
`../02-reference/README.md`, `../00-foundations/README.md`
