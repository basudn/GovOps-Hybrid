# GovOps Reference Architecture

## 1. Architecture Vision

The GovOps architecture is based on a capability-centric model, where the fundamental unit of governance is a **capability** (an action-resource pair). This represents a shift away from traditional identity-centric models.

The architecture is designed to be a vendor-neutral framework that can be applied to a wide range of systems and technologies. See `./system-context.md` for the C4 Level-1 boundary view and `./component-view.md` for how the individual services relate to one another.

## 2. Architecture Diagram

Below is a high-level diagram illustrating the key components and data flows within the GovOps framework.

[View Architecture Diagram](./architecture-diagram.md)

## 3. The Two-Plane Model

GovOps separates governance from enforcement into two planes (see `../../00-foundations/scope.md`):

- **Governance Plane** (centralized) — the Capability Catalog, Policy Management, Schema
  Management, Federation Management, and Continuous Compliance. This is where governance
  decisions are authored and reviewed.
- **Runtime Plane** (distributed) — the actual policy decision points (PDPs) making local
  authorization decisions at request time, using policy and schema published by the Governance
  Plane.

## 4. The GovOps Loop

Governance is not a one-time catalog exercise; it is a closed loop:

```text
Govern → Authorize → Execute → Observe → Detect → Respond → Govern
```

| Step | What happens | Primary component(s) |
|---|---|---|
| Govern | Capabilities are inventoried, classified, and policy intent is authored. | Capability Catalog, Policy Management, Schema Management |
| Authorize | A runtime request is evaluated against published policy. | Policy Decision Point |
| Execute | The authorized action is actually carried out. | The application / workload itself |
| Observe | Application telemetry and kernel-level observability are joined on `capability_id`. | Runtime Authorization Context, Kernel Observability |
| Detect | Drift, anomalies, or policy violations are identified from the joined Observe data. | Event Handling and Response |
| Respond | An automated or human remediation action is triggered. | Event Handling and Response |
| Govern | Findings feed back into catalog and policy revisions, closing the loop. | Capability Catalog, Policy Management |

## 5. The Four Functional Layers

Independent of the two-plane split, GovOps's responsibilities can be grouped into four layers:

1. **Governance layer** — Capability Catalog, Policy Management, Schema Management: what is
   allowed to exist and be governed.
2. **Observability layer** — Runtime Authorization Context, Kernel Observability: what actually
   happened, correlated back to what was governed.
3. **Identity layer** — Federation Management: which external issuers and credential sources are
   trusted, independent of any specific runtime decision.
4. **Event-handling layer** — Event Handling and Response: closing the Detect → Respond part of
   the loop.

Continuous Compliance and Governance Metrics cut across all four layers rather than belonging to
one; see `../../03-metrics-and-compliance/README.md`.

## 6. Capability Lifecycle

A capability moves through a lifecycle independent of, but coordinated with, the GovOps loop:

```text
Discover → Register → Classify → Govern → Deploy → Observe → Retire
```

- **Discover** — an engineering team identifies a new action-resource pair the system exposes.
- **Register** — the capability is added to the catalog and assigned a `capability_id`.
- **Classify** — risk-tier, business-impact, and other optional metadata are attached (see
  `../components/capability-catalog.md`).
- **Govern** — policy is authored or updated to cover the capability.
- **Deploy** — the policy reaches production and the capability becomes enforceable.
- **Observe** — runtime decisions and execution evidence accumulate against the `capability_id`.
- **Retire** — the capability is deprecated when the underlying action or resource no longer
  exists; the `capability_id` is never reused for a different action-resource pair.

## 7. Core Components

### 7.1. Authorization Capability Catalog (ACC)

The ACC is a centralized catalog of all capabilities within the system. It provides a standardized way to define and manage capabilities, including their associated metadata (e.g., risk, business impact). See `../components/capability-catalog.md` for the full design (Gemara/CUE profile, `capability_id` hash convention, lint/drift tooling).

### 7.2. Policy Decision Point (PDP)

The PDP is responsible for evaluating policies and making access decisions. In the GovOps framework, PDPs are designed to be:

- **Stateless:** Each decision is atomic and independent.
- **Policy-Neutral:** The framework does not prescribe a specific policy language or engine.

See `../components/policy-decision-point.md`.

### 7.3. Observable Events

GovOps enables runtime observability of policy decisions by exposing events at the kernel level. These events include information such as the capability ID, the decision (allow/deny), and the policy version. See `../components/kernel-observability.md` and `../components/runtime-authorization-context.md`.

## 8. Key Architectural Patterns

### 8.1. Centralized Policy Authoring

Policies are authored and managed in a centralized, vendor-neutral repository. This allows for version control, analysis, and automated distribution of policies.

### 8.2. Distributed Runtime Security

The architecture supports the distribution of runtime security engines (PDPs) throughout the system. This allows for enforcement of policies at the edge, closer to the resources being protected.

### 8.3. Alignment with "Beyond Zero"

The GovOps architecture is strategically aligned with the principles of Google's "Beyond Zero" framework, which also emphasizes a shift towards a more granular, resource-oriented security model. See `../../01-explanation/positioning/README.md` for the full comparison, including its currently unconfirmed status.

## 9. Known Inconsistency: Five Core Services vs. Nine Total

Earlier GovOps material describes "the core GovOps services" as five (Capability Catalog, Policy
Management, Schema Management, Federation Management, Continuous Compliance) while also
documenting nine service sections in total (adding Runtime Authorization Context, Governance
Metrics, Kernel Observability, and Event Handling and Response), without explaining why the other
four are excluded from "core." This inconsistency has not been resolved. See
`../../06-decisions/ADR-002-nine-service-reference-model.md` and `../components/README.md`.
