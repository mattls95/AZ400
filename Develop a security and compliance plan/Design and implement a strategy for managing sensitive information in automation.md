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

