---
title: "How-To and Process"
status: planned
document_type: how-to
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, process, roadmap]
related: [../07-project-and-roadmap/README.md, ../06-decisions/README.md]
---

# How-To and Process

## Status

Planned. This section will contain practical, step-by-step guides for the processes an
organization needs to run GovOps day to day. None of the guides below exist yet — the architectural
and reference material in `02-reference/` describes what the components are and how they relate;
it does not yet establish a required operational process for using them.

## Planned content

- Approval workflows for new and changed capabilities
- Review cadence for the Capability Catalog and policy stores
- Ownership and sign-off model (who is accountable for a capability, and how that is recorded)
- Exception handling (temporary deviations from a governing policy, and how they are tracked)
- Evidence review and retention (operationalizing the retention gap named in
  `../03-metrics-and-compliance/evidence/README.md#open-gaps`)
- Capability lifecycle management (Discover → Register → Classify → Govern → Deploy → Observe →
  Retire, per `../02-reference/architecture/README.md`)
- Governance incident handling (the human process around the events described in
  `../02-reference/components/event-handling-and-response.md`)

## Planned reference scenarios

Two candidate end-to-end scenarios have been proposed as the basis for future how-to content, both
recommended together as complementary rather than competing:

| Scenario | Description | Why it matters |
|---|---|---|
| `agentgateway` + Cedarling + OpenSearch/OpenTelemetry | An MCP proxy (`agentgateway`) fronting a Cedarling PDP, observed over OpenTelemetry into OpenSearch. | The AI-agent capability story — demonstrates capability observability both with and without a PDP present. |
| GitHub Actions self-governance | Governing the GovOps working group's own GitHub repository and its Actions via a PDP and the group's own policies (proposed as a dogfooding exercise). | Weaker as an AI-agent story, but produces a continuous stream of real events — needed to actually test metrics such as [Denial Ratio Trend](../03-metrics-and-compliance/governance-metrics/denial-ratio-trend.md) against live data, and doubles as a credibility demonstration. |

Sequencing between the two has not been decided — see
`../07-project-and-roadmap/README.md#open-questions`.

## Current status

No guide in this section has been drafted. This page exists to record what is planned and to keep
the roadmap for this section in one place, consistent with how other not-yet-written sections in
this documentation set are handled (see `../05-tutorials/README.md`).
