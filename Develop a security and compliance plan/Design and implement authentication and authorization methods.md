# Develop a security and compliance plan

## Design and implement authentication and authorization methods

### Choose between Microsoft Entra service principals and managed identities for Azure resources (system-assigned and user-assigned)
The three choices you need to distinguish are:

Service principal
Managed identity
├── System-assigned
└── User-assigned
Service principal

A service principal is an application identity in Microsoft Entra ID that automation or applications can use.

Conceptually:

Application / pipeline
        ↓ authenticate
Service principal
        ↓ RBAC
Azure resource

A service principal can authenticate using a client secret or certificate. In modern CI/CD scenarios, federated identity/workload identity federation can also avoid stored credentials.

The important characteristic is that the service principal has a lifecycle separate from an Azure compute resource.

Managed identity

A managed identity is also represented through Microsoft Entra ID, but Azure manages important parts of the identity lifecycle for you.

The big advantage:

The Azure workload can authenticate without you managing application credentials.

For example:

Azure Function
      ↓
Managed identity
      ↓
Key Vault Secrets User
      ↓
Key Vault

No Key Vault password needs to sit inside the Function's configuration.

This is usually a strong signal in exam questions:

Azure resource needs to access another Azure resource without storing credentials.

Think managed identity.

System-assigned managed identity

A system-assigned identity belongs directly to one Azure resource.

VM
├── VM lifecycle
└── System-assigned identity

Enable it on the VM → identity exists.

Delete the VM → identity is deleted with it.

The relationship is essentially:

1 Azure resource
      ↕
1 system-assigned identity

This gives strong lifecycle coupling.

Typical scenario:

One Function App needs access to Key Vault and doesn't need to share its identity with anything else.

System-assigned is a natural choice.

User-assigned managed identity

A user-assigned managed identity is created as a separate Azure resource.

User-assigned identity
       ↓
 ┌─────┼─────┐
 ↓     ↓     ↓
VM1   VM2   VM3

Multiple Azure resources can use the same identity.

Its lifecycle is also independent:

Delete VM1
→ user-assigned identity remains

That makes it useful when multiple workloads should share the same identity/permissions or when the identity must survive replacement of an application resource.

A useful decision model
Azure-hosted workload?
        │
        ├─ Yes → Can managed identity satisfy the requirement?
        │           │
        │           ├─ Yes → usually prefer managed identity
        │           │
        │           └─ No → consider service principal/federation
        │
        └─ No → service principal/federated identity may fit

Then, if managed identity fits:

Identity tied to one resource's lifecycle?
→ System-assigned

Identity shared/reused or independently managed?
→ User-assigned

Authentication ≠ authorization

This distinction is extremely important for AZ-400.

Creating a managed identity does not automatically give it access to resources.

Managed identity
→ WHO the workload is

Azure RBAC
→ WHAT it's allowed to do

For example:

Function's managed identity
        +
Key Vault Secrets User
        +
scope = Key Vault
        ↓
Function can read appropriate secrets

That connects directly to your earlier least-privilege work.

- System-assigned managed identity → one Azure resource, same lifecycle.
- User-assigned managed identity → reusable/shared identity with independent lifecycle.
- Service principal → useful when the workload is external to Azure or needs its own Entra application identity.
- Managed identity handles authentication; RBAC handles authorization.
- “No stored credentials” + Azure-hosted workload → managed identity is a strong candidate.
- “Identity must survive resource replacement” → user-assigned managed identity.
- “External system authenticates to Azure” → service principal or federated identity.
- Enabling a managed identity does not grant resource access automatically.
- Prefer least privilege at the narrowest practical scope.

### Implement and manage GitHub authentication, including GitHub Apps, GITHUB_TOKEN, and personal access tokens
This objective is about choosing the right GitHub credential for automation and managing its permissions safely.

The three main options are:

GITHUB_TOKEN
→ built into GitHub Actions
→ short-lived
→ scoped to the workflow repository

GitHub App
→ independent application identity
→ fine-grained permissions
→ can span repositories/org resources
→ good for long-lived automation

Personal access token (PAT)
→ tied to a user
→ useful for user-scoped automation or short-lived scripts
→ fine-grained PAT preferred over classic PAT when possible

GitHub recommends using the built-in GITHUB_TOKEN when it can satisfy the workflow requirement. It is created automatically for each job, is based on a GitHub App installation token, and is scoped to the repository containing the workflow.

GITHUB_TOKEN

A workflow can use:

permissions:
  contents: read
  issues: write

and then access the token as:

${{ secrets.GITHUB_TOKEN }}

or:

${{ github.token }}

The important security rule is:

Explicitly grant only the permissions the workflow needs.

So if the workflow only checks out source:

permissions:
  contents: read

is preferable to broad write permissions.

GITHUB_TOKEN is short-lived and expires with the job/effective token lifetime. It also has an intentional recursion safeguard: most events created using it do not trigger another workflow automatically, which prevents accidental infinite workflow chains.

GitHub Apps

A GitHub App is better when automation needs more than the current repository, such as:

Repository A
   ↓
Automation
   ├→ Repository B
   ├→ organization project
   └→ multiple repositories

GitHub Apps have fine-grained permissions, can be limited to chosen repositories, use short-lived installation tokens, and are not tied to one employee's account. GitHub recommends them for long-lived integrations and organization-level automation.

This is similar to the service-principal discussion you just had:

PAT
→ personal identity lifecycle

GitHub App
→ application identity lifecycle
Personal access tokens

PATs authenticate as a GitHub user.

That means automation using a PAT is affected by:

user leaves organization
user permissions change
token expires/revoked
organization PAT policy changes

GitHub currently recommends fine-grained PATs over classic PATs whenever possible.

PATs still have legitimate uses, especially user-oriented scripts or cases where a particular API/resource isn't well suited to GITHUB_TOKEN or a GitHub App.

The important exam rule is:

Don't choose a PAT just because it's easiest to paste into a secret.

GITHUB_TOKEN
→ GitHub Actions automation
→ primarily current repository
→ automatically provided
→ short-lived
→ permissions controlled by workflow

GitHub App
→ durable application identity
→ selected repositories / organization automation
→ fine-grained permissions
→ not tied to an employee
→ short-lived installation tokens

Fine-grained PAT
→ acts as a user
→ useful for personal/user-scoped automation
→ user manages token lifecycle

- You don't manually generate GITHUB_TOKEN.
- GITHUB_TOKEN authentication doesn't mean unlimited access; permissions: controls authorization.
- Follow least privilege rather than write-all.
- Prefer a GitHub App over a PAT for durable organizational automation.
- Prefer fine-grained PATs when a PAT is appropriate.
- Don't hard-code PATs into workflows or repositories.
- A GitHub App is an application identity; a PAT represents a user.
- gh may also require repository context—the error you encountered wasn't an authentication failure


### Implement and manage Azure DevOps service connections and personal access tokens

Service connection
→ pipeline-oriented authentication
→ centrally managed
→ can use workload identity federation
→ preferred for durable automation

PAT
→ user-bound bearer token
→ manually created and rotated
→ better for short-lived personal/legacy scenarios
→ higher risk

Microsoft's current guidance is to prefer Microsoft Entra-based authentication and service connections over PATs when possible. For Azure Pipelines, workload identity federation is especially important because it avoids storing long-lived secrets.

Service connections

A service connection gives a pipeline an authenticated relationship to another system.

For example:

Azure Pipeline
      ↓
Service connection
      ↓
Azure subscription/resource group

Instead of putting credentials directly into YAML:

password: super-secret-value

the YAML references the service connection by name:

azureSubscription: my-service-connection

The connection itself contains or establishes the authentication mechanism.

A strong modern pattern for Azure Resource Manager service connections is:

Azure Pipeline
      ↓
Workload identity federation
      ↓
Microsoft Entra identity
      ↓
Azure RBAC
      ↓
Target resources

No client secret needs to sit in Azure DevOps. Microsoft recommends workload identity federation whenever possible for Azure service connections.

Scope matters

A service connection is still only as secure as the permissions behind it.

Suppose the pipeline deploys only one application resource group.

This:

Service connection
→ Contributor
→ entire subscription

is much broader than:

Service connection
→ appropriate role
→ application's resource group

So once again:

Right identity + right role + right scope.

You can also restrict which pipelines may use a service connection instead of enabling broad “grant access to all pipelines” behavior.

PATs

A PAT represents a user in Azure DevOps.

Conceptually:

Personal script
    ↓
PAT belonging to Alice
    ↓
Azure DevOps REST API

PATs are bearer secrets, so anyone who obtains one may be able to act with its granted scopes. Microsoft recommends avoiding PATs when stronger Entra-based mechanisms are available and treating PATs with password-level care.

When a PAT really is necessary, you should minimize:

scope
+ organization access
+ lifetime

and rotate/revoke it appropriately. Azure DevOps also provides administrative PAT policies to limit their creation, scope, and lifespan.

Service connection vs PAT

A good exam model is:

Azure Pipeline needs durable access
→ Service connection

Pipeline → Azure
→ preferably workload identity federation

Personal temporary script
→ PAT may be acceptable

Production automation using long-lived user PAT
→ usually redesign

The simplest distinction is:

Service connection
→ pipeline-oriented authentication
→ centrally managed
→ can use workload identity federation
→ preferred for durable automation

PAT
→ user-bound bearer token
→ manually created and rotated
→ better for short-lived personal/legacy scenarios
→ higher risk

Microsoft's current guidance is to prefer Microsoft Entra-based authentication and service connections over PATs when possible. For Azure Pipelines, workload identity federation is especially important because it avoids storing long-lived secrets.

Service connections

A service connection gives a pipeline an authenticated relationship to another system.

For example:

Azure Pipeline
      ↓
Service connection
      ↓
Azure subscription/resource group

Instead of putting credentials directly into YAML:

password: super-secret-value

the YAML references the service connection by name:

azureSubscription: my-service-connection

The connection itself contains or establishes the authentication mechanism.

A strong modern pattern for Azure Resource Manager service connections is:

Azure Pipeline
      ↓
Workload identity federation
      ↓
Microsoft Entra identity
      ↓
Azure RBAC
      ↓
Target resources

No client secret needs to sit in Azure DevOps. Microsoft recommends workload identity federation whenever possible for Azure service connections.

Scope matters

A service connection is still only as secure as the permissions behind it.

Suppose the pipeline deploys only one application resource group.

This:

Service connection
→ Contributor
→ entire subscription

is much broader than:

Service connection
→ appropriate role
→ application's resource group

So once again:

Right identity + right role + right scope.

You can also restrict which pipelines may use a service connection instead of enabling broad “grant access to all pipelines” behavior.

PATs

A PAT represents a user in Azure DevOps.

Conceptually:

Personal script
    ↓
PAT belonging to Alice
    ↓
Azure DevOps REST API

PATs are bearer secrets, so anyone who obtains one may be able to act with its granted scopes. Microsoft recommends avoiding PATs when stronger Entra-based mechanisms are available and treating PATs with password-level care.

When a PAT really is necessary, you should minimize:

scope
+ organization access
+ lifetime

and rotate/revoke it appropriately. Azure DevOps also provides administrative PAT policies to limit their creation, scope, and lifespan.

Service connection vs PAT

A good exam model is:

Azure Pipeline needs durable access
→ Service connection

Pipeline → Azure
→ preferably workload identity federation

Personal temporary script
→ PAT may be acceptable

Production automation using long-lived user PAT
→ usually redesign

There's also a newer Azure DevOps service connection for pipelines that need to access Azure DevOps resources themselves—such as cross-organization repositories or feeds—using Entra workload identities instead of PATs.


So a useful mnemonic is:
Managed identity/service principal = WHO. Service connection = CONNECTION. PAT = USER TOKEN.

Developer PAT
→ tied to a person
→ secret must be protected/rotated
→ employee lifecycle can affect automation

Service connection + workload identity federation
→ designed for pipeline access
→ centrally managed in Azure DevOps
→ no long-lived client secret
→ Azure RBAC can be narrowly scoped

| Option                                            | Meaning                                                                                               | Typical use                                                                           |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **App registration**                              | Azure DevOps uses an Entra application/service principal, typically with workload identity federation | Strong default for Azure Pipelines → Azure                                            |
| **App registration or managed identity (manual)** | You provide/configure an existing identity manually                                                   | When you need explicit control over an existing identity or can't use automatic setup |
| **Managed identity**                              | Service connection uses an Azure managed identity                                                     | Useful when your Azure DevOps execution setup can use an existing MI                  |
| **Managed identity (agent-assigned)**             | Uses a managed identity assigned to the self-hosted Azure agent's compute                             | Self-hosted agents running on Azure resources with MI                                 |


Pipeline
   ↓
[Azure DevOps authorization]
"May this pipeline use az400-wif-rg-sc?"
   ↓
Service connection
   ↓
[Azure RBAC]
"What may its identity do in Azure?"
   ↓
Resource group

This separation is very exam-relevant.

Keep this model:

Azure Pipeline
   ↓
Service connection
   ↓
Entra identity
   ↓
Azure RBAC
   ↓
Azure resources

For Azure access, a service connection using workload identity federation is a strong default because it avoids long-lived secrets.

PATs are different:

PAT
→ represents a user in Azure DevOps
→ bearer secret
→ should be narrowly scoped and short-lived when required

- Prefer service connections over personal PATs for durable pipeline automation.
- Scope the Azure identity to the narrowest required resource scope.
- Restrict which pipelines may use a service connection.
- Service connection authorization and Azure RBAC are separate controls.
- Microsoft-hosted agents commonly pair well with an app registration + workload identity federation.
- Self-hosted Azure agents can also use managed-identity-based options.
- PATs are user-bound and require lifecycle management, rotation, and careful scoping.
- Azure DevOps authorization controls which pipelines can use the connection.
- Azure RBAC controls what the underlying identity can do in Azure.

### Design and implement permissions and roles in GitHub
This point is about authorization inside GitHub: who can access an organization or repository, and what they are allowed to do.

The main layers are:

Organization
├── Owners
├── Members
└── Teams
      ↓
Repositories
      ↓
Repository roles / permissions

At repository level, common permission levels include:

Read
→ view/clone repository

Triage
→ manage issues and pull requests without write access to code

Write
→ push code, manage normal development work

Maintain
→ manage repository settings without full administrative control

Admin
→ full repository administration

The key security principle is again least privilege.

If someone only manages issues, don't give them Write.
If a team only needs to deploy from one repository, don't give them organization-wide admin rights.

Teams

For organizations, teams are usually better than granting permissions user-by-user:

Team: BackendDevelopers
→ Write on backend-repo

Team: Security
→ Read on all repos
→ Maintain on security-policy repo

This makes access easier to manage when people join, leave, or change roles.

Organization roles vs repository roles

Don't confuse:

Organization Owner
→ broad organization-level administration

Repository Admin
→ full control of one repository

Repository Write
→ development permissions, not repository administration

Someone can therefore be a normal organization member but still have Admin on one specific repository.

Permissions vs branch/ruleset controls

Repository role determines what someone is generally allowed to do.

Rulesets/branch protection can still restrict actions such as:

Write permission
   ↓
can push branches

main protected
   ↓
cannot bypass required PR/checks

So:

Repository role grants capability; repository rules constrain how that capability is exercised.

Repository role
→ who can contribute and at what level

Ruleset
→ how protected branches/tags may be changed

### Design and implement permissions and security groups in Azure DevOps
The core question is:

Who should be allowed to perform which actions in Azure DevOps, and at what scope?

Azure DevOps authorization has several layers:

Organization
    ↓
Project
    ↓
Resources
├── Repositories
├── Pipelines
├── Environments
├── Service connections
├── Agent pools
└── Artifacts
Security groups

Instead of assigning permissions individually, Azure DevOps provides built-in groups, and you can create custom groups.

Important built-in project groups include:

Project Administrators
→ broad project administration

Contributors
→ normal development activities

Readers
→ read-only access

Build Administrators
→ administer build/pipeline resources

Release Administrators
→ administer release-related resources

The same principle from GitHub applies:

Prefer group membership over managing dozens of individual ACLs.

For example:

Developers
    ↓
Contributors group
    ↓
standard development permissions

rather than configuring Alice, Bob, Charlie, etc. independently.

Permissions: Allow, Deny and Not set

This is a particularly important Azure DevOps concept.

A permission can generally appear as:

Allow
Deny
Not set

Allow explicitly grants it.

Not set means there is no explicit grant from that permission entry; the user may still receive the permission through another group.

Deny is much stronger. An explicit deny generally overrides an allow inherited through another group.

For example:

Developers group
→ Delete repository: Allow

Alice
→ Delete repository: Deny

Effective permission for Alice
→ Deny

This is why throwing Deny everywhere can create difficult permission problems.

Inheritance

Permissions commonly flow down from broader scopes.

Conceptually:

Project
   ↓ inherited
Repository
   ↓
Branch

You can customize permissions at narrower scopes when necessary.

But the goal isn't:

“Configure every repository and pipeline differently.”

Prefer predictable group-based permissions and introduce exceptions only where requirements justify them.

Azure DevOps groups vs Microsoft Entra groups

You may also use Microsoft Entra groups to manage membership.

For example:

Entra group
Backend-Developers
       ↓
Azure DevOps
       ↓
appropriate project/group permissions

This can simplify lifecycle management because membership is centrally maintained rather than manually adding every employee in Azure DevOps.

Don't confuse permissions with access levels

This is an exam-worthy distinction.

An Azure DevOps access level such as Stakeholder or Basic determines the broad product capabilities available to a user.

A permission determines whether they're authorized to perform a particular operation.

Conceptually:

Access level
→ What Azure DevOps features are available?

Permissions/security groups
→ What is this user authorized to do?

Both can affect what the user ultimately experiences.

Reader
→ view the environment

User
→ use the environment in pipelines

Creator
→ create environments

Administrator
→ manage the environment and its security

- Prefer security groups over individual user permissions.
- Use built-in groups such as Contributors and Project Administrators where they match the requirement.
- Create narrower/custom groups when built-in groups are too broad.
- Apply permissions at the narrowest appropriate scope: organization → project → repository/pipeline/environment/etc.
- Allow grants a permission.
- Not set doesn't explicitly grant it; permissions can come from other memberships.
- Deny is an explicit restriction and generally overrides Allow, so use it deliberately.
- Don't confuse access levels with permissions.
- Some Azure DevOps resources use their own roles. For an Environment, User allows use while Administrator provides management authority.

### Recommend appropriate access levels, including stakeholder access in Azure DevOps and outside collaborator access in GitHub
Access level answers “what product capabilities should this person have?” while permissions answer “what are they authorized to do?”

The two named concepts to know are:

Azure DevOps
→ Stakeholder access

GitHub
→ Outside collaborator
Azure DevOps: Stakeholder access

Azure DevOps access levels include Stakeholder and Basic (with additional levels depending on licensing/services).

Stakeholder is intended for users who need limited participation without the full capabilities of a regular developer.

Think of people such as:

Product owner
Business stakeholder
Project manager
Occasional participant

They might need to participate in planning/work tracking without needing the complete developer toolset.

A developer who needs normal repository and development functionality is more naturally associated with Basic, assuming the requirements fit.

Access level ≠ permissions

This is the main exam trap.

Suppose Alice has:

Access level:
Stakeholder

Security group:
Contributors

You cannot simply reason:

“Contributors gives her everything a developer needs.”

Her access level can still limit product functionality.

Think of effective access as constrained by both:

Access level
     +
Permissions
     ↓
What the user can actually do

So changing a permission doesn't necessarily solve an access-level limitation.

GitHub: Outside collaborator

An outside collaborator is someone who has access to repositories in a GitHub organization without becoming an organization member.

For example:

GitHub Organization
├── Employees → organization members
│
└── External contractor
       ↓
   outside collaborator
       ↓
   specific repository

This is useful for contractors, consultants, or partners who need access to selected repositories but don't need general organization membership.

The important security principle is:

Give external users access only to the repositories and permission levels they actually need.

So:

Contractor
→ Outside collaborator
→ orders-api
→ Write

can be preferable to unnecessarily making the contractor an organization member with broader organizational access.

Outside collaborator ≠ permission level

This is another exam trap.

Outside collaborator describes the person's relationship to the organization.

It doesn't mean:

Outside collaborator = Read

You still choose an appropriate repository permission separately:

Outside collaborator
        +
Repository role
→ Read / Triage / Write / Maintain / Admin

So this mirrors what we just learned in Azure DevOps:

Azure DevOps:
Access level ≠ permission

GitHub:
Organization relationship ≠ repository permission

### Configure projects and teams in Azure DevOps
The new focus is the organizational structure of Azure DevOps:

Azure DevOps Organization
        ↓
Projects
        ↓
Teams
        ↓
Boards / backlogs / iterations / areas
Projects

A project is a major isolation and organization boundary in Azure DevOps. It contains resources such as:

Project: OnlineStore
├── Repos
├── Pipelines
├── Boards
├── Test Plans
├── Artifacts
└── Teams

A common exam trap is creating a project for every application or development team.

Suppose one product contains:

OnlineStore
├── Web frontend
├── Orders API
└── Payments API

You don't necessarily need:

❌ Project: Frontend
❌ Project: Orders
❌ Project: Payments

You could have:

Project: OnlineStore
├── Team: Frontend
├── Team: Orders
└── Team: Payments

Projects are appropriate when you need a stronger organizational boundary—for example substantially different products, administration, visibility, or lifecycle.

Teams

A project automatically has a default team, and additional teams can be created within it.

Teams let different groups work within the same project while maintaining their own planning views.

OnlineStore project
       ↓
 ┌─────┼────────┐
 ↓     ↓        ↓
Web   Orders  Payments
Team   Team     Team

This is particularly important for Azure Boards.

Teams can have their own configuration around:

Backlogs
Boards
Sprints
Area paths
Iteration paths
Area paths

Think:

Area path = which work belongs to the team.

For example:

OnlineStore
├── Frontend
├── Orders
└── Payments

The Orders team could be configured to work with:

Area path:
OnlineStore\Orders

Work items assigned to that area can then appear on the Orders team's backlog/board.

Iteration paths

Think:

Iteration path = when the team plans to do the work.

For example:

OnlineStore
├── Sprint 1
├── Sprint 2
└── Sprint 3

So:

Area      → WHO/WHAT part of product
Iteration → WHEN

That's a useful exam mnemonic.

Teams aren't the same as security groups

This is an important distinction given the previous objective.

Azure DevOps Team
→ collaboration/planning unit
→ boards, backlogs, areas, iterations

Security group
→ authorization unit
→ permissions

There is some interaction between these concepts, but don't treat “create a team” and “create a security group” as interchangeable solutions.

- Project → larger organizational/resource boundary.
- Team → collaboration and planning unit inside a project.
- Don't automatically create separate projects just because there are separate development teams.
- Area Path = WHAT/WHERE the work belongs.
- Iteration Path = WHEN the work is planned.
- Teams can have their own boards, backlogs, areas, and sprint configuration.
- Team ≠ security group: teams primarily organize collaboration/planning; security groups primarily organize authorization.
