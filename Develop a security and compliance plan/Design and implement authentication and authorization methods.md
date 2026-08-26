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