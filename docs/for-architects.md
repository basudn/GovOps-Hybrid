---
title: "Technical Overview for Architects and Engineers"
status: draft
document_type: overview
source_of_truth: false
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, overview, technical-overview, audience-technical]
related: [./02-reference/architecture/README.md, ./02-reference/components/README.md, ./06-decisions/README.md, ./for-executives.md]
---
# Technical Overview for Architects and Engineers

*Audience: architects and engineers implementing or integrating with GovOps. For a business-level
framing, see [`for-executives.md`](./for-executives.md).*

This document provides a technical starting point for understanding and implementing the GovOps framework.

## Core Architectural Principles

- **Capability-Centric:** The fundamental unit of governance is the **capability**, defined as an `action-resource` pair. This shifts the focus from static roles to observable actions.
- **Policy-Neutral:** GovOps is not a policy engine. It is a governance layer designed to work with any policy decision point (PDP), such as OPA, Cedar, or custom engines.
- **Runtime Observability:** GovOps's core technical feature is making authorization decisions observable at the kernel level (or an equivalent high-performance local endpoint), providing a high-fidelity stream of decision and execution evidence. No kernel tool emits `capability_id` natively today — mapping raw kernel/process events back to `capability_id` is a named open gap; see [`kernel-observability.md`](./02-reference/components/kernel-observability.md#known-gap-no-kernel-tool-emits-capability_id-natively).

## Getting Started: Key Documents

1.  **The specification (`/spec` directory):**
    - [ACC Schema (`/spec/acc-schema.yaml`)](../spec/acc-schema.yaml): a machine-readable schema for the Authorization Capability Catalog, aligned with the `capability_id` hash convention in [`capability-catalog.md`](./02-reference/components/capability-catalog.md#the-capability_id-convention). Status: early draft, not yet ratified as normative — see [`conformance/README.md`](./02-reference/conformance/README.md).
    - [Observable Event Schema (`/spec/observable-event-schema.yaml`)](../spec/observable-event-schema.yaml): a schema for the Runtime Authorization Context record a PDP emits after every authorization decision, aligned with the five core fields and minimality rules in [`runtime-authorization-context.md`](./02-reference/components/runtime-authorization-context.md).

2.  **Reference architecture (`docs/02-reference/architecture/`):**
    - [Reference Architecture](./02-reference/architecture/README.md): the two-plane model, GovOps loop, and capability lifecycle.
    - [Components](./02-reference/components/README.md): deep dives into specific components like [Capability Catalog](./02-reference/components/capability-catalog.md) and [Kernel Observability](./02-reference/components/kernel-observability.md).

3.  **Architectural Decision Records (`docs/06-decisions/`):**
    - Read the [ADRs](./06-decisions/README.md) to understand the rationale behind key design choices, such as why "capability" was chosen as the unit of governance.

## How It Works: The Flow

1.  **Define Capabilities:** An organization inventories its critical operations as capabilities in the ACC (e.g., `capability: approve-payment`).
2.  **Instrument the PDP:** The policy engine (e.g., OPA) is instrumented to emit an **Observable Event** after every authorization decision.
3.  **Observe and Act:** A monitoring system consumes the stream of events, checks them against governance rules, generates metrics, and triggers alerts or automated remediation actions.
