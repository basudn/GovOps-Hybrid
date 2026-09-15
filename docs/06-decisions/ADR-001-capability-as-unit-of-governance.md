---
title: "ADR-001: Capability as Unit of Governance"
status: proposed
document_type: ADR
source_of_truth: true
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, adr, capability]
related: [../01-explanation/govops-thesis/capability-as-governance-unit.md]
---

# ADR-001: Capability as Unit of Governance

## Status
Proposed

## Context
Governance programs traditionally organize around identity/roles (RBAC). This causes role explosion as org structure changes, doesn't generalize to non-human actors (AI agents, service accounts), and conflates "who is asking" with "what is being governed."

## Decision
GovOps treats the capability (group, action, resource) as the primary unit of governance. `capability_id` is a stable hash of exactly those three fields. Identity remains a valid input to a runtime decision but is not the catalog's primary key.

## Consequences
- The catalog stays stable across org changes.
- Non-human actors fit naturally (they request actions on resources, not roles).
- Requires a cultural shift: policy authoring/review habits built around personas need to adapt to a capability-first catalog.

## Alternatives considered
- **Pure RBAC**: rejected — role explosion, poor fit for non-human actors.
- **Pure PBAC without a governance layer**: PBAC already supports resource-first policies at the decision-mechanism level, but doesn't prescribe what gets cataloged/audited — GovOps adds that governance-process layer on top, it doesn't replace PBAC. See `../01-explanation/why-existing-approaches-fall-short/pbac-without-governance-layer.md`.

## Links
`../01-explanation/govops-thesis/solution-overview.md#capability-as-the-unit-of-governance`, `../02-reference/information-model/README.md`
