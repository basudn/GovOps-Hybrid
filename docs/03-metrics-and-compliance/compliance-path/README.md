---
title: "Compliance Path"
status: draft
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, compliance]
related: [./gemara-to-oscal.md, ./framework-mappings.md, ../../06-decisions/ADR-007-gemara-for-compliance-path.md]
---

# Compliance Path

The compliance path is GovOps's direct answer to the point-in-time compliance gap described in
`../../01-explanation/problem/README.md`: `capability_id` → Gemara → OSCAL → framework profile.
See `./gemara-to-oscal.md` for the full mechanics and worked example.

## What GovOps can and cannot claim

GovOps provides a governance model, a traceability structure, evidence-oriented metrics, and
mappings to external control frameworks. It explicitly does **not**:

1. Define legal interpretations of the EU Cyber Resilience Act, SOC 2, ISO 27001, or any other
   framework.
2. Certify an organization, product, or implementation as compliant.
3. Replace a formal risk assessment, audit, legal review, or control owner.
4. Define all organization-specific security controls.
5. Guarantee that a mapped technical artifact satisfies an external control.

This boundary exists specifically to prevent a framework mapping from being read — by a human or
an AI agent consuming this documentation — as an unsupported claim of compliance. See
`../../00-foundations/scope.md` for the parallel non-goals statement at the whole-project level.

## Contents

- [Gemara to OSCAL](./gemara-to-oscal.md) — the mechanics of the mapping path and a worked
  example.
- [Framework Mappings](./framework-mappings.md) — current status (all planned, none built) for
  ISO 27001, SOC 2, the EU Cyber Resilience Act, and general OSCAL control mapping.
