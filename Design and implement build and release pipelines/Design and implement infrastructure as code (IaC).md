# Design and implement build and release pipelines

## Design and implement infrastructure as code (IaC)

### Recommend a configuration management technology for application infrastructure
A useful starting distinction is:

Infrastructure provisioning
→ What infrastructure should exist?

Configuration management
→ How should machines/software be configured?

There is overlap between modern tools, but the distinction helps with exam scenarios.

For Azure/AZ-400, the technologies worth distinguishing include:

Bicep / ARM templates
→ Azure-native declarative infrastructure

Terraform
→ declarative infrastructure across Azure and other platforms

Azure CLI / PowerShell
→ imperative scripting and orchestration

Ansible
→ agentless configuration management + automation

PowerShell DSC
→ desired-state configuration, especially Windows-oriented scenarios
Declarative vs imperative

This is fundamental.

Imperative says how to perform the change:

1. Create resource group
2. Create VNet
3. Create subnet
4. Create VM
5. Configure VM

For example, a long Azure CLI script.

Declarative says what the desired state should be:

VNet:
  address space = 10.0.0.0/16

Subnet:
  address = 10.0.1.0/24

The IaC engine determines the operations required to reach that state.

That connects directly to the idempotency concept from your resiliency work.

Bicep

Bicep is particularly appropriate when the infrastructure is Azure-focused:

Bicep
  ↓
Azure Resource Manager
  ↓
Resource Group
├── App Service
├── Storage
├── Key Vault
└── App Configuration

Advantages include Azure-native resource support, declarative syntax, dependency handling, modules, and no separate infrastructure-state file to manage.

Terraform

Terraform is especially relevant when infrastructure spans providers:

Terraform
├── Azure
├── AWS
├── Cloudflare
└── other providers

A major architectural distinction is state. Terraform maintains state describing the infrastructure it manages.

So when an exam question mentions Terraform collaboration, think about:

remote state
locking
secure state storage
team access

rather than having everyone's important state sitting independently on their laptops.

Ansible

Ansible becomes particularly interesting when the requirement is about configuring systems:

VM already exists
   ↓
Install nginx
Configure files
Create users
Configure service
Start service

It is agentless in common usage and typically connects remotely to managed machines.

PowerShell DSC

Desired State Configuration focuses on defining and maintaining desired machine configuration:

Server should have:
├── IIS installed
├── required Windows features
├── configuration present
└── service running

The emphasis is not just “run these commands,” but:

This is the state the machine should be in. One nuance: don't memorize:

“Bicep = infrastructure, Ansible = configuration”

as an absolute boundary. Modern tools overlap. Bicep can invoke mechanisms that configure workloads, Terraform providers can manage configuration-like resources, and Ansible can provision cloud resources. On the exam, choose based on the dominant requirement.

Keep this distinction clear:

Terraform configuration (.tf)
→ source code
→ version control ✅

Terraform state (.tfstate)
→ infrastructure state
→ may contain sensitive values
→ remote secured backend ✅
→ ordinary Git repository ❌

For “Recommend a configuration management technology for application infrastructure”, your decision framework is:
| Requirement                             | Strong candidate           |
| --------------------------------------- | -------------------------- |
| Azure-native declarative IaC            | **Bicep**                  |
| Multi-cloud declarative IaC             | **Terraform**              |
| Linux/agentless machine configuration   | **Ansible**                |
| Windows desired-state/drift enforcement | **PowerShell DSC**         |
| Imperative Azure automation             | **Azure CLI / PowerShell** |


### Define an IaC strategy, including source control and automation of testing and deployment
This point moves from choosing a configuration management technology to actually designing how it will be implemented and operated.

The key question becomes:

How do we keep infrastructure and machine configuration consistent, repeatable, secure, and recoverable over time?

A good configuration management strategy usually includes:

- configuration stored as code
- version control and PR review
- reusable modules/playbooks/configurations
- environment-specific parameters kept separate from reusable logic
- secrets kept outside source code
- idempotent execution
- drift detection and remediation
- CI/CD execution rather than manual administration
- validation before and after changes

You can think of it as:

Configuration code
      ↓
Version control
      ↓
PR / validation
      ↓
Pipeline
      ↓
Apply desired configuration
      ↓
Validate result
      ↓
Detect/remediate drift

The implementation pattern changes depending on the tool.

For Ansible, you might have:

inventory
playbooks
roles
group_vars

For DSC, you'd define desired-state configurations and apply/enforce them against target machines.

For Bicep/Terraform, you'd typically use modules, parameters, remote state where applicable, and deployment pipelines.

Idempotency matters again

Suppose configuration says:

nginx must be installed
nginx must be running
/etc/nginx/nginx.conf must contain X

Running the automation once should configure that state.

Running it again should ideally result in:

already correct → no unnecessary change

rather than reinstalling or corrupting the machine.

That is why configuration management tools are generally stronger than long chains of one-off shell commands.

- version-controlled
- reusable
- parameterized by environment
- secret-safe
- idempotent
- validated after application
- capable of detecting/remediating drift where appropriate
- Automation is not enough if it only runs once.
- Reuse one role/module and externalize environment-specific values.
- Keep secrets out of ordinary source-controlled files.
- Idempotency means repeated execution converges on the same desired state without unnecessary changes.


### Design and implement desired state configuration for environments, including Azure Automation State Configuration, Azure Resource Manager, Bicep, and Azure Machine Configuration
This objective ties together two different layers of desired state:

Azure environment
├── Resource state
│   └── ARM / Bicep
│
└── Machine/OS state
    ├── Azure Machine Configuration
    └── Azure Automation State Configuration (legacy)

You've already demonstrated desired state with Ansible:

desired configuration
→ compare with actual configuration
→ correct differences

Now we need to understand how Azure's technologies apply that principle.

ARM and Bicep — desired Azure resource state

ARM templates and Bicep describe the desired state of Azure resources.

For example:

resource storage 'Microsoft.Storage/storageAccounts@...' = {
  name: storageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
}

You're declaring:

“This storage account should exist with this configuration.”

rather than scripting:

Create storage account
Change SKU
Set property X
...

Bicep is essentially a more concise authoring language for Azure Resource Manager deployments.

Think:

Bicep source
    ↓
ARM deployment
    ↓
Azure resources
Azure Machine Configuration

Here's the important new part.

Suppose Bicep creates a VM successfully:

Bicep
 ↓
VM exists ✅

That doesn't necessarily answer:

Is the required software installed?
Are security settings correct?
Are required services configured?
Is the OS compliant with organizational policy?

Azure Machine Configuration addresses configuration/compliance inside machines. It's part of Azure Policy's guest configuration capabilities and can audit or apply machine settings.

So remember the boundary:

Bicep / ARM
→ Azure resource state

Machine Configuration
→ configuration inside the machine

Machine Configuration can work with Azure VMs and, through Azure Arc, non-Azure machines as well.

Azure Automation State Configuration

Azure Automation State Configuration historically provided a managed Azure implementation of PowerShell DSC, where configurations could be compiled into node configurations and assigned to managed nodes.

Conceptually:

PowerShell DSC configuration
        ↓
Azure Automation
        ↓
compiled node configuration
        ↓
registered nodes
        ↓
desired state

However, this is where current knowledge matters: Azure Automation State Configuration was retired on September 30, 2025. Microsoft directs customers toward Azure Machine Configuration.

For your exam objective, you still need to recognize what it was and how it differs, because the published objective explicitly names it.

A useful mental model is:

Azure Automation State Configuration
→ older Azure-hosted PowerShell DSC service

Azure Machine Configuration
→ current Azure governance/configuration approach

- ARM/Bicep → desired state of Azure resources.
- Azure Machine Configuration → desired/compliant state inside machines.
- Azure Automation State Configuration → older DSC-based service; retired, but still relevant to recognize in legacy scenarios.
- Bicep does not replace ARM; it is an authoring layer that deploys through ARM.
- Use Bicep and Machine Configuration together when both resource state and guest OS state matter.
- Reapplying the same Bicep should converge safely rather than recreate resources unnecessarily.
- A changed declaration causes ARM to update supported properties toward the new desired state.
- For new guest-configuration designs, favor Azure Machine Configuration over retired Azure Automation State Configuration.

### Design and implement Azure Deployment Environments for on-demand self-deployment
This objective introduces Azure Deployment Environments (ADE). The important idea is controlled developer self-service.

Instead of developers filing tickets:

Developer
   ↓
"Please create my test environment"
   ↓
Platform team
   ↓
manually provision resources

the platform team defines approved environment templates, and developers provision environments themselves:

Platform team
   ↓
approved IaC definitions
   ↓
ADE catalog
   ↓
Developer self-service
   ↓
Dev/Test/Sandbox environment

This connects strongly to your earlier subscription-vending/platform thinking: the platform team defines the guardrails, while consumers get self-service within those boundaries.

Core ADE hierarchy

There are several objects to distinguish:

Dev Center
   ↓
Project
   ↓
Environment
   ↓
Azure resources

Dev Center is the organizational/platform-level resource. It centralizes things such as catalogs and environment types.

Project represents a development workload/team and connects developers to the environment types they're permitted to use.

Environment is an actual deployed instance requested by a developer.

For example:

Dev Center: ContosoEngineering
│
├── Catalog
│   ├── WebApp
│   └── ThreeTierApp
│
└── Project: Orders
       ↓
   Developer requests WebApp
       ↓
   Environment: matt-dev
       ↓
   actual Azure resources
Catalogs and environment definitions

The platform team provides environment definitions through catalogs.

An environment definition contains IaC describing an approved environment. ADE supports IaC approaches including ARM/Bicep and Terraform scenarios.

Conceptually:

Git repository
└── environments/
    └── webapp/
        ├── manifest
        └── IaC files
             ↓
          ADE Catalog
             ↓
     WebApp definition available

This gives developers choices without giving them unrestricted infrastructure creation.

Environment types

This is an important exam concept.

An environment definition answers:

What should be deployed?

For example:

WebApp definition
→ App Service + Storage + Key Vault

An environment type is more about:

Where/how is this class of environment deployed and governed?

For example:

Dev
Test
Sandbox

At the project level, environment types can be configured with deployment subscriptions, identities, and permissions.

So don't confuse:

Environment definition
→ infrastructure template

Environment type
→ deployment/governance context
Why use ADE instead of giving developers IaC directly?

Developers could technically clone a Bicep repository and execute:

az deployment group create ...

But ADE adds a self-service platform layer.

The platform team can control:

approved templates
allowed environment types
deployment identity
permissions
subscriptions
governance

while developers get:

choose approved definition
→ supply permitted parameters
→ create environment
→ use it
→ delete it when finished

This is especially useful for ephemeral development/test environments.

- Dev Center → central organization/platform scope.
- Project → team/workload scope within the Dev Center.
- Catalog → supplies approved environment definitions.
- Environment definition → describes what can be deployed.
- Environment type → controls the deployment/governance context such as Dev or Test.
- Environment → actual deployed instance.
- Developers request environments; they don't necessarily need broad permissions to provision every underlying resource.
- ADE provides controlled self-service, not unrestricted infrastructure access.
- IaC can enforce additional constraints even when the self-service interface already restricts inputs.