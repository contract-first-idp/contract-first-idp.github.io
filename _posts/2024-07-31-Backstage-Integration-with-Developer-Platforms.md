---
author: David Gordon
github_username: davgordo
title: "Backstage Integration with Developer Platforms"
date: 2024-07-31
updated: 2026-08-13
description: "Why Contract-First IDP uses Backstage to capture intent in Git instead of turning the portal into a platform orchestration engine."
---

<aside class="article-note"><strong>2026 update:</strong> This architectural choice became one of the defining boundaries in Contract-First IDP 1.0. Backstage golden paths create repositories and reviewable Git changes; Argo CD, Tekton, registries, and operators reconcile asynchronously afterward. The original argument below is preserved because the implementation ended up reinforcing it.</aside>

In my experience working on Backstage-based Internal Developer Portal pilot and adoption initiatives, I have observed two fundamental contrasting patterns for Backstage integration with the underlying developer platform. Especially within the context of a Kubernetes-based platform for container infrastructure, one specific integration pattern seems to offer a higher return on investment. This article explores the contrasting integration models and why Contract-First IDP adopted GitOps as its primary integration pattern between Backstage, Red Hat Developer Hub in this context, and an OpenShift-based developer platform.

## Synchronous platform interaction via APIs

The intuitive integration model relies heavily on custom Backstage plugin development. Typically targeting RESTful endpoints, plugins introduce capabilities such as scaffolder actions designed to interact directly with a platform component's API or orchestrate several APIs together.

![Backstage with synchronous API interactions](/images/backstage-synchronous-api.png?raw=true "Backstage with synchronous API interactions")

In this model, user interaction with the portal causes platform management commands to be issued in real time. A platform component could be a certificate issuer, monitoring service, artifact repository, or any other system with a management API.

The model is functionally viable, but heavy portal customization can position the developer portal as a critical process-orchestration component of the platform. That introduces several costs:

- implementing custom plugins
- maintaining plugin lifecycles
- continuously integrating custom code with new Backstage releases
- accepting the Backstage runtime as an operations-orchestration environment
- scaling operational automation inside the portal tier

Custom plugins can still be useful, especially for focused user experiences and read-only introspection. The concern is making the portal responsible for the long-running, privileged work of changing platform state.

## Declarative platform configuration scaffolding

A different integration model exposes a rich set of platform capabilities through a much narrower Backstage surface. The key integration point is the Git provider.

In a GitOps environment, Backstage's ability to publish scaffolded content and open pull requests can expose platform capabilities without requiring a custom action for each underlying controller. Git becomes the durable handoff between developer intent and platform implementation.

![Backstage with GitOps](/images/backstage-gitops.png?raw=true "Backstage with GitOps")

Kubernetes is also a more accommodating environment than Backstage for operational automation because it:

- supports polyglot automation implementations
- is designed to scale processes across infrastructure
- provides strong isolation boundaries between processes
- has broad adoption of the controller and operator model

Pushing operational concerns from Backstage into Kubernetes moves the work to a layer designed to reconcile state continuously.

Git also creates a natural review boundary. Platform changes can be introduced through pull requests with approval workflows, automated validation, and policy checks before they become desired state.

A GitOps-oriented integration model can also simplify secure-environment connectivity. When the reconciler is local to a restricted environment, the environment needs outbound access to the Git provider rather than exposing a broad set of management APIs directly to the developer portal network.

## What this became in Contract-First IDP 1.0

The 1.0 architecture makes the handoff explicit:

1. `software-templates` captures developer intent and creates Git state.
2. Tenant repositories persist the stable contract between developer experience and platform implementation.
3. `developer-charts` provides trusted implementations of that supported intent.
4. Argo CD combines tenant state with those implementations and reconciles OpenShift resources.

A successful Backstage task therefore means the requested repository or pull request exists. It does not mean every downstream controller has finished. Reconciliation can continue, retry, and recover after the scaffolder task has ended.

That boundary also keeps Backstage out of the cluster. Repository credentials are sufficient for the primary golden-path workflow; cluster credentials remain with the platform and its controllers.

## Conclusion

Contract-First IDP does not use Backstage as its primary platform-operations orchestration engine. Backstage is the developer-facing interface for capturing intent. Git stores that intent. Kubernetes controllers perform the operational work.

The result is a smaller portal surface, a durable audit trail, and a platform architecture whose operational behavior can evolve independently from the developer experience as long as the Git contract between them remains compatible.
