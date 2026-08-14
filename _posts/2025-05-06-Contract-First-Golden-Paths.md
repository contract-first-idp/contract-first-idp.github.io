---
title: "Golden Paths for Contract-First Development"
date: 2025-05-06
updated: 2026-08-13
description: "How Contract-First IDP turns Backstage catalog relationships into an end-to-end developer workflow where APIs have independent lifecycles and implementations start from selected contracts."
---

A core responsibility of an Internal Developer Platform is to create safe, standardized, repeatable paths that developers can follow without needing to understand every implementation detail beneath the platform.

In Contract-First IDP, those paths are organized around one premise: **the interface contract can exist, evolve, and be useful before a provider implementation is complete.**

The first version of this article described that idea as a target. Contract-First IDP 1.0 turns it into a working catalog and GitOps lifecycle.

## The golden-path sequence

The 1.0 catalog model is intentionally simple:

```text
Domain
  -> System
      -> API
      -> Component
      -> Resource
```

For a new tenant, the primary golden paths normally run in this order:

1. **Create Tenant Domain** — establishes the tenant repository, ownership model, SCM context, and ordered environment lifecycle.
2. **System Golden Path** — creates a System repository and activates it in the build environment.
3. **OpenAPI Specification Golden Path** — creates an independent API contract repository and wires Registry publication.
4. **Component Golden Path** — creates a working implementation repository that provides and consumes selected contracts.
5. **Resource Golden Path** — attaches managed dependencies through the same reviewable Git model.

Activation and promotion paths repeat as the application moves through its lifecycle.

## APIs have their own lifecycle

The API repository owns `specification.yaml`. A Component can provide the API, consume it, or both, but the contract does not belong to either implementation.

That independence is what makes parallel development possible.

On `main`, the API publication pipeline validates the specification with Spectral and publishes an Apicurio Registry version identified by the exact Git SHA. A human Git tag such as `v2.1.0` creates an additional immutable human-readable Registry version for the same commit.

Git remains the authoritative source, while the Registry provides a stable distribution and retrieval interface for implementation tooling.

## Virtualization keeps the contract useful immediately

A contract should become useful before a production provider exists. Microcks is part of the platform precisely for that stage of the lifecycle: published API contracts can be virtualized so consumers can develop and exercise integrations against a mock endpoint while provider implementation continues independently.

That preserves the core idea represented by the original API lifecycle graphic: contract, consumer, and producer work are related, but they do not need to move in lockstep.

![Contract-first API lifecycle](/images/api-lifecycle.png)

## Components select contracts, not repositories

The Component Golden Path asks developers which API the Component provides and which APIs it consumes. Those selections are recorded as explicit contract versions.

Generated implementations retrieve the chosen contracts from Apicurio rather than assuming direct knowledge of another team's source repository.

Contract consumption works across Systems. Runtime endpoint resolution remains environment-owned configuration, so the contract identity and the network location of a deployed provider are separate concerns.

## Implementation profiles share one platform contract

Contract-First IDP 1.0 supports several implementation profiles, including:

- Quarkus Camel OpenAPI with Java DSL
- Quarkus Camel OpenAPI with YAML DSL
- Spring Boot Camel OpenAPI with Java DSL
- Spring Boot OpenAPI
- Node.js OpenAPI

The important part is not the list of frameworks. Each profile implements the same platform-facing contract.

Java profiles use the Apicurio Maven integration to retrieve selected contracts. Camel profiles can scaffold API-qualified routes and mock missing provider operations while implementation is incomplete.

The Node.js profile retrieves contracts into the generated repository, uses OpenAPI Generator for TypeScript server and client types, uses `openapi-backend` for provider routing and request/response validation, and uses `openapi-client-axios` for typed consumer clients. Each consumed API gets its own runtime URL override.

A developer therefore starts with a repository that already understands the interfaces it provides and consumes.

## Git is the durable handoff

Backstage captures intent, but the portal does not own the ongoing lifecycle.

`software-templates` creates repositories and reviewable desired state. Tenant Git becomes the stable interface. `developer-charts` interprets that state using trusted platform implementations. Argo CD continuously reconciles the result.

That separation lets the golden-path experience evolve independently from runtime implementation details as long as the Git contract remains compatible.

## Release and promotion complete the path

Golden paths are not complete when a repository is scaffolded.

A Component main commit is built once into an immutable commit-addressed image. A human Git tag such as `v1.7.3` aliases that same digest rather than rebuilding source. Promotion is a separate Git operation that selects the human release for the next environment and copies the same immutable digest into the target environment registry repository.

That distinction matters:

- source changes create artifacts
- release tags name already-built artifacts
- promotion changes environment desired state
- Argo CD enforces that desired state

The same declarative model therefore reaches from initial scaffold through runtime promotion.

## Realizing the benefits

Contract-first golden paths create the most value when contracts are treated as durable, independent products rather than files generated from implementation code.

Once that boundary exists, the platform can automate around it: validation, publication, virtualization, client generation, provider routing, compatibility policy, catalog relationships, and release reproducibility all become easier to reason about.

The goal is not merely to generate more code. It is to give each team a stable interface around which autonomous work can proceed.
