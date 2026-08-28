# Develop a security and compliance plan

## Design and implement a strategy for managing sensitive information in automation

### Implement and manage secrets, keys, and certificates by using Azure Key Vault
We've already established two important ideas:

Don't put secrets in YAML/source control.

Managed identity
→ authentication

RBAC
→ authorization

The new objective is specifically about Azure Key Vault as the secure store for secrets, keys, and certificates, including how automation retrieves and manages them.

Secrets vs keys vs certificates

These three Key Vault object types serve different purposes.

Azure Key Vault
├── Secrets
│   → sensitive values
│   → passwords, API keys, connection strings
│
├── Keys
│   → cryptographic keys
│   → encryption, signing, verification
│
└── Certificates
    → X.509 certificates
    → TLS/authentication scenarios
    → certificate lifecycle management

A common exam trap is treating everything sensitive as a secret.

Suppose an application needs to encrypt data using an RSA key. You could technically store arbitrary information as a secret, but Key Vault Keys are designed for cryptographic operations and allow the private key material to remain protected by Key Vault.

Think:

Secret = retrieve a value. Key = perform cryptographic operations. Certificate = manage an X.509 identity/certificate lifecycle.

Key Vault separates management plane and data plane

This distinction is important.

Someone might be able to manage the Key Vault Azure resource without automatically being allowed to read its secrets.

Conceptually:

Management plane
→ create/configure/delete the vault

Data plane
→ read secrets
→ use keys
→ access certificates

With Azure RBAC, different roles represent these capabilities.

For example:

Key Vault Reader
→ can read metadata
→ cannot read secret values

Key Vault Secrets User
→ can read secret contents

Key Vault Crypto User
→ cryptographic operations using keys

Key Vault Administrator
→ broad data-plane administration

So Reader is another potential exam trap:

Reading the Key Vault resource/metadata does not necessarily mean reading secret values.

Applications shouldn't authenticate with another secret if avoidable

Imagine:

App Service
   ↓
password stored in config
   ↓
Key Vault
   ↓
retrieve another password

We've protected one secret by introducing another secret.

A better Azure-hosted design is:

App Service
   ↓
Managed identity
   ↓
Entra authentication
   ↓
Key Vault RBAC
   ↓
Secret

No Key Vault credential needs to be stored by the application.

This directly connects to the managed-identity lab you completed earlier.

Secret versions

Key Vault objects are versioned.

If you set:

DbPassword = password-v1

and later update it:

DbPassword = password-v2

Key Vault creates another version rather than simply treating the previous value as though it never existed.

Conceptually:

DbPassword
├── Version 1
├── Version 2
└── Version 3 ← current

Applications can retrieve the current version, while version-specific references can intentionally pin a particular version.

This matters for rotation.

Rotation and expiration

Putting a password into Key Vault doesn't magically solve its lifecycle.

You still need a strategy for:

creation
   ↓
secure storage
   ↓
access
   ↓
rotation
   ↓
expiration
   ↓
revocation/deletion

Where possible, eliminating credentials entirely with managed identity/workload identity federation is preferable to continuously managing passwords.

But when a secret genuinely exists, Key Vault gives you a controlled place to manage it.

- Secrets → passwords, connection strings, API credentials.
- Keys → cryptographic operations such as signing/encryption.
- Certificates → X.509/TLS certificate lifecycle.
- Managed identity is preferable for Azure-hosted workloads so you don't store another credential just to access Key Vault.
- Management-plane roles like Owner don't automatically grant secret-value access when RBAC authorization is enabled.
- Use the narrowest appropriate data-plane role and scope.
- Secrets are versioned; versionless retrieval usually follows the latest enabled version, while version-specific references stay pinned.


### Implement and manage secrets and secretless authentication (for example, workload identity federation/OpenID Connect) in GitHub Actions and Azure Pipelines
This objective connects several things you've already practiced:

Key Vault
→ store secrets securely

Managed identity
→ Azure workload without stored credentials

Azure DevOps service connection + WIF
→ pipeline authenticates without client secret

GITHUB_TOKEN
→ GitHub workflow gets short-lived GitHub credentials

The new focus is choosing between storing a secret and eliminating the secret entirely through workload identity federation / OpenID Connect (OIDC).

Traditional secret-based pipeline authentication

Historically, a pipeline authenticating to Azure might use a service principal with:

Tenant ID
Client ID
Client secret

The secret has to be stored somewhere:

GitHub Actions / Azure Pipelines
        ↓
stored client secret
        ↓
Entra service principal
        ↓
Azure

Even if the secret is safely stored as a GitHub Actions secret or Azure DevOps secret variable, it still has a lifecycle:

create → store → protect → rotate → expire → revoke
Secretless authentication

With workload identity federation, there is no long-lived client secret to store.

Instead:

GitHub Actions / Azure Pipelines
        ↓
short-lived OIDC token
        ↓
Microsoft Entra ID
   validates trust
        ↓
short-lived Azure access token
        ↓
Azure

The important word is trust.

Microsoft Entra ID is configured to trust identity assertions from a particular external workload under particular conditions.

For GitHub Actions, that trust can be constrained by information such as the repository and branch/environment.

Conceptually:

"Trust GitHub tokens matching:
 organization = Contoso
 repository   = orders-api
 branch       = main"

A workflow matching that federated credential can request an Azure token without possessing an Azure client secret.

Why this is safer

Compare:

CLIENT SECRET

Steal secret
→ potentially usable until expiration/revocation

with:

OIDC / WIF

Workflow proves identity
→ short-lived token
→ Entra validates configured federation
→ short-lived Azure token

You substantially reduce long-lived credential management and leakage risk.

This leads to an important exam principle:

If workload identity federation is supported, prefer eliminating a long-lived secret rather than merely storing that secret more securely.

Key Vault is excellent for secrets that genuinely must exist. It doesn't mean every authentication scenario should introduce a secret.

GitHub Actions

GitHub Actions can request an OIDC token when the workflow has:

permissions:
  id-token: write
  contents: read

id-token: write is an important exam clue.
It allows the workflow to request an OIDC token. It does not mean:

“Give the workflow write access to Azure.”

Azure authorization remains separate.

Conceptually:

id-token: write
→ workflow may request OIDC identity token

Entra federated credential
→ determines whether that identity is trusted

Azure RBAC
→ determines what it can do in Azure

The workflow can then use Azure login with federation rather than supplying a client secret.

Azure Pipelines

You've actually already implemented this side.

Your lab used:

- task: AzureCLI@2
  inputs:
    azureSubscription: 'az400-wif-rg-sc'

Behind that:

Azure Pipeline
        ↓
Service connection
        ↓
Workload identity federation
        ↓
Entra identity
        ↓
Azure RBAC

There was no Azure client secret in your YAML.

So for Azure Pipelines, think:

Service connection is the Azure DevOps abstraction; workload identity federation can be the authentication mechanism underneath it.

Pipeline secrets still matter

Secretless authentication doesn't mean:

“Pipelines never need secrets anymore.”

Suppose your build calls an external vendor that only supports an API key.

Then the secret genuinely exists.

For GitHub Actions, it might be stored as an appropriate GitHub secret and referenced as:

"${{ secrets.VENDOR_API_KEY }}"

Azure Pipelines can similarly use secret variables, variable groups, or Key Vault integration depending on the requirement.

The design decision therefore becomes:

Can authentication be secretless?
        │
       YES
        ↓
OIDC / WIF

       NO
        ↓
Store secret securely
+ least privilege
+ don't expose in logs
+ rotate
+ minimize lifetime/scope

GitHub Actions on main
        ↓
id-token: write
        ↓
GitHub issues short-lived OIDC token
        ↓
Entra checks federated credential
  issuer + subject + audience
        ↓
Entra issues Azure access token
        ↓
Azure RBAC
Contributor @ lab resource group
        ↓
Azure resources

- Secret management → securely store a credential that must exist.
- Secretless authentication → remove the long-lived credential entirely where federation is supported.
- GitHub Actions OIDC → id-token: write lets the workflow request a GitHub OIDC token.
- Entra federated credential → defines which GitHub workload is trusted.
- Azure RBAC → defines what that identity may do.
- Azure Pipelines → typically uses a service connection with workload identity federation underneath.
- id-token: write does not grant Azure permissions.
- Key Vault does not make an API key secretless; it only stores the secret securely.
- Client ID, tenant ID, and subscription ID are identifiers, not secrets.
- Federated credential = authentication trust.
- Azure RBAC = authorization.
- A federated subject can be narrowly scoped to a repo/branch/environment, reducing blast radius.

### Design and implement a strategy for managing sensitive files during deployment, including Azure Pipelines secure files
This objective builds on Key Vault and pipeline secrets, but the important new distinction is sensitive values vs sensitive files.

You've already worked with:

Secret
→ API key
→ password
→ connection string

Secretless authentication
→ OIDC / WIF

But deployment tooling sometimes requires an actual file.

Examples include:

Signing certificate (.p12/.pfx)
SSH private key
Provisioning profile
License file
Configuration file containing sensitive data

Putting these directly into Git is usually inappropriate.

Azure Pipelines Secure Files

Azure DevOps provides Secure Files in the pipeline Library.

Conceptually:

Sensitive file
      ↓
Azure DevOps Library
      ↓
Secure Files
      ↓
authorized pipeline
      ↓
downloaded temporarily to agent
      ↓
deployment/task uses file

Secure files are protected resources. You can control which pipelines are authorized to use them, similar to the service-connection authorization boundary we encountered earlier.

The YAML doesn't contain the contents of the file.

A pipeline can use the DownloadSecureFile task:

- task: DownloadSecureFile@1
  name: deploymentCertificate
  inputs:
    secureFile: 'deployment-cert.pfx'

Then later tasks can reference its temporary path:

$(deploymentCertificate.secureFilePath)

The important idea is:

secureFilePath gives the job a temporary local path to the downloaded secure file.

Azure Pipelines removes the secure file from the agent when the job finishes.

Secure File vs secret variable

This is probably the most important exam distinction.

If an application needs:

API_TOKEN=abc123

that's naturally a secret value.

If a deployment tool requires:

company-signing-cert.pfx

that's naturally a sensitive file.

Don't encode an entire binary certificate into a giant secret variable merely because secret variables exist.

Sensitive scalar value
→ secret / Key Vault

Sensitive file required by tooling
→ Secure Files
Secure File vs Key Vault certificate

There's some overlap here.

Suppose Azure-native infrastructure can consume a certificate directly from Key Vault. Key Vault may be the stronger lifecycle-management solution.

But suppose a build tool literally requires:

sign-tool --certificate ./certificate.pfx

The agent needs an actual file. Secure Files provides a straightforward mechanism for securely delivering that file to the job.

So don't memorize:

“certificate = always Secure Files.”

Instead ask:

Does the deployment/build process require an actual file on the agent?

Don't copy it into the artifact

Here's an easy trap:

Secure Files
    ↓
download cert.pfx
    ↓
copy into $(Build.ArtifactStagingDirectory) ❌
    ↓
publish artifact ❌

You've just taken something protected by Secure Files and put it into an ordinary pipeline artifact.

The safe lifecycle should be closer to:

Authorize
   ↓
Download when needed
   ↓
Use temporarily
   ↓
Job ends / cleanup
File + password

A .pfx file can itself be password protected.

That produces two separate sensitive things:

certificate.pfx
→ Secure File

PFX_PASSWORD
→ secret variable / Key Vault

Don't store:

pfxPassword: "SuperSecret123"

just because the .pfx itself is protected.

Use sensitive files temporarily; never promote them into normal build artifacts unless that storage mechanism is explicitly designed to protect them.

- Secret value → Key Vault / secret variable
- Sensitive file required on the agent → Azure Pipelines Secure Files
- Secretless auth available → prefer OIDC/WIF over storing a credential at all
- Don’t put sensitive files in Git.
- Don’t copy a Secure File into a normal published artifact.
- A .pfx file and its password are separate concerns: Secure File for the file, secret store for the password.
- Pipeline YAML referencing a Secure File does not automatically authorize access to it.
- If the consumer can use Key Vault directly, that may be preferable to materializing a file on the agent.

### Design pipelines to prevent leakage of sensitive information
This objective ties together Key Vault, Secure Files, secret variables, and OIDC/WIF, but the new focus is what happens after sensitive information enters a pipeline.

A secret can be stored perfectly securely and still leak during execution:

Key Vault ✅
   ↓
Pipeline retrieves secret
   ↓
script prints secret ❌
   ↓
pipeline log

So think about the entire lifecycle:

Retrieve → Use → Prevent exposure → Clean up
Logs are a major leakage path

Consider:

echo "$API_KEY"

or debugging such as:

set -x
curl -H "Authorization: Bearer $TOKEN" ...

set -x is especially dangerous because the shell prints expanded commands, potentially exposing credentials.

Azure Pipelines supports secret variables and attempts to mask their values in logs:

actual value: abc123...
log output:   ***

But masking is a defense-in-depth mechanism, not permission to print secrets deliberately.

A good rule is:

Never intentionally write sensitive values to logs, even when masking is enabled.

Don't pass secrets on command lines unnecessarily

Suppose a tool supports:

tool --password "$PASSWORD"

The secret may become visible through command tracing, process inspection, error output, or tooling diagnostics.

When supported, prefer safer mechanisms such as environment variables, stdin, or dedicated credential mechanisms.

For Azure Pipelines, a secret variable can be explicitly mapped into the environment of only the step that needs it:

- bash: |
    ./deploy.sh
  env:
    API_TOKEN: $(ApiToken)

This is preferable to making sensitive information broadly available throughout the pipeline.

Think:

Minimize both scope and lifetime of secret exposure.

Don't transform secrets and assume masking follows

A particularly useful exam trap:

Secret = MySecret123

Pipeline transforms it
→ Base64
→ URL encoding
→ substring
→ structured output

Don't assume Azure Pipelines will automatically recognize every transformed representation as sensitive.

For example:

echo "$TOKEN" | base64

can leak the credential in a different representation.

Masking the original secret does not make arbitrary transformations safe to print.

Artifacts and files

You've already discovered this with Secure Files.

Leakage isn't limited to logs:

Sensitive information
├── Logs
├── Pipeline artifacts
├── Test results
├── Debug output
├── Temporary files
├── Environment variables
└── Source/generated configuration

For example, a build might generate:

appsettings.generated.json

containing a database password and then accidentally execute:

PublishPipelineArtifact
→ entire working directory

Now the secret has escaped into a retained artifact.

Secretless is still the strongest option where available

Our GitHub OIDC lab illustrates an important principle:

Best:
credential doesn't exist
→ OIDC/WIF

Next:
credential exists
→ retrieve securely only when required
→ minimize exposure
→ don't log/publish it
→ clean it up

You can't accidentally print an Azure client secret that your pipeline never possesses.

## Automate security and compliance scanning

### Design a strategy for security and compliance scanning, including dependency, code, secret, and licensing scanning
This objective shifts from protecting credentials to automatically detecting security and compliance problems before software is released.

For AZ-400, separate four scanning categories:

Security & compliance scanning
│
├── Dependency scanning
│   → Are libraries/packages vulnerable?
│
├── Code scanning
│   → Does our source code contain vulnerabilities?
│
├── Secret scanning
│   → Have credentials accidentally entered the repository?
│
└── License scanning
    → Are dependency licenses acceptable for our organization?
Dependency scanning — SCA

Dependency scanning is often called Software Composition Analysis (SCA).

Suppose:

orders-api
└── Newtonsoft.Json 10.x
       ↓
known vulnerability

Your own source code could be perfectly written, but the application is still vulnerable because of a third-party dependency.

Dependency scanners therefore compare dependency information against vulnerability databases.

This connects to lock files you've encountered before:

package-lock.json
packages.lock.json
poetry.lock
etc.

Lock files help scanners understand the resolved versions actually being used, not merely broad version ranges.

A good strategy usually scans dependencies:

PR
→ identify vulnerable dependency before merge

default branch
→ continuously assess current codebase

scheduled scan
→ discover newly disclosed vulnerabilities

That last one matters: code can become vulnerable without changing.

Monday:
Dependency 1.2.3 → no known vulnerability

Thursday:
new CVE published for 1.2.3

Your Git repository changed?
→ No

Your security risk changed?
→ Yes

So relying exclusively on PR scans can miss newly discovered vulnerabilities.

Code scanning — SAST

Code scanning commonly involves Static Application Security Testing (SAST).

It analyzes your source/code structure for security problems such as unsafe data handling or vulnerable patterns.

Conceptually:

Your source code
      ↓
Static analysis
      ↓
security findings

In GitHub, CodeQL is an important example.

Don't confuse this with dependency scanning:

CodeQL / SAST
→ problem in code you wrote

Dependency/SCA
→ problem in code you depend on
Secret scanning

This looks for credentials that have accidentally entered source control:

AZURE_CLIENT_SECRET=...
GitHub PAT
AWS access key
private key
API token

This complements the secret-management work we just completed.

Key Vault protects correctly managed secrets.

Secret scanning asks:

Did somebody put a secret somewhere it should never have been?

An important response strategy is:

Secret detected
      ↓
Don't merely delete the Git line
      ↓
Revoke / rotate credential
      ↓
Remove exposure
      ↓
Investigate usage/history

Why?

Because Git has history. Once a real credential has been committed/pushed, you should assume it may have been exposed.

License scanning

This one is about compliance, rather than primarily vulnerabilities.

Suppose your organization allows:

MIT
Apache-2.0
BSD

but has a policy restricting certain licenses.

A dependency can therefore be:

Secure version ✅
No known CVE ✅
License policy violation ❌

So vulnerability scanning alone does not establish dependency compliance.

Where should scans run?

A strong strategy uses several points rather than one enormous security stage just before production:

Developer / PR
→ fast feedback

CI on main
→ enforce policy

Scheduled
→ detect newly disclosed issues

Pre-release/deployment
→ enforce appropriate release gates

But there's a performance trade-off.

You probably don't want a 40-minute exhaustive security suite running on every tiny commit if faster targeted checks can reject obvious problems earlier.

Think:

Fast + high-value checks
→ early

Expensive/comprehensive checks
→ later or scheduled

Critical policy checks
→ block promotion when required

This connects directly to the pipeline concurrency/cost work you did earlier.

Findings need policy

Running scanners isn't enough.

You need to decide what findings do:

Critical vulnerability
→ block?

High vulnerability
→ block?

Medium vulnerability
→ warning?

Forbidden license
→ block?

Detected credential
→ block + revoke?

The answer depends on organizational risk policy.

The exam trap is assuming:

Every finding must always block every pipeline.

A mature strategy considers severity, confidence, environment, exceptions, and risk acceptance.

### Configure Microsoft Defender for Cloud DevOps Security
Now we're moving from individual scanners to a service that gives security teams visibility across the DevOps lifecycle: Microsoft Defender for Cloud DevOps Security.

You've already learned:

npm audit → dependency vulnerabilities
CodeQL    → source-code vulnerabilities
Secret scanning
License scanning

The new question is:

How can Defender for Cloud connect to DevOps environments and surface security posture and findings centrally?

The core model

Think of it roughly as:

GitHub / Azure DevOps
        │
        │ connection
        ▼
Microsoft Defender for Cloud
        │
        ├── DevOps security posture
        ├── Recommendations
        └── Findings / risk visibility

This matters in larger organizations. A security team may have hundreds of repositories spread across GitHub organizations and Azure DevOps organizations.

Rather than inspecting every repository independently, Defender for Cloud can provide a more centralized security view.

DevOps connectors

A key concept for AZ-400 is the DevOps connector.

You establish a connection between Defender for Cloud and a supported DevOps environment such as GitHub or Azure DevOps.

Conceptually:

Defender for Cloud
      │
      └── DevOps connector
              │
              ├── GitHub organization
              │      ├── repo-a
              │      └── repo-b
              │
              └── Azure DevOps organization
                     ├── project-a
                     └── project-b

Don't confuse this with the Azure DevOps service connection we configured earlier.

Azure DevOps service connection
→ Pipeline authenticates TO Azure/resources

Defender for Cloud DevOps connector
→ Defender connects TO the DevOps environment
  for security visibility/integration

That distinction is very exam-friendly.

Defender for Cloud vs your pipeline scanner

Another important distinction:

Defender for Cloud does not mean:

“We no longer need security controls in CI.”

You still want the shift-left controls we discussed:

Developer
    ↓
PR
    ↓
CI security controls
    ↓
Merge

Defender for Cloud adds centralized posture and security management around that DevOps estate.

Think of the two perspectives:

Developer perspective
→ "Is this PR safe to merge?"

Security/platform perspective
→ "What security risks exist across our DevOps estate?"
Infrastructure-as-Code is important here

There's also an important area we didn't emphasize in the previous objective: IaC security.

Imagine a repository contains:

main.bicep
terraform/
ARM templates
Kubernetes manifests

Security scanning can identify infrastructure configuration problems before those configurations become deployed resources.

For example:

IaC template
→ insecure configuration detected
→ developer receives feedback
→ fix before deployment

This extends the shift-left principle from application code to cloud infrastructure definitions.

The security loop

For this objective, keep this model in mind:

Connect DevOps environment
        ↓
Discover repositories
        ↓
Assess DevOps security posture
        ↓
Surface recommendations/findings
        ↓
Remediate
        ↓
Continuously reassess

We'll avoid repeating all the SAST/SCA theory from the previous objective and focus on connectors, centralized posture, recommendations, and Defender integration.

DevOps connector
→ connects Defender to GitHub/Azure DevOps

DevOps security posture
→ centralized view across repositories

Recommendations/findings
→ identify and prioritize security weaknesses

IaC scanning
→ shift infrastructure security left

PR/CI security controls
→ prevent insecure changes before merge

The biggest exam trap is:

Defender for Cloud DevOps Security ≠ Azure DevOps service connection ≠ replacement for CI security scanning.

### Configure GitHub Advanced Security for GitHub and GitHub Advanced Security for Azure DevOps
This overlaps with the scanning objective we just completed, so we'll not repeat SAST/SCA basics. The new focus is configuring the GitHub Advanced Security capabilities themselves, on both GitHub and Azure DevOps.

One terminology update is useful: GitHub now presents Advanced Security capabilities primarily as GitHub Code Security and GitHub Secret Protection. Azure DevOps similarly lets you enable Code Security and/or Secret Protection.

Think:

GitHub Advanced Security capabilities
│
├── Code Security
│   ├── Code scanning / CodeQL
│   ├── Dependency security
│   └── Security overview
│
└── Secret Protection
    ├── Secret scanning
    ├── Push protection
    └── Security overview
GitHub side

You've already configured CodeQL advanced setup in our previous lab.

The important new configuration distinction is:

Default setup
→ GitHub manages CodeQL configuration
→ easiest onboarding
→ less customization

Advanced setup
→ workflow configuration
→ greater control
→ custom triggers/build/query behavior

For most standard repositories, default setup is the simpler starting point. Advanced setup becomes useful when you need customization.

Then there's secret scanning vs push protection:

Secret scanning
→ "A secret exists in the repository."

Push protection
→ "Stop this secret from being pushed."

That distinction is important.

Secret scanning can inspect Git history for exposed credentials. Push protection tries to stop supported secrets from entering the repository in the first place.

So:

Detection:
developer → push secret → repository → ALERT

Prevention:
developer → push secret → BLOCKED
                            ↑
                      push protection

From a security-design perspective, prevention is preferable where practical.

GitHub Advanced Security for Azure DevOps

Here's the part that's probably newer for you.

The same major ideas are available for Azure Repos through GitHub Advanced Security for Azure DevOps:

Azure Repos
│
├── Secret Protection
│   ├── repository secret scanning
│   └── push protection
│
└── Code Security
    ├── dependency scanning
    └── CodeQL code scanning

Microsoft currently allows these products to be enabled at repository, project, or organization scope.

That's a configuration/governance decision:

Repository
→ selective adoption

Project
→ consistent protection across project repos

Organization
→ broad centralized rollout

There are also separate options for automatically enabling protection for future repositories/projects. Enabling existing repositories does not necessarily mean you've configured future repositories automatically.

That's a good exam trap.

Azure DevOps scanning configuration

Azure DevOps now has default and advanced CodeQL setup too.

Default setup requires no CodeQL pipeline YAML and currently scans the default branch. Advanced setup puts CodeQL tasks into Azure Pipelines and gives control over branches, builds, agents, queries, etc.

With advanced setup, recognize this pattern:

steps:
- task: AdvancedSecurity-Codeql-Init@1

 build steps when required

- task: AdvancedSecurity-Codeql-Analyze@1

Don't memorize YAML character-for-character. Understand:

Initialize CodeQL
      ↓
Build/analyze application as required
      ↓
Perform CodeQL analysis
      ↓
Alerts in Advanced Security

Dependency scanning is also pipeline-based in Azure DevOps, while secret repository scanning starts in the background when Secret Protection is enabled.

PR enforcement

This is particularly relevant to AZ-400.

Finding vulnerabilities is one thing:

scan → alert

Enforcement adds:

PR
 ↓
security scan
 ↓
security status check
 ↓
policy violated?
 ↓
BLOCK MERGE

GitHub Advanced Security for Azure DevOps supports PR annotations and security status checks; status checks can prevent merging when relevant security findings violate the configured policy.

This connects directly to your previous answer:

Security scanning still needs to be addressed even if functional tests pass.

### Integrate GitHub Advanced Security with Microsoft Defender for Cloud
This objective connects the previous two objectives, so we'll focus specifically on the integration path rather than repeat CodeQL, secret scanning, SCA, or Defender basics.

You've already built these two mental models:

GitHub Advanced Security
→ produces security findings close to developers

Microsoft Defender for Cloud DevOps Security
→ centralized security posture across DevOps environments

Now combine them:

GitHub / Azure DevOps repositories
        │
        ├── CodeQL findings
        ├── dependency findings
        └── secret findings
                 │
                 ▼
        DevOps connector
                 │
                 ▼
      Microsoft Defender for Cloud
                 │
                 ▼
     Centralized DevOps security view

The important architectural idea is not to replace GitHub Advanced Security with Defender.

They serve different perspectives:

GitHub Advanced Security
→ developer/repository security workflow
→ detect and remediate near the code

Defender for Cloud
→ cloud/security-team perspective
→ correlate and prioritize DevOps security posture
Why integrate them?

Imagine an enterprise has:

GitHub
├── 150 repositories

Azure DevOps
├── 200 repositories

Azure
├── production workloads

A developer cares about:

"What CodeQL alerts exist in my repository?"

The security team may care about:

"Which DevOps findings are associated with resources that could affect our critical Azure workloads?"

That's where integrating DevOps security information into the broader Defender for Cloud security context becomes valuable.

Connector is still fundamental

Remember our previous lab:

Defender for Cloud
      ↓
DevOps connector
      ↓
GitHub / Azure DevOps

That connector is the foundation for Defender's DevOps visibility.

This is still not an Azure DevOps service connection:

Service connection
Pipeline → Azure

DevOps connector
Defender for Cloud → DevOps environment
Findings remain actionable near the code

Another important design principle:

Developer
→ works with findings in GitHub/Azure DevOps

Security team
→ gains centralized visibility in Defender

Integration doesn't mean developers should abandon their native repository security workflow and remediate everything from Defender.

Think of Defender as adding security context and centralized prioritization.

Cloud-to-code context

One of the particularly useful ideas behind the integration is connecting development security with cloud security.

Conceptually:

Code repository
      ↓
pipeline
      ↓
Azure workload

If security tooling can understand relationships across those layers, security teams can prioritize findings with better context.

For example, two repositories might both have security findings:

demo-tool
→ vulnerability

payments-api
→ vulnerability
→ associated with important production workload

Raw severity might be similar, but the risk context can make the second finding much more urgent.

This fits the Defender for Cloud approach you've already learned:

Prioritize using risk/severity and business/cloud context, not merely count alerts.

GitHub Advanced Security
→ detects security problems near the code
→ developer-focused remediation
→ PR security feedback/enforcement

DevOps connector
→ integration path into Defender

Defender for Cloud
→ centralized security posture
→ cloud/workload context
→ organization-wide prioritization

### Automate container scanning, including scanning container images and configuring an action to run CodeQL analysis in a container
This objective introduces containers as another security boundary. We already know CodeQL/SAST and dependency scanning, so we'll focus on what's new.

There are actually two different ideas hidden in this objective:

A. Scan a container image
   → "Is the image we're shipping vulnerable?"

B. Run CodeQL analysis in a container
   → "Can our code-analysis environment itself be containerized?"

Don't confuse them.

Container image scanning

Consider:

FROM node:20

COPY . /app
RUN npm ci

Your application might pass CodeQL and npm audit, but the final image contains much more:

Container image
│
├── Base OS packages
│   ├── openssl
│   ├── libc
│   └── ...
│
├── Runtime
│   └── Node.js
│
├── Application dependencies
│
└── Application

A vulnerability could therefore exist in the base image or OS package even though your application dependency scan passes.

That's why container-image scanning matters:

Build image
    ↓
Scan resulting image
    ↓
Evaluate vulnerabilities against policy
    ↓
Pass → publish/deploy
Fail → stop promotion

This is another example of scanning the artifact you're actually going to deploy.

Scan before and after registry push

There are useful security controls at different stages:

Source
→ SAST / dependency / secret scanning

Build
→ container image

Image
→ vulnerability scan

Registry
→ ongoing assessment

Deployment
→ only approved images promoted

Why scan in CI and potentially monitor images in a registry?

Same reason we discussed scheduled dependency scanning:

An image doesn't need to change for a new CVE to be discovered later.

So:

CI image scan
→ stop known-vulnerable image from being released

Registry/continuous assessment
→ detect vulnerabilities disclosed after image was built
Container registries

In Azure you'll naturally encounter Azure Container Registry (ACR).

Don't make this mistake:

"Image successfully pushed to ACR"
        ≠
"Image is secure"

A registry stores/distributes the artifact. Security assessment is a separate concern.

Microsoft Defender for Cloud's container security capabilities can provide vulnerability assessment for container images in supported registry/container scenarios.

Severity policy

Just like earlier scanning:

Scanner finds CVE
      ↓
Critical?
High?
Medium?
      ↓
Security policy
      ↓
block / warn / accept

Again, scanner execution and policy enforcement are different things.

CodeQL analysis in a container

Now the second half.

Normally you've seen:

GitHub runner
→ CodeQL initialize
→ build
→ CodeQL analyze

But sometimes the software must be built inside a particular container because it requires a specialized build environment:

GitHub Actions runner
       ↓
Container
├── compiler
├── SDK
├── dependencies
└── application build

The challenge is ensuring CodeQL can correctly observe/analyze the build occurring in that container.

Conceptually:

CodeQL initialization
        ↓
containerized build
        ↓
CodeQL captures required build information
        ↓
CodeQL analysis

This is especially relevant to compiled languages, where CodeQL may need to observe compilation.

Don't confuse:

CodeQL analyzing source built in container
→ SAST

Scanning the resulting Docker image
→ container vulnerability scanning

They protect different layers.

Security layers

A useful AZ-400 model is:

SOURCE CODE
→ CodeQL

APPLICATION DEPENDENCIES
→ dependency/SCA

CONTAINER IMAGE
→ image vulnerability scanning

RUNNING CLOUD ENVIRONMENT
→ Defender/cloud security controls

Passing one layer doesn't prove the next layer is safe. Running CodeQL analysis in a container ≠ scanning a container image.

### Automate analysis of vulnerabilities of open-source components by using Dependabot alerts
This overlaps strongly with the dependency/SCA scanning we've already done, so we won't repeat what a dependency vulnerability is.

The new focus is Dependabot and how GitHub automates detection and remediation of vulnerable open-source dependencies.

You've already used:

npm audit
→ scan dependency graph
→ find known vulnerabilities
→ pipeline can fail

Dependabot adds GitHub-native, ongoing dependency security management.

Think of three related capabilities:

Dependency graph
→ What dependencies does this repo use?

Dependabot alerts
→ Which dependencies have known vulnerabilities?

Dependabot security updates
→ Can GitHub propose a PR upgrading a vulnerable dependency?

There's also Dependabot version updates, which is easy to confuse with security updates:

Security update
→ vulnerable dependency
→ PR attempts to remediate vulnerability

Version update
→ newer dependency version exists
→ PR keeps dependency current
→ vulnerability not required
Dependency graph comes first

GitHub needs to understand the repository's dependencies.

For your npm lab:

package.json
package-lock.json
       ↓
Dependency graph
       ↓
lodash version identified
       ↓
GitHub Advisory Database
       ↓
known vulnerable?
       ↓
Dependabot alert

So Dependabot alerts are closely tied to GitHub's understanding of the dependency graph.

Alerts vs updates

This distinction is important:

Dependabot alert
→ "You have a vulnerable dependency."

Dependabot security update
→ "Here's a PR that may fix it."

Detection and remediation automation are separate concepts.

This connects to our earlier npm audit fix --force discussion. Automatically changing dependencies can introduce compatibility problems, so the proposed update should still go through your normal engineering controls:

Dependabot detects vulnerability
        ↓
Dependabot opens security-update PR
        ↓
CI
├── tests
├── security scans
└── other policies
        ↓
review / merge
Why Dependabot is useful alongside CI

Remember this scenario?

Monday: dependency has no known CVE
Friday: new CVE disclosed
Repository hasn't changed

Dependabot alerts are valuable because GitHub can identify newly disclosed vulnerabilities in dependencies represented in the repository's dependency graph. You don't have to wait for a developer to manually run npm audit.

So don't think:

npm audit OR Dependabot

Think:

CI dependency scanning
→ enforcement during development

Dependabot alerts
→ ongoing GitHub-native vulnerability awareness

Dependabot security updates
→ automated remediation PRs
Don't confuse alerts with version updates

A classic exam distinction:

"We need to know when an open-source
dependency has a known vulnerability."
→ Dependabot alerts

"We want automatic PRs to remediate
vulnerable dependencies."
→ Dependabot security updates

"We want monthly PRs keeping all
dependencies current."
→ Dependabot version updates

Vulnerability → security update
Keep packages current → version update

Dependabot alerts
→ detects vulnerable dependencies

Security updates
→ opens PRs to remediate vulnerable dependencies

Grouped security updates
→ groups compatible security fixes into fewer PRs

Version updates
→ opens routine update PRs even without vulnerabilities

Dependabot alerts
→ detect known vulnerable dependencies

Dependabot security updates
→ PRs specifically to remediate vulnerabilities

Grouped security updates
→ combine compatible security fixes into fewer PRs

Dependabot version updates
→ routine dependency updates
→ vulnerability not required

Dependabot = open-source dependency risk. CodeQL = your source-code security analysis.
