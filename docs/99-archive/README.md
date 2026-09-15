---
title: "Archive"
status: current
document_type: reference
source_of_truth: true
normativity: informative
owner: TBD
last_reviewed: "2026-09-15"
tags: [govops, archive]
related: []
---

# Archive

This section holds superseded material. Nothing has been superseded yet, so it is currently empty.

## Policy

When a document in this repository is superseded — an ADR revisited, a section rewritten in a way
that discards rather than extends its predecessor — the superseded version moves here rather than
being deleted, with:

- `status: superseded` in its front matter, and
- a `superseded_by` field pointing to the document that replaced it.

The document that replaces it should carry a `supersedes` field pointing back, per the front-matter
conventions in `../_templates/README.md`.
