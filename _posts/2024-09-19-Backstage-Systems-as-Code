---
author: David Gordon
github_username: davgordo
---

# Backstage Systems-as-Code

The Contract-first IDP reference architecture manages Backstage System entities as code in Git repositories. System entity definitions are scaffolded and published to Git repositories and the catalog of Systems is, in turn, discovered from the Git provider. This pattern is quite common for managing Backstage Components, because of the core Backstage entities, Components tend to have the closest relationship to source code managment. In contrast, organizations often choose to explore Backstage System catalog integration with a source-of-truth other than Git. This article discusses some reasons why Systems-as-code may be a better choice, even if your organization is committed to using a specific product as the system of record for Enterprise applications.

Developer portals exist first and foremost for developers. Presumably, the rise in popularity of Backstage has something to do with a lack of attention in the software industry as a whole on the developer experience, but all-too-often the developer perspective is still sidelined in the design phase of a portal. Developer portal maintainers usually work on a platform team and are not usually the intended users of the portal. Without thorough integration of the developer perspective throughout the design and planning processes in a portal pilot initiative, the resulting portal experience can fall short, leading platform teams to scramble for developer feedback explaining the hesitation to adopt.

## Pitfall: Prioritizing Business Stakeholders

Executive-level support for internal software initiatives is usually quite important. Building a self-service portal that exposes complex developer operations intuitively is no small investment. Funding needs to be secured and contiuously justified. For this reason, platform teams naturally gravitate to business stakeholder concerns.

For example, businesses tend to link Enterprise software asset management with financial planning and tracking as well as human resource allocation and other business logistics workflows. In order to manage all of this, IT organizations often invest in expensive software products. So, the business's source-of-truth for "software systems" is already established -- it should be the universal source of truth, right?

### Revenge of the Nerds

If you're a developer, you've probably been asked the question "Does it have a valid [CMDB] ID?". This is more like getting pulled over than it is like getting a birthday card. Developers don't spend time browsing traditional Enterprise asset inventory systems with glee. Attaining an asset ID is more or less the only related developer concern, and it's like getting driver's license -- you're just in trouble if you're driving without one.

From a developer's perspective, a software system doesn't live as a record in some SQL table, a software system is real deployment of some application to one or more environments. Developers are concerned with the technical asset, not the business asset. In this world, the source-of-truth is often Git, especially in a GitOps-driven environment where there is a direct correlation between Git locations and deployed systems.

### Kumbaya

Just because the traditional enterprise asset management system isn't the source-of-truth for Backstage doesn't mean it can't be integrated with the developer experience.

For example, it is generally a safe practice to configure Backstage scaffolder templates to make Git pull requests, rather than make direct changes to main branches. Pull request events are usually an opportunity to incorporate automated validations and quality gates. Using this approach, policy can be introduced to the platform preventing systems with an invalid or missing Enterprise asset management ID from being merged into the live manifest. In this scenario, the enterprise asset management system isn't the source-of-truth for Backstage Systems, but every System is linked to a tracked asset.

Alternatively, asset validation can be introduced to Backstage scaffolder template forms. So even before a template is executed, the developer-provided asset ID can be validated. This can be accomplshed by defining a validator for a [custom field extension](https://backstage.io/docs/features/software-templates/writing-custom-field-extensions/#creating-a-field-extension).

```typescript
export const validateAssetValidation = (
  value: string,
  validation: FieldValidation,
) => {
  const isValid = // match record in external system

  if (isValid === false) {
    validation.addError(
      `Asset ID must exist in the corporate system of record.`,
    );
  }
};
```

The field extension with custom validation can be referenced from any template.

```yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: Test template
  title: Test template with custom extension
spec:
  parameters:
    - title: Asset Validation
      required:
        - asset_id
      properties:
        asset_id:
          title: Asset ID
          type: string
          ui:field: ValidateAsset
  steps:
  [...]
```

These options promote looser coupling between the developer portal and the Enterprise asset management system, potentially mitigating vendor lockin concerns. Developers can also focus on the Systems they care to onboard to the portal, and avoid potential catalog clutter resulting from bulk discovery.

## Alignment with GitOps

 In a GitOps-driven environment, composition is a common technique for representing tiered relationship between domains, systems, and components. There are several techniques for achieving a composition pattern with a GitOps scheme including ArgoCD “app of apps”, Helm umbrella charts, and Kustomize external resource references.

 So, a System may not be a deployable asset in and of itself, it may be only a collection of references to sub-entities (Components, APIs, and Resources), but it still requires a Git manifest if for no other reason than to provide the link between the top-level GitOps manifest and each of its sub-entities.

 ![GitOps Entity Hierarchy](/images/gitops-hierarchy.png?raw=true "GitOps Entity Hierarchy")

 But Systems are often more than just a link. In the context of Kubernetes, systems align well with the concept of namespaces. Namespaces are contain other resources and are used to establish tenant boundaries. Additionally platform teams may want to instrument each platform tenant namespace with a standard set of resources and configurations, for example, perhaps container registry pull secrets or RBAC configuration. The System lifecycle maps nicely to namespace management. Namespace management built into the Component lifecycle tends to be awkward when more than one Component shares a namespace, because in this case, namespace provisioning should only needs to happen before the first component is introduced.

 ## Conclusion

 Contract-First IDP is influenced by an architectural decision to manage Backstage Systems-as-code. We are committed to this concept in order to be developer-centric, GitOps-oriented, and loosely coupled with external system. We also benefit from full control of System entity metadata and entity specification. This does not prevent us from potentially enforcing policy to ensure that Backstage Systems align with an Enterprise asset management tool.