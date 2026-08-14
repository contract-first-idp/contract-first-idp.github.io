---
author: David Gordon
github_username: davgordo
title: "Backstage Systems-as-Code"
date: 2024-09-19
updated: 2026-08-13
description: "Why Git-backed Backstage Systems make a useful technical source of truth for composition, ownership, and environment lifecycle."
---

<aside class="article-note"><strong>2026 update:</strong> The 1.0 catalog model extends this idea upward and downward. A Domain now establishes tenant ownership, SCM context, and environment policy; Systems own APIs, Components, and Resources as peers. The System repository remains the key unit of reviewable application desired state.</aside>

The Contract-First IDP reference architecture manages Backstage System entities as code in Git repositories. System entity definitions are scaffolded and published to Git repositories and the catalog of Systems is, in turn, discovered from the Git provider.

This pattern is common for Components because Components naturally relate to source code. Organizations sometimes hesitate to apply the same approach to Systems because a separate enterprise application inventory or CMDB may already exist. This article explains why a Git-backed technical model can still be the better source of truth for the developer experience.

## Pitfall: prioritizing the business system of record

Enterprise software asset management is important. It often connects application inventory with finance, compliance, ownership, and planning. That does not automatically make the same system the most useful technical source of truth for a developer portal.

From a developer's perspective, a software system is more than a database record. It is an application boundary with source repositories, deployable Components, APIs, Resources, environments, and operational state. In a GitOps-driven environment, Git is often the place where those relationships already become concrete.

That does not prevent the portal from validating or linking to an enterprise asset system. A scaffolder form can validate a required asset identifier, and a pull request can be blocked when required governance metadata is absent. The business inventory and the technical manifest can remain connected without forcing one to replace the other.

## Alignment with GitOps

In a GitOps-driven environment, composition is a natural way to represent relationships between Domains, Systems, APIs, Components, and Resources.

![GitOps Entity Hierarchy](/images/gitops-hierarchy.png?raw=true "GitOps Entity Hierarchy")

A System may not be deployable by itself, but it still benefits from a manifest because it provides the durable composition point between higher-level tenancy and lower-level application concerns.

Systems also align well with namespace and environment lifecycle. Namespace policy, shared configuration, and application-level desired state should not need to be recreated by every Component that happens to share the same System.

## The 1.0 catalog model

Contract-First IDP 1.0 makes the hierarchy explicit:

```text
Domain
  -> System
      -> API
      -> Component
      -> Resource
```

The Domain is the tenant and lifecycle authority. It defines ownership, SCM context, and the ordered environment lifecycle. A System groups an application capability inside that Domain.

APIs, Components, and Resources are peers beneath the System:

- **APIs** own communication contracts independently from implementations.
- **Components** provide or consume APIs and produce deployable artifacts.
- **Resources** represent managed dependencies such as databases.

The System repository stores the narrow desired-state files that activate these child entities and select Component releases for environments. That makes lifecycle changes small enough to review directly in Git.

## Systems as stable application contracts

The important property of Systems-as-code is not simply that `catalog-info.yaml` lives in a repository. The repository becomes a stable application-level contract.

For example, Component promotion is represented by a small release-selection file rather than a deployment pipeline mutating the target cluster. A release file answers one question: which immutable artifact should run in this environment. Runtime configuration remains separate.

This keeps the application model understandable to developers and reviewers while leaving the implementation of namespaces, Pipelines, ImageStreams, Deployments, and operators to the platform-owned charts.

## Conclusion

Contract-First IDP continues to manage Systems as code because the pattern is developer-centric, GitOps-oriented, and loosely coupled from external systems of record.

The 1.0 architecture strengthens that decision: the System repository is now the durable boundary where APIs, Components, Resources, activation, and release selection converge into reviewable application intent.
