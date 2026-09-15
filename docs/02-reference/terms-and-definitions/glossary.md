---
title: "Glossary"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, reference, glossary]
related: [../architecture/README.md, ../components/README.md]
---

# Glossary

| Term | Definition |
|---|---|
| **Capability** | The fundamental unit of governance in GovOps: an action-resource pair (e.g., "transfer" + "bank-account"), optionally qualified by risk-tier, business-impact, data-sensitivity, geography, and org-unit. See `../../06-decisions/ADR-001-capability-as-unit-of-governance.md`. |
| **`capability_id`** | The stable identifier for a capability: a SHA-256 hash of `<group-slug>` + `<action-slug>` + `<resource-slug>` (pipe-delimited). See `../components/capability-catalog.md#the-capability_id-convention`. |
| **Governor** | A term used informally throughout this documentation for organizational stakeholders (security, audit, compliance, engineering, finance, legal) who define, review, or consume governance decisions. **Not yet formally defined** — see `../../07-project-and-roadmap/README.md`. |
| **Governance Plane** | The centralized plane where governance decisions are authored and reviewed: Capability Catalog, Policy Management, Schema Management, Federation Management, Continuous Compliance. |
| **Runtime Plane** | The distributed plane where Policy Decision Points make local authorization decisions at request time. |
| **GovOps loop** | The closed loop: Govern → Authorize → Execute → Observe → Detect → Respond → Govern. See `../architecture/README.md#4-the-govops-loop`. |
| **Runtime Authorization Context** | The minimal decision record that crosses the Governance/Runtime plane boundary: `capability_id`, `decision`, `decision_id`, `policy_store_id`, `policy_store_version`. See `../components/runtime-authorization-context.md`. |
| **Application telemetry** | Self-reported application logs/traces — can be silent or wrong, unlike kernel observability. |
| **Kernel observability** | Independent, OS-level proof of what actually executed (via eBPF, kernel tracing, or equivalent), decoupled from application self-reporting. See `../components/kernel-observability.md`. |
| **PBAC** | Policy-Based Access Control — a decision mechanism (how a decision gets made), not a governance process. GovOps is engine-neutral with respect to PBAC engines. See `../../01-explanation/why-existing-approaches-fall-short/pbac-without-governance-layer.md`. |
| **RBAC** | Role-Based Access Control — the traditional identity-centric model GovOps positions itself against, citing role explosion and drift as key weaknesses. See `../../01-explanation/problem/README.md`. |
| **PARC** | Principal, Action, Resource, Context — the request shape used by AuthZen; neutral on whether the "principal" is an identity or something else. See `../../01-explanation/positioning/README.md#relationship-to-authzen`. |
| **PDP** | Policy Decision Point — evaluates a request against published policy; stateless, atomic, policy-neutral, observable. See `../components/policy-decision-point.md`. |
| **PEP** | Policy Enforcement Point — enforces the PDP's decision. In most GovOps deployments, the PEP is the requesting application itself. |
| **PAP** | Policy Administration Point — authors, versions, and publishes policy. In GovOps, this role is played by Policy Management. |
| **ACC** | Authorization Capability Catalog — the formal name for GovOps's Capability Catalog component and its CUE/Gemara-based schema. See `../components/capability-catalog.md`. |
| **Gemara** | An OpenSSF project providing generic `#Capability` / `#CapabilityCatalog` CUE types that GovOps's Authorization Capability Profile refines. Also the basis for GovOps's compliance-mapping path to OSCAL. |
| **OSCAL** | Open Security Controls Assessment Language — the compliance-interoperability layer GovOps's Continuous Compliance service targets, via Gemara, rather than reinventing framework-specific mappings itself. |
| **Trestle** | Tooling in the OSCAL ecosystem used to project Gemara/GovOps evidence into OSCAL artifacts. |
| **Decision** | The outcome of a PDP evaluation: `allow`, `deny`, or `challenge`. See `../components/policy-decision-point.md#decision-outcomes-allow-deny-and-challenge`. |
| **Event** *(gap)* | Event Handling and Response currently has `event_type` and `timestamp` but no formal `event_id` — treat "event" as an informal term until this is resolved. |
| **Trace execution** *(gap)* | No `trace_execution_id` is yet defined to link a decision to the specific execution it authorized. See `../../06-decisions/ADR-003-information-model-identifier-strategy.md`. |
| **Federation** | The governance decision of which external issuers/token sources an organization trusts, separate from runtime token validation. See `../components/federation-management.md`. |
| **Challenge** | A third PDP decision outcome (alongside allow/deny) requiring additional evidence before a request can proceed. Carries `challenge_id`, `capability_id`, `reason`, `required_evidence`, `expires_at`. |
| **Two-plane model** | GovOps's separation of governance (centralized) from enforcement (distributed runtime). See `../architecture/README.md#3-the-two-plane-model`. |
