---
title: "Component: Capability Catalog"
audience: technical
status: draft
summary: "Describes the Authorization Capability Catalog (ACC), its purpose, and its schema."
related_files:
  - /spec/acc-schema.yaml
llm_focus: "This document describes a specific technical component. Focus on the definition, structure, and technical implementation details, especially its relationship to the Jamara and OSCAL standards."
---
# Authorization Capability Catalog (ACC)

The Authorization Capability Catalog (ACC) is a central component of the GovOps framework. It provides a standardized way to define and manage capabilities within an organization.

## Capabilities

A capability is defined as a unique **action-resource pair**. It represents a specific operation that can be performed on a resource.

## The Catalog

The ACC is a catalog of these capabilities. Each entry in the catalog is identified by a unique capability ID and contains metadata about the capability, such as:

- Risk level
- Business impact
- Geographic region
- Data type

## Schema and Format

The ACC uses a YAML-based format, compatible with the [Jamara](https://github.com/ossf/jamara) standard from the OpenSSF. This allows for integration with compliance tools that support the Open Security Controls Assessment Language (OSCAL).

The schema for the ACC is defined in `/spec/acc-schema.yaml`.

## Automated Generation

Given the potential scale of capabilities in a large organization, the ACC is designed to be generated automatically, for example, during the policy authoring process.