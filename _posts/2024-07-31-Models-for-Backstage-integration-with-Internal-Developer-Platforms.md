---
author: [David Gordon](https://github.com/davgordo)
---

# Models for Backstage integration with Internal Developer Platforms

In my experience working on Backstage-based Internal Developer Portal pilot and adoption initiatives, I have observed two fundamental contrasting patterns for Backstage integration with the underlying developer platform. Especially within the context of a Kubernetes-based platform for container infrastructure, one specific integration pattern seems to offer a higher return on investment. This article will explore the contrasting integration model, how they are different, and the potential implications for ongoing developer portal operations and the developer platform itself.

The right integration model for a given developer portal use case is always contextual. The context is often heavily influenced by the underlying developer platform itself, especially its interfaces for management activities including onboarding and cataloging tenant entities. GitOps is not always the right pattern for all use cases, however I think it is the right choice for the Contract-First IDP reference architecture, and the discussions in this article will attempt to support the incorporation of GitOps as the primary integration pattern between Backstage (Red Hat Developer Hub in this context) and the reference internal developer platform (OpenShift-based in this context) as an early architectural decision.

## Synchronous platform interaction via APIs

Before discussing the GitOps-oriented integration pattern for which I will advocate, we can examine the more common and arguably more intuitive integration pattern in order to provide a standard alternative approach for comparison. This paradigm for Backstage integration usually relies heavily on custom Backstage plugin development. Typically targeting RESTful endpoints, plugins are developed to introduce new capabilities such as scaffolder actions, each designed to interact with a particular platform component's native API and/or orchestrate several of them.

![Backstage with synchronous API interactions](/images/backstage-synchronous-api.png?raw=true "Backstage with synchronous API interactions")

In this model, when users interact with the portal, e.g. run scaffolder actions, platform management commands are issued in real-time, typically with synchronous API calls to platform component APIs. (A platform component could be any tool used in platform operations such as a certificate issuer, a monitoring service, an artifact repository, etc.) This approach is intuitive and aligns with the notion of Backstage plugins. In theory, the integration model enables developers to execute orchestrations of platform management actions, which in many cases could be useful for developer operations. As long as a given platform component exposes a management API, some integration is likely possible with custom plugin development, even unique, possibly "home-grown" platform components.

However, I caution against too much plugin-oriented portal customization, especially for the purposes of automating platform operations because this risks positioning the developer portal as a critical process orchestration component of the overall developer platform architecture. Custom Backstage plugin development may be useful for some organizations, but we propose that designing a significant portion of platform and developer operations automation as an extension of the developer portal is not desirable for most enterprises given:

- cost of implementing custom plugins
- cost of maintaining plugin lifecycle
- cost of integrating plugins with new Backstage releases
- constraints of the Backstage runtime for developer operations and process orchestration
- automation platform performance and scaling considerations

In our experience, while this Backstage platform integration approach is functionally viable and can be successful, it is typically an expensive approach, requiring substantial initial investment in plugin development as well as long term commitment to plugin maintenance. Depending on the degree and complexity of plugin-based Backstage customization, at least one developer with a strong JavaScript development background may be required to engage nearly full-time with management of custom plugin lifecycles and continuous integration with new Backstage releases. Additionally, Backstage template developers often find Backstage's template action orchestration is too simplistic to support composing operational processes, sometimes pushing orchestration complexity to the plugin layer or often to a custom platform services orchestration layer outside of the Backstage runtime which itself implies upfront and ongoing investment.

## Declarative platform configuration scaffolding

There is another, often overlooked, integration approach that can expose a rich set of platform capabilities to developers with just a few standard Backstage plugins. In this approach, the key Backstage integration point with the underlying platform is the Git provider. 

In a GitOps context, Backstage's ability to publish scaffolded content to Git repositories potentially exposes any given platform capability to developers, especially when the target platform is Kubernetes-based. In turn, a Git-oriented Backstage entity discovery strategy is an intuitive fit. In a GitOps context, nothing can be a better source of truth for entity inventory than the enterprise Git provider.

![Backstage with GitOps](/images/backstage-gitops.png?raw=true "Backstage with GitOps")

In my opinion, Kubernetes is a more accommodating environment than Backstage for platform and developer automation for at least the following reasons:

- supports polyglot automation implementations
- designed for scaling processes across infrastructure
- facilitates strong isolation boundaries between processes
- significant adoption of the operator model by vendors

Pushing developer operations and automation concerns from Backstage to Kubernetes moves the problem to a platform layer with much more capability and flexibility for operations orchestration.

Additionally, Quality control and approval workflows for platform changes can be a challenge when a developer portal exposes a rich set of capabilities to developers. In a GitOps context, platform changes are usually introduced with a Git merge from a fork or branch. Assuming the Git-provider is an enterprise-grade solution, it is almost always possible to configure approval workflows and potentially integrate quality gates in order to enforce policy and ensure quality for certain types of platform changes.

Lastly, a GitOps-oriented Backstage integration approach could create new possibilities for safe management of sensitive environments with restrictive network security standards. Assuming the GitOps reconciler is local to the secure environment, the only cross-connection required between the developer environment and the secure production environment is an outbound connection from the secure environment to the Git provider. In comparison to the contrasting plugin-oriented Backstage integration paradigm, a GitOps approach could be considered more secure since the secure environment does not need to expose any APIs directly to the developer portal network. At the very least the simplified network integration requirements will almost certainly save a few firewall change requests.

## Conclusion

While Backstage plugins are potentially useful for read-only platform introspection use cases, we will not focus on Backstage plugins as our primary platform operations orchestration tool. Instead, we will prioritize adoption of platform components which are managed by mature Kubernetes operators. This means, we will use Backstage template actions mainly for scaffolding GitOps manifests to be reconciled by various controllers, but usually ArgoCD. This will take pressure off of plugin development and maintenance and help standardize a workflow for quality control and approvals.