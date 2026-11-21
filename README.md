# Standardized Azure Landing Zone (AdventureWorks Template)

*Status: November 2026*

Modern cloud adoption strategies use an Azure landing zone (ALZ) as the foundation for scalable and secure deployments. In the current Cloud Adoption Framework guidance, an Azure landing zone consists of a platform landing zone, which provides governance, security and shared services centrally, and workload landing zones, in which workload teams deploy and operate their resources within the platform's guardrails. The AdventureWorks template described in this document is the hub-and-spoke variant of the Microsoft platform landing zone. Microsoft originally published it as the AdventureWorks reference implementation; today the same architecture is delivered through the Azure landing zone accelerators (portal accelerator, and Bicep or Terraform based on Azure Verified Modules) together with the Azure landing zone library. The following sections highlight the principal advantages of this standardized template compared to a fully customized landing zone approach.

![Key benefits of the standardized landing zone](graphics/Standardized%20Azure%20Landing%20Zone%20-%20Figure%201%20-%20Key%20Benefits.png)

Figure 1: Key benefits of the standardized landing zone

## Standardized vs. Customized Landing Zones

An Azure landing zone is a proven and flexible architecture for governing, securing and scaling a multi-subscription Azure environment. It addresses scalability, security, governance, networking and identity, so that workload teams can deploy efficiently while staying aligned with enterprise compliance standards. Microsoft recommends that most organizations operate a single platform landing zone per Microsoft Entra tenant and distribute workload landing zones to workload teams through a repeatable, ideally automated, subscription vending process.

In a standardized landing zone based on the AdventureWorks template, Microsoft's reference architecture is implemented as provided or with minor adjustments through the accelerator's configuration files and library overrides. The design follows the five Azure landing zone design principles: subscription democratization, policy-driven governance, single control and management plane, application-centric service model, and alignment with Azure-native design and roadmaps. In practice, this enables scalable deployment across many subscriptions, extensive use of Azure Policy for governance, centralized management, adaptability to diverse workloads including AI, and consistent alignment with Azure's evolving feature set.

![Azure landing zone architecture of the AdventureWorks template](graphics/Standardized%20Azure%20Landing%20Zone%20-%20Figure%202%20-%20Landing%20Zone%20Architecture.png)

Figure 2: Azure landing zone architecture of the AdventureWorks template

Further reading: [What is an Azure landing zone? (Cloud Adoption Framework)](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/)

By contrast, a custom landing zone is one that an organization designs and builds itself, choosing its own policies, architecture and configurations. Customization allows tailoring to specific needs, but it forgoes the continuously maintained Microsoft baseline. The following comparison summarizes the differences:

### Comparison at a Glance

**Governance and Policy**

- *Standardized landing zone:* Policy-driven guardrails out of the box. The ALZ library assigns built-in and custom Azure Policy definitions and initiatives with Deny, DeployIfNotExists and Modify effects at each management group scope. When a subscription is placed under the Landing zones management group, policies automatically configure Microsoft Defender for Cloud, diagnostic settings and monitoring. Assignments are pinned to validated policy versions, so updates are adopted deliberately.
- *Customized landing zone:* Policies and initiatives must be selected, written, tested and versioned by the cloud team. Coverage depends on internal expertise, and reaching comparable breadth (well over a hundred definitions and initiatives) takes significant effort.

**Best Practice Guardrails**

- *Standardized landing zone:* Secure-by-default guardrails for networking, identity, data and AI services, for example denying subnets without NSGs, denying management ports from the internet (including 0.0.0.0/0 sources), enforcing TLS and private endpoints for PaaS services, Entra-only authentication for SQL and auditing deprecated configurations such as AKS kubenet.
- *Customized landing zone:* Guardrails are defined from scratch. Without a maintained baseline, some resource types remain ungoverned, and configurations drift from best practice as Azure evolves.

**Deployment and Time-to-Value**

- *Standardized landing zone:* Deployed with the ALZ IaC accelerator (Bicep or Terraform, both built on Azure Verified Modules, with CI/CD pipelines in GitHub or Azure DevOps) or with the portal accelerator. A guided PowerShell experience generates the configuration, and the baseline can run within days. Cost-optimized starting scenarios exist for smaller organizations.
- *Customized landing zone:* Network topology, identity integration, management and governance are designed, coded and tested in-house, which typically takes weeks to months and delays dependent projects.

**Maintenance and Updates**

- *Standardized landing zone:* Maintained by Microsoft as an open-source product with a public What's new log, accelerator release notes and versioned ALZ library releases. Policy refreshes, bug fixes, new regions and service changes are delivered as upgradeable releases with upgrade guidance.
- *Customized landing zone:* Every change in Azure (new services, deprecations, retirements) must be tracked and implemented by the internal team, so the environment tends to lag behind current recommendations.

**Organizational Model**

- *Standardized landing zone:* A proven management group hierarchy (Platform with Security, Management, Identity and Connectivity; Landing zones with Corp, Online and optionally Local; Sandbox; Decommissioned) and dedicated platform subscriptions create clear responsibility boundaries between platform and workload teams.
- *Customized landing zone:* Structures are invented locally, often with too few subscriptions or an unclear hierarchy, which blurs responsibilities and complicates later restructuring.

**Security and Compliance**

- *Standardized landing zone:* Microsoft Defender for Cloud plans, the Microsoft cloud security benchmark, central logging and a dedicated Security subscription for Microsoft Sentinel are part of the design, so every workload landing zone is under security oversight from day one.
- *Customized landing zone:* Defender for Cloud, logging and SIEM integration must be enabled and enforced for each subscription, and gaps are easy to miss.

**Operational Efficiency**

- *Standardized landing zone:* DeployIfNotExists policies install Azure Monitor Agent with a central managed identity and configure data collection rules, change tracking and Azure Update Manager. Azure Monitor Baseline Alerts provide ready-made alerts, and teams familiar with the Cloud Adoption Framework understand the environment immediately.
- *Customized landing zone:* Agents, diagnostics, alerting and patching are configured per subscription or with self-built automation, and documentation is internal only.

**Cost Management**

- *Standardized landing zone:* Subscriptions per workload, tag policies, audits of unused resources and shared central services support cost allocation and avoid duplicated tooling. Optional components such as DDoS Network Protection, the Azure Firewall SKU or Microsoft Sentinel are sized to requirements.
- *Customized landing zone:* Tagging, cost segmentation and budgets depend on the custom design, and costs are often harder to attribute.

The standardized AdventureWorks landing zone builds on the Microsoft platform landing zone, which consolidates the lessons learned from many enterprise deployments. Organizations get a secure, well-governed starting point in which the platform resources required by the application portfolio are already considered, so teams can deploy with confidence in a consistent environment. A fully custom approach can also succeed, but it requires replicating these capabilities through sustained internal effort.

## Current Azure Landing Zone Baseline

Microsoft continuously evolves the Azure landing zone. The following changes, released between 2024 and 2026, define the current baseline on which this document is based (status November 2026):

**Deployment tooling**

- *Current state:* ALZ IaC accelerator with Bicep (Azure Verified Modules, generally available since January 2026) or Terraform (Azure Verified Modules), plus the portal accelerator for Azure public cloud. Classic ALZ-Bicep was supported for one year after the AVM release in November 2025.
- *Implication for the AdventureWorks template:* New deployments use the AVM-based Bicep or Terraform starter modules and the CI/CD pipelines that the accelerator bootstraps in GitHub or Azure DevOps.

**ALZ library**

- *Current state:* Policy definitions, initiatives, assignments and archetypes are versioned in the Azure Landing Zones Library (for example platform/alz 2026.04.2).
- *Implication for the AdventureWorks template:* Policy updates are adopted by moving to a newer library version and reviewing the plan or what-if output.

**Management groups**

- *Current state:* Security management group and subscription under Platform (September 2025); optional Local management group under Landing zones for Azure Local workloads (April 2026).
- *Implication for the AdventureWorks template:* Microsoft Sentinel and other security tooling are placed in the dedicated Security subscription.

**Microsoft Sentinel**

- *Current state:* No longer deployed by the portal accelerator; deployed separately following the Sentinel deployment guide. Management in the Azure portal ends on 31 March 2027.
- *Implication for the AdventureWorks template:* Sentinel is onboarded to the Microsoft Defender portal from the start.

**Monitoring**

- *Current state:* Azure Monitor Agent with a central user-assigned managed identity and data collection rules replaces the retired Log Analytics agent. Automation accounts are no longer deployed by default (February 2025). Azure Monitor Baseline Alerts (AMBA) provide baseline alerting.
- *Implication for the AdventureWorks template:* Legacy agents and unused automation accounts are removed; AMBA is used for platform and workload alerts.

**Security baseline**

- *Current state:* Microsoft cloud security benchmark v2 assigned at the intermediate root alongside v1; Defender for AI workloads; Guest Attestation for Trusted Launch VMs; updated Defender for Servers policy.
- *Implication for the AdventureWorks template:* Defender plan coverage, cost and regulatory compliance reporting are reviewed against the new defaults.

**Policy hygiene**

- *Current state:* Built-in policy references pinned to validated versions; deprecated custom definitions and initiatives removed in April 2026.
- *Implication for the AdventureWorks template:* Deprecated assignments in existing environments are replaced before upgrading.

**Networking**

- *Current state:* Zone-redundant VPN gateways by default, Azure Firewall management NIC for all SKUs, Azure Bastion and Private DNS Resolver options, Azure Virtual Network Manager security admin rules (preview).
- *Implication for the AdventureWorks template:* The hub design is aligned with the current defaults.

**Terminology and ownership**

- *Current state:* CAF uses "workload landing zones" instead of "application landing zones"; the platform landing zone accelerator is owned by the Azure Migrate product team since June 2026.
- *Implication for the AdventureWorks template:* This document follows the current terminology.

Existing environments built on the earlier AdventureWorks reference implementation can adopt these changes incrementally by following the upgrade guidance of the accelerator.

## Azure Policy Best Practices and Guardrails

One of the strongest advantages of the Microsoft-provided landing zone is its policy-driven governance model. Azure Policy is used extensively to prevent configurations that violate guidelines and to configure or remediate resources automatically. In the AdventureWorks template, the policy set comes from the versioned Azure landing zone library and is assigned per management group archetype:

- **Proactive and Preventive Enforcement:** Policies use the Deny, DeployIfNotExists (DINE) and Modify effects, complemented by Audit for workload-specific compliance. Preventive policies stop non-compliant resources from being created, for example deployments in unapproved regions, subnets without a network security group or management ports exposed to the internet. Proactive policies deploy settings or enable services automatically so that new resources are compliant from the start, which greatly reduces the operational burden as the environment scales.

- **Automatic Security and Monitoring Configuration:** When a new subscription is placed under the Landing zones management group (for example under Corp), the following happens automatically:

  - Microsoft Defender for Cloud is enabled with the configured plans, including Defender CSPM, Defender for Servers and, where selected, Defender for AI workloads.

  - Azure Monitor Agent is deployed to virtual machines and scale sets with a central user-assigned managed identity, and data collection rules connect them to the central Log Analytics workspace for VM insights, change tracking and Azure Update Manager.

  - Resource logs (allLogs category group) and Activity Logs are sent to the central Log Analytics workspace in the Management subscription for unified monitoring, and service health alerts are configured.

  - Tagging and configuration standards apply: policies audit or enforce required tags such as Environment, Owner or CostCenter, add them when missing with the Modify effect, and enforce backup, encryption and private DNS integration for private endpoints.

- **Centralized Control with Flexibility:** All assignments are defined centrally and inherited by every subscription below the management group. Most guardrails use Azure built-in definitions, and custom ALZ definitions fill the gaps where no built-in definition exists. Assignments are pinned to validated versions of built-in policies, so changes to Microsoft's built-in definitions cannot silently alter the environment, and individual assignments can be adjusted or switched off through library overrides instead of code changes.

- **Guardrails vs. Speed:** Policy guardrails allow cloud adoption at speed without sacrificing governance. Workload teams deploy with their preferred tools while policies check in the background, for example that SQL databases use Microsoft Entra-only authentication, that storage accounts reject insecure SMB versions or that PaaS services do not expose public endpoints. Non-compliant deployments are denied immediately instead of being found in a later manual review.

By comparison, achieving such comprehensive policy coverage in a custom landing zone is challenging. The cloud team must identify the required policies, write or reuse definitions, test them and keep them current as Azure services change. Corner cases such as minimum TLS versions across all services, insecure SMB settings or deprecated network plugins are easily missed. The standardized landing zone includes them and Microsoft refines them continuously; for example, the management-port policy was tightened to deny 0.0.0.0/0 as a source, and deprecated custom definitions were replaced by built-in policies in April 2026.

The policy model embedded in the AdventureWorks landing zone provides (a) consistency, because every subscription and resource is governed uniformly, (b) compliance, because governance standards apply from day one, and (c) reduced operational effort, because automatic configuration replaces manual setup. This governance foundation is a primary advantage of the standardized approach.

## Continuous Maintenance and Microsoft Support

A significant benefit of a Microsoft-provided landing zone is ongoing maintenance. Cloud platforms and recommended practices evolve quickly, and the Azure landing zone is maintained as a living, open-source product by Microsoft engineers together with the community. This helps in several ways:

- **Regular, Documented Releases:** The Azure landing zone documentation publishes a monthly "What's new" log, the IaC accelerator publishes release notes with upgrade guidance, and the ALZ library is released in dated versions. Typical content includes:

  - Improvements to guidance and tooling, such as the guided PowerShell experience that generates the accelerator configuration, CAF naming standards, or cost-optimized scenarios for smaller organizations.

  - Policy refreshes that add new definitions, move existing ones to new versions and retire deprecated ones, each with notes on breaking changes and required actions.

  - Bug fixes, for example corrected parameter names or resource location mappings, documented together with the library version that contains the fix.

  - Support for new services and regions, for example new Azure regions in the private DNS zone policies, Defender for AI workloads, Azure Bastion and Private DNS Resolver options, or the Local management group for Azure Local workloads.

  - Tooling updates across the portal accelerator and the Bicep and Terraform starter modules, which are built on Azure Verified Modules and versioned independently.

Because these updates are documented publicly, customers can review each change and decide when to adopt it. The template is not static: Microsoft engineers refine it continuously, driven by community feedback through GitHub issues and regular community calls.

- **Alignment with the Azure Roadmap:** The landing zone follows Azure's platform roadmap. The Log Analytics agent was replaced by Azure Monitor Agent with data collection rules, automation accounts were removed from the default deployment once they were no longer needed, Microsoft Sentinel moved to a dedicated Security subscription as Sentinel transitions to the Microsoft Defender portal, and the Microsoft cloud security benchmark v2 was added as soon as it became available.

- **Documentation and Community:** Because many organizations use the same design, there is extensive documentation on Microsoft Learn and on the Azure landing zone documentation site, including FAQs, upgrade guides and known issues, as well as recorded community calls. In a fully custom approach, the internal team alone documents and troubleshoots the environment.

- **Controlled Upgrades:** Because the landing zone is deployed as code from a version-controlled repository, a new release is adopted through the normal change process: update the module and library versions, review the Terraform plan or Bicep what-if output and deploy through the pipeline. Release notes state which changes are breaking and what action is required, so the platform improves incrementally instead of being re-architected.

In contrast, a custom landing zone relies entirely on the organization's internal update process. If the original architects change roles or the team is busy, the design is not updated. Custom environments that were current at deployment often lack newer Azure features two years later, and catching up requires a dedicated project. The standardized approach turns improvement into a regular business-as-usual activity.

Recent examples illustrate the benefit of continuous maintenance:

- In September 2025, Microsoft introduced a Security management group and platform subscription to host security tooling such as Microsoft Sentinel. Organizations using the accelerator received the new structure together with documentation and upgrade steps.

- In April 2026, deprecated custom policy definitions and initiatives were removed and replaced by built-in policies or newer versions, and all remaining references to built-in policies were pinned to validated versions. This prevents unexpected breaking changes when Microsoft updates built-in policies, a risk that a custom environment would have to identify and manage on its own.

In summary, the standardized landing zone is not a one-time deliverable but an evolving product. By using it, an organization effectively shares the maintenance of its platform architecture with Microsoft, which keeps the environment current, secure and efficient as Azure grows.

## Organizational Model: Subscription-Based Delegations

Designing the organizational structure of the cloud environment is a complex task. It involves deciding how to divide Azure subscriptions, how to arrange management groups and how to assign responsibilities to platform and workload teams. The AdventureWorks template provides a proven model for this, which is a notable benefit over designing it in-house.

![Subscription-based delegation between platform, network and workload teams](graphics/Standardized%20Azure%20Landing%20Zone%20-%20Figure%203%20-%20Subscription-Based%20Delegations.png)

Figure 3: Subscription-based delegation between platform, network and workload teams

**Key aspects of the organizational model in the standardized landing zone:**

- **Management Group Hierarchy:** Below the tenant root group, an intermediate root management group (named after the organization) holds the entire landing zone, so that policies and role assignments are inherited consistently. Below it, the current design uses:

  - **Platform:** for central services, with child management groups for Security, Management, Identity and Connectivity, each containing a dedicated subscription managed by the platform team.

  - **Landing zones:** for workloads, divided by connectivity needs into Corp (internal workloads connected to the hub and to on-premises networks), Online (internet-facing workloads) and, where required, Local (Azure Local workloads). The AdventureWorks hub-and-spoke design places workload landing zones that are peered with the hub network under Corp.

  - **Sandbox and Decommissioned:** Sandbox for experimentation with relaxed guardrails and no connectivity to corporate networks, and Decommissioned for subscriptions that are being retired.

The accelerator creates this hierarchy from an architecture definition file in which names, display names and additional management groups can be customized. The hierarchy reflects the principles of a single control plane and subscription democratization: management is centralized, while the actual work happens in distributed subscriptions.

- **Dedicated Platform Subscriptions:** The reference architecture uses separate subscriptions for the platform functions:

  - A Management subscription for shared operations services such as the central Log Analytics workspace, data collection rules, the managed identity used by Azure Monitor Agent, and dashboards.

  - A Connectivity subscription for the hub virtual network or Virtual WAN hubs, VPN and ExpressRoute gateways, Azure Firewall, Azure Bastion, DDoS protection, private DNS zones and the Private DNS Resolver. Network administrators manage it without access to workload resources.

  - An Identity subscription for identity services that must run in Azure, such as domain controllers, together with their Recovery Services vault.

  - A Security subscription for security operations tooling, in particular the Log Analytics workspace used by Microsoft Sentinel.

  - Workload landing zone subscriptions, provided per workload and environment (for example development, test and production) and placed under Corp, Online or Local according to their requirements. Subscriptions are isolation boundaries for failures, permissions and scale limits, and the subscription democratization principle treats them as units of delegation: each workload team receives its own subscriptions and works freely within the guardrails.

- **Role-Based Access Control and Delegation:** Clear subscription boundaries enable an effective assignment of responsibilities:

  - The platform team (for example a Cloud Center of Excellence) owns the platform subscriptions, keeps read access across the estate and manages policies and core services, ideally with just-in-time privileged access through Microsoft Entra Privileged Identity Management.

  - The network team manages the Connectivity subscription, including peering, routing and firewall rules, without requiring access to workload resources.

  - Workload teams receive Owner or Contributor rights only on their own workload landing zone subscriptions. They cannot change platform resources such as central logging or the hub network, because these are outside their scope.

  This approach enforces least privilege and separation of duties, as recommended by the Azure landing zone design. Instead of a single shared subscription with overlapping responsibilities, access is compartmentalized along organizational boundaries such as workloads, departments or environments.

- **Subscription Vending and Scale:** As the number of workloads grows, new subscriptions are needed for new projects or because of quota limits. Microsoft recommends a repeatable, automated subscription vending process, available as Azure Verified Modules for Bicep and Terraform, that creates the subscription, places it in the correct management group, connects its virtual network to the hub and assigns roles and budgets. Every new workload landing zone therefore starts from the same governance and security baseline.

Overall, the organizational model in a standardized landing zone leads to:

- **Clarity:** Everyone knows which subscription or resource group they are responsible for. The platform team is not a bottleneck for every change; it provides the paved road and the guardrails, and workload teams operate within them.

- **Reduced Risk:** Delegating via subscriptions means, for example, an app team's owner rights are scoped just to their subscription. They can't accidentally alter central policies or network infrastructure. Any "experimentation" they do is contained.

- **Easier compliance and auditing:** If an auditor asks "Who has access to production data?", you can show that only Team X's admins have access to Subscription X (which contains that data) and central admins – a much simpler model than unwinding RBAC on hundreds of resource groups in one big subscription.

- **Scalability:** A new environment is a new subscription, a repeatable unit created through subscription vending. Policies ensure that it is configured correctly, so there is little room for environment drift.

When implementing a custom landing zone approach, it is possible to develop a comparable model; however, there is a risk of either oversimplification or unnecessary complexity. Organizations that are new to Azure may initially consolidate all resources within a single subscription for convenience, only to encounter challenges in separating workloads as they scale. Conversely, others might establish multiple subscriptions without a clear strategic framework or sufficient automation, resulting in inconsistencies across environments.

The AdventureWorks template incorporates an organizational structure informed by extensive enterprise experience. By adopting it, organizations benefit from a proven framework for subscription management and delegation instead of building such a model from scratch.

## Integrated Compliance with Microsoft Defender for Cloud

Security and compliance are core principles in the design of enterprise landing zones. A standardized landing zone integrates Azure's native security services, in particular Microsoft Defender for Cloud, so that all workloads are protected consistently.

Microsoft Defender for Cloud delivers two primary functions:

1.  **Cloud Security Posture Management (CSPM):** Continuous assessment of resources against security best practices and regulatory standards, surfaced as recommendations, secure score and compliance reports. Defender CSPM adds agentless scanning, attack path analysis and the cloud security explorer.

2.  **Cloud Workload Protection (Defender plans):** Threat protection for specific resource types such as servers, containers, databases, storage, Key Vault, Azure Resource Manager and AI workloads, including alerts for suspected malware on a virtual machine or SQL injection attacks on a database.

Within the AdventureWorks template, Defender for Cloud is part of the platform from the start rather than an optional add-on:

- **Automatic Enablement and Consistency:** DeployIfNotExists policies enroll every subscription below the intermediate root in Defender for Cloud with the configured plans and security contacts. This guarantees coverage across all workloads and prevents gaps caused by manual configuration errors. New subscriptions appear in the secure score and in the recommendations immediately.

- **Central Logging and Monitoring:** The landing zone provides a central Log Analytics workspace in the Management subscription. Resource logs, Activity Logs and Defender for Cloud data are sent there, giving a consolidated audit trail for compliance. Security operations data for Microsoft Sentinel is held in the dedicated Security subscription, so retention and access for security and operations data can be managed separately.

- **Defender Plans and Protections:** Defender plans for servers, storage, databases, containers, Key Vault, Resource Manager and AI workloads are enabled at scale through policy parameters. Vulnerability assessment uses Microsoft Defender Vulnerability Management, and the integration with Microsoft Defender for Endpoint is configured centrally, so individual teams do not have to activate protections themselves.

- **Continuous Compliance Assessment:** Because every landing zone subscription is covered, organizations have a single secure score and regulatory compliance dashboard. The Microsoft cloud security benchmark is assigned at the intermediate root (version 2 alongside version 1 while v2 is in preview), and further standards such as ISO 27001, PCI DSS or NIST SP 800-53 can be added. Workload-specific compliance initiatives for services such as SQL, Storage or Key Vault are assigned in audit mode.

- **Governance and Automation:** Governance rules in Defender for Cloud assign owners and due dates to recommendations, for example requiring high-severity findings to be remediated within 14 days, and exemptions can be documented with a justification. This provides one interface for enforcing and tracking the security posture across all subscriptions.

Workflow automation in Defender for Cloud connects alerts and recommendations with automated responses. A Logic App can, for example, create a ServiceNow ticket for the subscription owner when a critical recommendation appears. With Microsoft Sentinel in the Microsoft Defender portal, incidents from Defender for Cloud and other sources are correlated in a single incident queue and can trigger automated response and attack disruption. Because all subscriptions are onboarded the same way, a single automation covers the whole estate.

![Integrated compliance with Microsoft Defender for Cloud in the landing zone](graphics/Standardized%20Azure%20Landing%20Zone%20-%20Figure%204%20-%20Integrated%20Compliance%20with%20Defender%20for%20Cloud.png)

Figure 4: Integrated compliance with Microsoft Defender for Cloud in the landing zone

What advantages does this offer over a customized solution? Developing your own landing zone would require you to:

- Enable Microsoft Defender for Cloud plans on every subscription, because they are not enabled by default for new Azure subscriptions, and keep agents and extensions current.

- Make all teams forward their logs to a central workspace. Multiple siloed Log Analytics workspaces make it difficult to consolidate security data.

- Ensure that critical recommendations are prioritized and reviewed. Without a unified dashboard, some teams overlook recommendations and security gaps remain open.

- Automate tasks such as enabling Defender for SQL, vulnerability assessment and just-in-time VM access for each resource. Manual processes increase the risk that these measures are missed.

A primary advantage of the standardized landing zone lies in its comprehensive integration of security throughout the environment. Policies govern resources immediately upon creation, Defender performs continuous assessments during workload deployment, and any deviations are detected promptly, enabling automated remediation where necessary. This methodology enhances trust and compliance by ensuring all Azure assets are consistently managed and monitored according to recognized best practices.

From a compliance perspective–encompassing frameworks such as ISO, SOC, and HIPAA–the template streamlines adherence to various requirements, including centralized logging, segregation of access controls, ongoing monitoring, and clearly documented guardrails. This organized strategy significantly improves the capacity to demonstrate and maintain compliance.

Defender for Cloud shows how the security posture improves over time and integrates with Microsoft Defender XDR, Microsoft Purview and Microsoft Security Copilot. With this foundation in place, organizations can extend their defenses with Microsoft Sentinel in the Microsoft Defender portal for SIEM and advanced threat detection, which benefits directly from centralized log management. Microsoft Sentinel is supported only in the Defender portal after 31 March 2027, so new deployments should be onboarded there from the start.

The standardized Azure Landing Zone, integrated with Defender, ensures that security and compliance controls are consistently applied. For organizations aiming to meet industry standards or protect sensitive information, this approach offers substantial benefits over ad-hoc custom configurations.

## Summary

Implementing a standardized Azure landing zone such as the AdventureWorks template provides a robust and scalable foundation for the cloud environment. The advantages can be summarized as follows:

- **Comprehensive Governance Out-of-the-Box:** Centralized Azure Policy enforcement ensures that cloud resources adhere to industry best practices from inception. This approach strengthens security, reduces misconfigurations, and minimizes manual remediation. In contrast, a custom landing zone necessitates developing such a governance framework independently.

- **Continuous Improvements and Support:** Microsoft continually enhances the landing zone and documents every release. Organizations benefit from this ongoing development and evolve with Azure's latest features and community insights without frequent architectural revisions. Custom-built solutions often stagnate or require significant internal resources for maintenance.

- **Built-in Best Practices (Guardrails):** Incorporating established patterns from network design (such as hub-and-spoke models with centralized control) to identity and access management, the standardized landing zone embodies both industry standards and Microsoft's extensive field expertise. This comprehensive blueprint mitigates risk and helps ensure critical considerations–like ExpressRoute gateway configuration or management group structuring–are not overlooked, delivering an "enterprise-grade" foundation by default.

- **Clear Organizational Alignment:** Employing multiple subscriptions and management groups aligned to organizational units or environments enhances clarity and scalability. Teams operate autonomously within their respective subscriptions, while central IT retains oversight and governance at the management group level, achieving a balance between agility and control. While this model is possible with custom solutions, the standardized template offers a proven path, minimizing potential pitfalls during cloud adoption.

- **Enhanced Security Posture:** By integrating Microsoft Defender for Cloud and automating security controls, the standardized landing zone significantly fortifies security with minimal additional effort. Features such as continuous monitoring, threat detection, and automated response capabilities are embedded within operational workflows. In today's evolving threat landscape, having robust defense mechanisms activated by default is of considerable value.

- **Operational Efficiency & Cost Management:** Adopting a standardized approach reduces operational overhead over time through automation of routine tasks, ensuring environmental consistency and simplifying management. Although certain recommended services (such as Defender plans or centralized logging) may incur costs, the implementation of tagging, segmentation, and policy enforcement enables effective cost tracking and optimization. The risks associated with deviating from best practices–including potential security incidents or operational inefficiencies–can far outweigh these investments.

- **Rapid Deployment:** The Azure landing zone accelerators deploy the AdventureWorks template as code through CI/CD pipelines, so organizations can establish a secure, well-architected environment in days rather than weeks and focus on building business solutions on a reliable Azure foundation.

Alternatively, a custom landing zone may be developed to address highly specific requirements or constraints that the standard reference design does not accommodate. Such instances, however, are uncommon. Most organizations determine that the flexible architecture of enterprise-scale landing zones meets their objectives with minor modifications, such as enabling or disabling certain features or incorporating bespoke policies as needed. Overall, the effort-to-benefit ratio strongly favors leveraging the standardized template and implementing tailored adjustments within that structure, rather than completely redesigning the system from scratch.

## References

The status of this document is November 2026. The statements on the current Azure landing zone baseline are based on the following Microsoft sources:

- [Azure landing zone documentation, What's new](https://azure.github.io/Azure-Landing-Zones/whats-new/)
- [Azure landing zone IaC accelerator, release notes](https://azure.github.io/Azure-Landing-Zones/accelerator/accelerator-release-notes/)
- [Cloud Adoption Framework, What is an Azure landing zone?](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)
- [Release of Bicep Azure Verified Modules for Platform Landing Zone, Microsoft Tech Community, January 2026](https://techcommunity.microsoft.com/blog/azuretoolsblog/release-of-bicep-azure-verified-modules-for-platform-landing-zone/4487932)
- [Microsoft Sentinel in the Microsoft Defender portal](https://learn.microsoft.com/azure/sentinel/microsoft-sentinel-defender-portal)
