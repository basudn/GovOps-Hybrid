---
title: "Capability Catalog"
status: draft
document_type: component-reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, component, acc, gemara]
related: [../../06-decisions/ADR-001-capability-as-unit-of-governance.md, ../architecture/README.md, ../information-model/README.md]
---

# Capability Catalog

## Purpose

The Capability Catalog (sometimes called the Authorization Capability Catalog, or ACC) is the
governance-plane system of record for every governed action-resource pair in an organization. It
assigns each pair a stable `capability_id` and attaches optional risk and ownership metadata, so
that policy authoring, runtime decisions, observability, and compliance evidence can all be
correlated against the same identifier. See `../../06-decisions/ADR-001-capability-as-unit-of-governance.md`
for why the capability — not the identity — was chosen as GovOps's fundamental unit of governance.

## Responsibilities

- Define the schema for a capability: a required `action` + `resource` pair, optionally
  qualified by `risk-tier`, `business-impact`, `data-sensitivity`, `geography`, and `org-unit`.
- Compute and assign the `capability_id` for every catalog entry.
- Organize capabilities into named `Group`s (e.g., `payments`, `lending`) and maintain the
  canonical `Lexicon` of group/action/resource terms so capability slugs stay consistent.
- Provide lint tooling (`govops lint`) that validates catalog entries against the schema and
  lexicon before they are merged.
- Provide drift tooling (`govops drift`) that compares the catalog against deployed policy to
  surface capabilities that are ungoverned, unused, or inconsistently scoped.
- Export the catalog in a form Continuous Compliance can map through Gemara to OSCAL.

## Non-responsibilities

- Does not evaluate runtime authorization requests — that is the Policy Decision Point.
- Does not author or store policy logic — that is Policy Management.
- Does not decide which token issuers are trusted — that is Federation Management.
- Does not define the wire schema for entities/attributes used by policy engines — that is
  Schema Management.

## The `capability_id` convention

A capability's identifier is a deterministic hash so that the same action-resource pair always
resolves to the same ID, regardless of which team or tool registers it:

```text
capability_id = SHA-256( "<group-slug>|<action-slug>|<resource-slug>" )
```

For example, the capability "transfer funds from a bank account" in the `payments` group:

```text
group:    payments
action:   transfer
resource: bank-account

preimage:      "payments|transfer|bank-account"
capability_id: 0c451a4b7305a117ad7e4c874799c5982100823ef1b71db3490fffc35a63fef3
```

Only the group/action/resource slugs feed the hash. Risk-tier, business-impact, and the other
optional metadata fields are *not* part of the preimage — they can change over time (a
capability's business impact can be re-assessed) without changing its identity. This is what
makes `capability_id` safe to use as a long-lived correlation key across the Policy Decision
Point, Runtime Authorization Context, Kernel Observability, and Continuous Compliance (see
`../architecture/component-view.md#the-capability_id-data-flow-trace`).

## Schema

The catalog is modeled as a CUE profile that refines Gemara's generic `#Capability` type with the
fields authorization governance actually needs:

```cue
package authorization

import "github.com/gemaraproj/gemara@v1:gemara"

#AuthorizationCapability: {
	gemara.#Capability
	action:              string
	resource:            string
	"risk-tier"?:        "critical" | "high" | "medium" | "low"
	"data-sensitivity"?: "public" | "internal" | "confidential" | "restricted"
	"business-impact"?:  string
	geography?:          string
	"org-unit"?:         string
}

#AuthorizationCapabilityCatalog: {
	gemara.#CapabilityCatalog
	capabilities: [#AuthorizationCapability, ...#AuthorizationCapability]
}
```

| Field | Required | Purpose |
|---|---|---|
| `action` | Yes | The verb being governed (e.g., `transfer`, `read`, `approve`). |
| `resource` | Yes | The noun being acted on (e.g., `bank-account`, `invoice`). |
| `risk-tier` | No | `critical` \| `high` \| `medium` \| `low` — feeds prioritization and required segmentation in governance metrics (see `../../03-metrics-and-compliance/governance-metrics/README.md`). |
| `business-impact` | No | Free-text or org-defined enum describing consequence of misuse. |
| `data-sensitivity` | No | `public` \| `internal` \| `confidential` \| `restricted`. |
| `geography` | No | Jurisdictional scope, relevant to data-residency and federation decisions. |
| `org-unit` | No | Ownership for accountability and review routing. |

Risk-tier and business-impact together are the primary prioritization axis: a `critical`
risk-tier capability with `high` business-impact is the top candidate for policy authoring and
review attention; a `low`/`low` capability may be safe to leave under a permissive default while
governance effort is focused elsewhere.

### Repository layout

A GovOps-ACC repository is organized as:

```text
govops/
├── lexicon.yaml          # canonical group/action/resource terms
├── metadata.yaml          # catalog-level metadata (owner, version)
├── GovOps-ACC.yaml         # the capability catalog itself
├── GovOps-ACO.yaml         # capability-to-control mappings (compliance layer input)
├── mappings/               # framework-specific mapping documents
└── exports/oscal/          # generated OSCAL artifacts
```

The `Lexicon` is the controlled vocabulary of group, action, and resource slugs. Requiring new
capabilities to draw from (or extend, via review) the lexicon is what keeps `capability_id`
generation consistent across teams instead of splintering into synonyms (`xfer` vs. `transfer`).

## Tooling

- **`govops lint`** — validates that every entry in a catalog file conforms to the
  `#AuthorizationCapability` schema, that its slugs exist in the lexicon, and that its
  `capability_id` matches the recomputed hash of its own group/action/resource. Run in CI on
  every catalog change; a failing lint blocks merge.
- **`govops drift`** — compares the catalog against what is actually deployed in policy stores or
  observed at runtime, and reports three classes of finding:
  - **Type A** — a capability exists in the catalog but no policy references it (governed but
    unenforced).
  - **Type B** — a policy or runtime decision references a `capability_id` that does not exist in
    the catalog (enforced but ungoverned).
  - **Type C** — a capability's declared risk-tier/business-impact is inconsistent with the
    permissiveness of the policy actually enforcing it.

## Inputs

| Input | Source | Purpose | Required |
|---|---|---|---|
| New/changed capability definitions | Engineering teams (via PR) | Register or update a governed action-resource pair | Yes |
| Lexicon terms | Governance working group | Constrain slugs to a controlled vocabulary | Yes |
| Deployed policy references | Policy Management | Input to `govops drift` comparison | No (drift only) |

## Outputs

| Output | Consumer | Purpose |
|---|---|---|
| `capability_id` | Policy Management, Policy Decision Point, Runtime Authorization Context, Kernel Observability, Continuous Compliance | Cross-plane correlation key |
| Catalog export (Gemara `#CapabilityCatalog`) | Continuous Compliance | Input to the Gemara → OSCAL mapping path |
| Lint/drift reports | Engineering teams, governance reviewers | Surface ungoverned, unenforced, or inconsistent capabilities |

## Authoritative data

| Data object | Authority | Lifecycle owner | Notes |
|---|---|---|---|
| `capability_id` | Capability Catalog | Governance working group | Never reused for a different action-resource pair, even after retirement. |
| Lexicon | Capability Catalog | Governance working group | Extended via review, not ad hoc. |
| Risk-tier / business-impact values | Capability Catalog | Capability's `org-unit` owner | Re-assessable without changing `capability_id`. |

## Dependencies

- Gemara (`#Capability`, `#CapabilityCatalog` base types).
- Schema Management, for the entity/attribute vocabulary that resource and action slugs must
  stay consistent with.

## Interfaces

- CUE/YAML catalog files, versioned in source control.
- `govops lint` / `govops drift` CLI, run in CI.
- Catalog export consumed by Continuous Compliance (see `../../03-metrics-and-compliance/compliance-path/gemara-to-oscal.md`).

## Security and trust considerations

The catalog is governance-plane data — it describes what capabilities exist and how risky they
are, not any live secret or credential. Its integrity matters because `capability_id` is trusted
as a correlation key everywhere downstream; an incorrectly computed or collided ID would silently
misattribute policy, evidence, or metrics to the wrong capability. Lint enforcement of the hash
convention is the primary control against this.

## Observability and evidence

Catalog changes (additions, retirements, risk-tier changes) should themselves be auditable via
normal version-control history. `govops drift` findings are evidence that the catalog reflects
what is actually enforced, and Type A/B/C findings are a leading indicator worth tracking over
time (see `../../03-metrics-and-compliance/governance-metrics/README.md`).

## Illustrative use cases

These four condensed scenarios illustrate the catalog and its tooling in use; see the original
detailed worked examples (Acme Bank, Meridian Finance-style catalogs) for full command output.

1. **Catalog authoring.** A platform security engineer adds a new capability
   (`payments|approve|large-transaction`) to `GovOps-ACC.yaml`, drawing its slugs from the
   existing lexicon, and opens a PR. CI runs `govops lint`, which recomputes the `capability_id`
   and confirms it matches, and confirms `approve` and `large-transaction` are valid lexicon
   terms.
2. **Compliance mapping.** A compliance auditor uses `GovOps-ACO.yaml` to map the same capability
   to relevant controls (e.g., an approval-segregation-of-duties control), which Continuous
   Compliance later projects into an OSCAL component definition (see
   `../../03-metrics-and-compliance/compliance-path/gemara-to-oscal.md`).
3. **Lint enforcement.** A second engineer submits a capability with a typo'd resource slug not
   present in the lexicon; `govops lint` fails the PR with a specific error identifying the
   invalid slug, preventing catalog drift at the source.
4. **Drift detection.** A scheduled `govops drift` run compares the catalog against policy store
   contents and reports a Type B finding: a policy enforces a `capability_id` that was retired
   from the catalog six months earlier, prompting a review of whether the policy or the catalog is
   stale.
