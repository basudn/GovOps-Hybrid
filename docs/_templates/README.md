---
title: Templates
status: draft
document_type: overview
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-12"
tags: [govops, templates]
related: []
---

# Templates

Reusable document templates, and the front-matter metadata standard used across this documentation set.

## Front-matter metadata standard

```yaml
---
title: <Human-readable title>
status: current            # draft | proposed | current | deprecated | superseded | planned
document_type: explanation # reference | component-reference | interface | metric-reference | how-to | tutorial | ADR | overview | roadmap
source_of_truth: false     # whether this is canonical for a fact or contract
normativity: informative   # normative | informative | non-normative
owner: <team, working group, or TBD>
last_reviewed: YYYY-MM-DD
tags: [...]
related: [<relative links>]
supersedes: []
superseded_by: []
---
```

| Field | Meaning |
|---|---|
| `status` | Lifecycle state of the document |
| `document_type` | How the reader should use it |
| `source_of_truth` | Whether this is canonical for a fact or contract |
| `normativity` | Whether it describes, recommends, or requires behavior |
| `owner` | Accountable maintainer or working group |
| `last_reviewed` | Last substantive confirmation of accuracy |
| `related` | Links to dependent or explanatory documents |
| `superseded_by` | Prevents stale guidance from being treated as current |

## Templates

- [Component Template](component-template.md)
- [Metric Template](metric-template.md)
- [ADR Template](adr-template.md)
