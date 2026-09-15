---
title: "The Gemara to OSCAL Compliance Path"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, compliance, gemara, oscal]
related: [../../06-decisions/ADR-007-gemara-for-compliance-path.md, ../../02-reference/components/capability-catalog.md, ./framework-mappings.md]
---
# The Gemara to OSCAL Compliance Path

A foundational principle of GovOps is to treat governance and compliance as an engineering discipline. The choice to use the **Gemara** format for the Authorization Capability Catalog (ACC) is a direct reflection of this principle.

This document explains the relationship between Gemara and the Open Security Controls Assessment Language (OSCAL) and why this connection is critical for automating compliance.

## What are Gemara and OSCAL?

-   **Gemara:** An open-source standard from the OpenSSF for creating a security-focused, machine-readable body of knowledge. It provides a structured YAML format for defining security concepts.

-   **OSCAL:** The Open Security Controls Assessment Language is a set of standardized, machine-readable formats (XML, JSON, YAML) for documenting and assessing security controls. Developed by NIST, it is the modern standard for automating security assessments and compliance.

## GovOps and Gemara: A Concrete Example

GovOps uses the Gemara format to define its capabilities in the ACC. This provides a structured, machine-readable way to describe a governable action.

Consider the `approve-loan` capability. A simplified entry in the ACC, using a Gemara-aligned structure, might look like this:

```yaml
- name: cap:01H9J8K7N6P5R7Z3Y1X0W4B2D1
  description: The capability to approve a customer loan application over $10,000.
  type: capability  # GovOps specific type
  metadata:
    action: approve-loan
    resource: banking-app
    risk: high
    business_impact: significant
    # Custom metadata for compliance mapping
    maps_to_control:
      - id: "AC-3"
        framework: "NIST 800-53"
      - id: "9.2.2"
        framework: "ISO 27001"
```

In this example, Gemara provides the `name` and `description` fields. GovOps extends this with specific metadata like `action`, `risk`, and, crucially, a `maps_to_control` object that creates an explicit link between the GovOps capability and specific compliance controls.

## The three-layer compliance architecture

The path from a governed capability to a framework-specific audit artifact passes through three
distinct layers, each with a different job:

| Layer | Owned by | Job |
|---|---|---|
| **Enterprise truth** | Gemara / GovOps | The actual capability catalog and decision/execution evidence — what exists and what happened, in GovOps's own vocabulary. |
| **Compliance interoperability** | OSCAL / Trestle | A framework-neutral intermediate representation (OSCAL component definitions, control implementations) that any framework-specific tool can consume. |
| **Framework renderings** | Framework-specific tooling | The final artifact a specific auditor or regulator expects — NIST 800-53, ISO 27001, SOC 2, EU CRA, or others. |

## Why map to an abstract control layer first, rather than directly to each framework

1. **One mapping, many frameworks.** Mapping each capability to an abstract, OSCAL-native
   control layer once is cheaper than maintaining N separate direct mappings, one per framework
   GovOps might need to support.
2. **Frameworks change independently of capabilities.** A capability's risk profile does not
   change when ISO revises a control numbering scheme; only the OSCAL-to-framework rendering
   layer needs updating.
3. **Reuse of existing tooling.** OSCAL already has an ecosystem (Trestle and others) for
   producing framework-specific renderings; GovOps does not need to reinvent this, only feed it
   correctly formed input.
4. **Framework-neutral evidence is auditable evidence.** An enterprise truth layer that isn't
   already coupled to one framework's vocabulary is easier to audit against multiple frameworks
   without re-deriving evidence each time.

## The Strategic Path: From Capability to Compliance

The GovOps compliance path is designed to be automated, auditable, and continuous, moving away from point-in-time, manual spreadsheet audits. The flow is as follows:

1.  **Define Capabilities in Gemara:** Your organization defines its governable actions in the ACC using the Gemara format, as shown in the example above.

2.  **Transform to OSCAL:** Because Gemara is designed for this purpose, the ACC can be programmatically transformed into an OSCAL Component Definition. In this format, each GovOps `capability` is treated as a defined component that a system offers, with explicit links to the controls it satisfies.

3.  **Automate Assessment:** With the mappings in place, compliance tools that consume OSCAL (like the CNCF's Trestle project) can be used to automate assessments. The observable events from GovOps provide real-time **evidence** that the controls for each capability are being correctly enforced.

## Why This Matters

-   **Engineering-First Approach:** This approach aligns with modern DevOps and GitOps workflows. Compliance becomes part of the development lifecycle, not a separate, manual process.
-   **Auditability by Design:** The entire chain—from the definition of a capability in Gemara to the real-time events showing its execution—is machine-readable and traceable.
-   **Continuous Compliance:** Instead of periodic audits, the real-time events from GovOps provide a continuous stream of evidence, allowing for ongoing compliance verification.

By choosing Gemara, GovOps provides a clear, strategic path to connect its granular, capability-based governance model to the major, standardized compliance frameworks used across the industry.
