---
title: "ADR-006: Conformance Postponed"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, conformance]
related: [../02-reference/conformance/README.md]
---

# ADR-006: Conformance Postponed

## Status
Proposed

## Context
Mike Schwartz's item 1 agenda item asks whether the architecture doc is a normative spec, an implementation guide, or an explanatory overview — currently it mixes all three, making it unclear what implementers must support.

## Decision
The Architecture doc is not a normative spec and not an implementation guide yet — it's too early, given the unresolved five-vs-nine services inconsistency (ADR-002), the undefined `trace_execution_id` (ADR-003), and missing non-responsibility sections. It stays mostly explanatory (Diátaxis "Explanation"/"Reference," see ADR-008). The Authorization Capability Catalog (ACC) is the one exception: it will get a normative spec, phased for later, once it matures past the open questions in GovOpsWG/GovOps issue #17.

## Consequences
No Conformance section can be written with real MUST/SHOULD requirements yet, beyond a placeholder describing this deferral. This also means the doc's "AI-agent readability" requirement (Mike Schwartz, 2026-09-12) should not be read as implying the content itself is authoritative/normative — status fields must make that clear.

## Alternatives considered
- **Write a full Conformance section now**: rejected — would lock in requirements the group hasn't actually agreed on, on top of a model still in flux.

## Links
`../02-reference/conformance/README.md`, `../01-explanation/architectural-principles.md`
