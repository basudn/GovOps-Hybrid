# GovOps Reference Architecture

## 1. Architecture Vision

The GovOps architecture is based on a capability-centric model, where the fundamental unit of governance is a **capability** (an action-resource pair). This represents a shift away from traditional identity-centric models.

The architecture is designed to be a vendor-neutral framework that can be applied to a wide range of systems and technologies.

## 2. Architecture Diagram

Below is a high-level diagram illustrating the key components and data flows within the GovOps framework.

[View Architecture Diagram](./architecture-diagram.md)

## 3. Core Components

### 3.1. Authorization Capability Catalog (ACC)

The ACC is a centralized catalog of all capabilities within the system. It provides a standardized way to define and manage capabilities, including their associated metadata (e.g., risk, business impact).

### 3.2. Policy Decision Point (PDP)

The PDP is responsible for evaluating policies and making access decisions. In the GovOps framework, PDPs are designed to be:

- **Stateless:** Each decision is atomic and independent.
- **Policy-Neutral:** The framework does not prescribe a specific policy language or engine.

### 3.3. Observable Events

GovOps enables runtime observability of policy decisions by exposing events at the kernel level. These events include information such as the capability ID, the decision (allow/deny), and the policy version.

## 4. Key Architectural Patterns

### 4.1. Centralized Policy Authoring

Policies are authored and managed in a centralized, vendor-neutral repository. This allows for version control, analysis, and automated distribution of policies.

### 4.2. Distributed Runtime Security

The architecture supports the distribution of runtime security engines (PDPs) throughout the system. This allows for enforcement of policies at the edge, closer to the resources being protected.

### 4.3. Alignment with "Beyond Zero"

The GovOps architecture is strategically aligned with the principles of Google's "Beyond Zero" framework, which also emphasizes a shift towards a more granular, resource-oriented security model.
