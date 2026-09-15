---
title: "Schema Management"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, schema]
related: [./capability-catalog.md, ./policy-management.md, ../../07-project-and-roadmap/README.md]
---

# Schema Management

## Purpose

Schema Management governs the structure of the data that the Capability Catalog and Policy
Management depend on: entity types, their attributes, and the actions that can be performed on
them. It is the shared contract that lets issuers, applications, policy authors, and Policy
Decision Points agree on what an attribute name means and what shape it takes, without each
integration reinventing that agreement bilaterally.

> **Status note.** This is the least fleshed-out of the GovOps services. Its scope is described
> here at the level the reference model currently supports; the detailed schema format, versioning
> model, and change-management process are open questions tracked in
> `../../07-project-and-roadmap/README.md`.

## Responsibilities

- Define entity types (e.g., `user`, `service-account`, `bank-account`) and their attributes.
- Define the vocabulary of actions available for policy and catalog authors to reference.
- Version schema changes so that policy and catalog content written against one schema version
  can be evaluated for compatibility against a newer one.
- Act as the point of agreement between token issuers (who populate claims), applications (who
  request decisions), policy authors (who write conditions), and Policy Decision Points (who
  evaluate them).

## Non-responsibilities

- Does not assign `capability_id` values — that is the Capability Catalog.
- Does not author policy content — that is Policy Management.
- Does not decide which issuers are trusted to populate a given claim — that is Federation
  Management.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| Proposed entity/attribute additions | Engineering teams | Extend the schema to cover new data | Yes |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| Entity/attribute schema | Capability Catalog, Policy Management, Policy Decision Point | Shared vocabulary for resources, conditions, and claims |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| Entity/attribute definitions | Schema Management | Governance working group | Versioning and compatibility model not yet defined. |

## Dependencies

- None upstream; this is a foundational governance-plane service.

## Interfaces

- Consumed by the Capability Catalog (resource/action slugs) and Policy Management (condition
  attributes). No formal interface contract is defined yet — see `../interfaces/README.md`.

## Security and trust considerations

Because Schema Management defines what claims and attributes *mean*, an inconsistency here (e.g.,
two systems disagreeing on what a `role` attribute represents) can silently produce incorrect
authorization outcomes even when the Capability Catalog and Policy Management are each internally
consistent. This makes schema agreement a prerequisite for, not an optional refinement of, the
rest of the governance plane.

## Observability and evidence

Not yet defined.

## Related decisions

None yet — the boundary and depth of this service are open questions (see
`../../07-project-and-roadmap/README.md`).
