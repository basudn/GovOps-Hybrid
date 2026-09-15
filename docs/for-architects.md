---
title: "Technical Overview for Architects and Engineers"
audience: technical
status: draft
summary: "A technical entry point for architects and engineers, providing an overview of the GovOps architecture and linking to key reference materials."
---
# Technical Overview for Architects and Engineers

This document provides a technical starting point for understanding and implementing the GovOps framework.

## Core Architectural Principles

- **Capability-Centric:** The fundamental unit of governance is the **capability**, defined as an `action-resource` pair. This shifts the focus from static roles to observable actions.
- **Policy-Neutral:** GovOps is not a policy engine. It is a governance layer designed to work with any policy decision point (PDP), such as OPA, Cedar, or custom engines.
- **Runtime Observability:** The core technical feature of GovOps is its ability to make authorization decisions observable at the kernel level, providing a high-fidelity, real-time stream of security events.

## Getting Started: Key Documents

1.  **The Specification (`/spec` directory):**
    - **[ACC Schema (`/spec/acc-schema.yaml`)]**: This is the normative, machine-readable schema for the Authorization Capability Catalog. All capabilities must conform to this model.
    - **[Observable Event Schema (`/spec/observable-event-schema.yaml`)]**: This is the normative schema for the events that policy engines must emit to be compliant with GovOps observability.

2.  **Reference Architecture (`/docs/02-reference/architecture`):
    - **[Reference Architecture (`README.md`)]**: A detailed description of the architectural model and its components.
    - **[Components (`/docs/02-reference/components`)]**: Deep dives into specific components like the **Capability Catalog** and **Kernel Observability**.

3.  **Architectural Decision Records (`/docs/06-decisions`):
    - Read the **[ADRs]** to understand the rationale behind key design choices, such as why "capability" was chosen as the unit of governance.

## How It Works: The Flow

1.  **Define Capabilities:** An organization inventories its critical operations as capabilities in the ACC (e.g., `capability: approve-payment`).
2.  **Instrument the PDP:** The policy engine (e.g., OPA) is instrumented to emit an **Observable Event** after every authorization decision.
3.  **Observe and Act:** A monitoring system consumes the stream of events, checks them against governance rules, generates metrics, and triggers alerts or automated remediation actions.
