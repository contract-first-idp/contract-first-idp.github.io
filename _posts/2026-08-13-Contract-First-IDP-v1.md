---
title: "Contract-First IDP v1.0.0: Ready to Build On"
date: 2026-08-13
description: "v1.0.0 marks the point where Contract-First IDP becomes a tested, versioned reference architecture with clearer boundaries for users and contributors."
tags: Release
---

The core ideas behind Contract-First IDP have been consistent for some time: keep Backstage focused on developer intent, use Git as the handoff to the platform, model Systems as code, and give API contracts a lifecycle independent from their implementations.

v1.0.0 is less about introducing a new architecture than about making the existing one dependable.

The main paths have now been exercised end to end on a live OpenShift cluster. The reference artifacts are explicitly versioned. Repository boundaries are clearer. Documentation is organized around ownership and sources of truth. Contributors can work on one part of the architecture without needing to understand or modify the entire stack.

That is what makes this release significant.

## The original idea still holds

The original API lifecycle graphic remains the best summary of the project:

![Original contract-first API lifecycle](/images/api-lifecycle.png)

The contract sits between producers and consumers so that each can move independently.

In v1.0.0, that principle is supported by a complete platform flow. An API can be published before its provider exists. Microcks can virtualize it. Components can retrieve exact versions from Apicurio Registry and generate against them. Providers and consumers can evolve independently as long as they remain compatible with the contract.

The architecture did not need to change. The surrounding platform needed to mature.

## Tested through the actual platform lifecycle

A scaffolder template can render correctly and still fail as a developer experience.

For v1, the important paths were tested through the real platform: install the platform, create a Domain, create and activate a System, publish an API, build a Component, provision a Resource, release an artifact, and promote it.

Those paths cross Developer Hub, GitHub, Argo CD, OpenShift Pipelines, Quay, Apicurio Registry, Microcks, External Secrets, generated repositories, and the resulting OpenShift resources.

Live testing exposed issues that isolated template tests do not: reconciliation timing, ownership boundaries, catalog relationships, generated Git shape, release identity, and developer workflow friction.

The goal is not to simulate every platform product in a large test suite. It is to verify that the supported paths work as connected experiences.

## A versioned reference architecture

Contract-First IDP is published through three complementary reference artifacts:

- `software-templates`: developer experience reference
- `developer-charts`: runtime implementation reference
- `platform-components`: platform installation reference

Each begins its v1 lifecycle at `1.0.0`.

The repositories are independently versioned because they represent distinct architectural concerns. A platform target can record the exact versions it was tested with. Compatible changes can evolve independently within a major version, while breaking changes have an explicit place to surface.

This removes much of the ambiguity of treating the project as whatever happens to be on `main`.

## Clearer Git contracts

The handoffs between layers have also become smaller and more deliberate.

Backstage writes developer intent to Git. Argo CD combines that tenant-owned state with platform-owned implementation. Charts render resources. Controllers reconcile them.

Tenant repositories therefore carry intent, not runtime implementation details.

A System repository expresses environment state. An API repository owns its OpenAPI document. A Component repository owns source. Promotion can be represented by selecting an immutable release for an environment.

These Git shapes are effectively interfaces between independently maintained parts of the architecture. Keeping them small improves both operability and contributor experience.

## Easier to extend

The repository boundaries now provide clearer places for contributors to work.

Changes to developer-facing golden paths belong in `software-templates`. Changes to how supported intent becomes OpenShift resources belong in `developer-charts`. Changes to the shared platform installation belong in `platform-components`.

The Node.js OpenAPI Component profile is a useful example.

It uses a different implementation stack from the Java profiles, including Node 24, `openapi-backend`, `openapi-client-axios`, and OpenAPI generation tooling. But it still consumes the same API relationships, retrieves the same contracts, and participates in the same build, release, and promotion model.

Adding a new implementation profile did not require a new platform architecture.

That is the extension model v1 is intended to support.

## Golden paths now cover more of the lifecycle

The catalog hierarchy remains straightforward:

```text
Domain
  -> System
      -> API
      -> Component
      -> Resource
```

APIs, Components, and Resources are peers beneath a System, with independent lifecycles.

The golden paths now extend beyond entity creation. Systems can be activated into environments. API contracts are published and versioned. Components are built, released, and promoted. Resources participate in the same System and environment model.

API commits are published to Apicurio Registry under the exact Git SHA, while human Git tags can add immutable release names for the same contract revision.

Component commits are built once. A human release tag names the image already produced from that commit. Promotion selects that released artifact for another environment rather than rebuilding it.

This makes source identity, release identity, and deployment intent explicit and testable.

## A cleaner platform boundary

`platform-components` still provides an opinionated OpenShift reference installation so the architecture can be run end to end.

At the same time, platform-specific coordinates and credentials are kept outside tenant-owned Git. The platform target records the exact reference versions and runtime coordinates selected by an installation, while credentials remain cluster-owned.

This keeps the reference concrete enough to install and test without making the tenant contract dependent on one specific platform implementation.

## What v1.0.0 means

v1.0.0 establishes a stable baseline for the project.

The supported paths have been exercised end to end. The reference artifacts have explicit versions. The interfaces between layers are clearer. New implementation profiles and platform capabilities have defined places to fit.

The architecture is not finished, nor should it be.

It is now ready to build on.