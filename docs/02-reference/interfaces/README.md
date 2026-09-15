---
title: "Interfaces"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, interfaces]
related: [../components/runtime-authorization-context.md, ../architecture/component-view.md, ../../06-decisions/ADR-003-information-model-identifier-strategy.md]
---

# Interfaces

This section consolidates the contracts between GovOps components — what is actually defined
today, and what remains an open gap. See `../architecture/component-view.md` for the relationship
diagram these interfaces implement.

## Component interaction flow

```text
Capability Catalog ──▶ Policy Management ──▶ Policy Decision Point ──▶ Runtime Authorization Context
                                                                              │
                                                    ┌─────────────────────────┼─────────────────────────┐
                                                    ▼                         ▼                         ▼
                                          Kernel Observability      Event Handling and Response   Continuous Compliance
```

## The one defined contract: the Runtime Context Contract

The only interface with a fully specified minimum field set today is the record emitted by the
Runtime Authorization Context:

| Field | Required |
|---|---|
| `capability_id` | Yes |
| `decision` | Yes |
| `decision_id` | Yes |
| `policy_store_id` | Yes |
| `policy_store_version` | Yes |

This contract deliberately **never carries**: the token itself, policy text, or the full request
payload. See `../components/runtime-authorization-context.md` for the rationale.

## Undefined contracts

The following component-to-component interfaces exist conceptually (per
`../architecture/component-view.md`) but do not yet have a specified wire format, schema, or
transport:

| Interface | Between | Status |
|---|---|---|
| Catalog distribution | Capability Catalog → Policy Management | Undefined |
| Policy distribution | Policy Management → Policy Decision Point | Undefined |
| Observability event format | Kernel Observability → Event Handling and Response / Continuous Compliance | Undefined beyond the informal field list in `../components/kernel-observability.md` |
| Event/finding format | Event Handling and Response → Capability Catalog / Policy Management (closing the loop) | Undefined — no formal `event_id` |

## The authorization-to-execution contract gap

The largest single undefined contract is between authorization (the Runtime Authorization
Context's decision record) and execution (what actually ran). Closing this gap requires the
`trace_execution_id` identifier described in
`../../06-decisions/ADR-003-information-model-identifier-strategy.md`, which does not yet exist.
Until it does, joining "what was authorized" to "what specifically executed" relies on
`capability_id` alone plus best-effort timing correlation, not a dedicated join key.

## Related decisions

- `../../06-decisions/ADR-003-information-model-identifier-strategy.md`
- `../../06-decisions/ADR-004-evidence-and-observability-model.md`
