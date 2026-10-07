# Additional Microsoft Learn References for AZ-400

> These links are selected primarily to become familiar with the structure of the official Microsoft documentation for use during the exam.  
> The goal is not to memorize every page, but to know **where to look quickly**.

---

## 1. Azure Pipelines — Core Reference

- [Azure Pipelines documentation](https://learn.microsoft.com/en-us/azure/devops/pipelines/)
- [Azure Pipelines YAML schema](https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/)
- [Azure Pipelines conditions](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/conditions)
- [Azure Pipelines expressions](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/expressions)
- [Azure Pipelines templates](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/templates)
- [Azure Pipelines variables](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/variables)
- [Variable groups](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups)

### Particularly useful to recognize

```text
Pipeline
 ├─ stages
 ├─ jobs
 ├─ steps
 ├─ conditions
 ├─ dependsOn
 ├─ variables
 └─ templates
```

For scenario questions involving YAML syntax, this is one of the most useful documentation areas to know how to navigate.

---

## 2. Environments, Approvals, and Checks

- [Approvals and checks](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/approvals)
- [Create and target Azure Pipelines environments](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/environments)

Know where to find information about:

- approvals
- branch control
- environment checks
- exclusive locks
- protected resources
- deployment history

Mental model:

```text
Artifact
   ↓
Environment
   ↓
Checks / Approval
   ↓
Deployment
```

Remember:

> Approvals and checks are associated with protected resources/environments, rather than simply being another script inside the pipeline.

---

## 3. Agents, Runners, and Parallel Jobs

- [Azure Pipelines agents](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/agents)
- [Microsoft-hosted agents](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/hosted)
- [Parallel jobs in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/licensing/concurrent-jobs)

These pages are particularly useful because AZ-400 explicitly tests decisions involving:

- Microsoft-hosted vs self-hosted agents
- connectivity
- custom software
- agent maintenance
- capacity
- licensing
- concurrency
- parallel jobs

Useful distinction:

```text
Agent
= machine/process executing a job

Parallel job
= capacity/licensing allowing another job to run simultaneously
```

---

## 4. Testing and Code Coverage

- [Test in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/test/)
- [Publish Test Results task](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/publish-test-results-v2)
- [Code coverage in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/test/review-code-coverage-results)

Know where to find:

```text
Run tests
Publish test results
Publish code coverage
Coverage policies
Test result formats
```

Important exam distinction:

```text
Executing tests
        ≠
Publishing test results
        ≠
Publishing code coverage
```

---

## 5. Pipeline Artifacts

- [Publish and download pipeline artifacts](https://learn.microsoft.com/en-us/azure/devops/pipelines/artifacts/pipeline-artifacts)

Use this documentation area for questions involving:

- publishing build output
- downloading artifacts in later stages
- promoting the same artifact
- retention
- artifact dependencies

Remember:

```text
Build once
    ↓
Versioned artifact
    ↓
Dev
    ↓
Test
    ↓
Production
```

---

## 6. Branch Policies and Repository Security

- [About branches and branch policies](https://learn.microsoft.com/en-us/azure/devops/repos/git/branch-policies-overview)
- [Set and manage branch policies](https://learn.microsoft.com/en-us/azure/devops/repos/git/branch-policies)
- [Secure repositories and pull requests](https://learn.microsoft.com/en-us/azure/devops/repos/git/secure-repositories-pull-requests)
- [Set Git repository permissions](https://learn.microsoft.com/en-us/azure/devops/repos/git/set-git-repository-permissions)
- [Set Git branch permissions](https://learn.microsoft.com/en-us/azure/devops/repos/git/branch-permissions)

Know where to look up:

- required reviewers
- build validation
- status checks
- comment resolution
- repository permissions
- branch permissions
- force push
- policy bypass

Important distinction:

```text
Permission
= may this person perform the operation?

Policy
= what conditions must the operation satisfy?
```

---

## 7. Azure Artifacts

- [Azure Artifacts documentation](https://learn.microsoft.com/en-us/azure/devops/artifacts/)
- [Azure Artifacts concepts](https://learn.microsoft.com/en-us/azure/devops/artifacts/artifacts-key-concepts)
- [Feed views](https://learn.microsoft.com/en-us/azure/devops/artifacts/feeds/views)
- [Upstream sources](https://learn.microsoft.com/en-us/azure/devops/artifacts/concepts/upstream-sources)

Become familiar with:

```text
Feed
Views
Upstream sources
Package versions
Package promotion
```

Especially recognize:

```text
@Local
@Prerelease
@Release
```

---

## 8. Deployment Strategies and Azure App Configuration

- [Feature management in Azure App Configuration](https://learn.microsoft.com/en-us/azure/azure-app-configuration/concept-feature-management)
- [Manage feature flags](https://learn.microsoft.com/en-us/azure/azure-app-configuration/manage-feature-flags)
- [Azure App Service deployment slots](https://learn.microsoft.com/en-us/azure/app-service/deploy-staging-slots)

The feature-management documentation is worth knowing because the current objective explicitly mentions Azure App Configuration Feature Manager.

Recognize the three broad feature-management scenarios:

```text
Switch
    → on/off

Rollout
    → progressively expose feature

Experiment
    → compare variants/outcomes
```

Also keep straight:

```text
Deployment
= code reaches environment

Release
= users gain access to feature
```

Feature flags can separate the two.

---

## 9. Infrastructure as Code — Bicep

- [Bicep documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/)
- [Bicep overview](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview)
- [Bicep deployment commands](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deploy-cli)
- [Bicep what-if operation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deploy-what-if)

Know how to quickly find:

- modules
- parameters
- outputs
- resource syntax
- deployment scopes
- what-if

Mental model:

```text
Bicep
   ↓
ARM deployment
   ↓
Desired Azure resources
```

---

## 10. Azure Machine Configuration and Desired State

- [Azure Machine Configuration](https://learn.microsoft.com/en-us/azure/governance/machine-configuration/)
- [Azure Automation State Configuration](https://learn.microsoft.com/en-us/azure/automation/automation-dsc-overview)

Useful distinction:

```text
ARM / Bicep
"What resources should exist?"

Machine Configuration / DSC
"How should the machines be configured?"
```

You probably don't need to memorize implementation syntax here; recognize the use cases and know where the documentation lives.

---

## 11. Azure Deployment Environments

- [Azure Deployment Environments documentation](https://learn.microsoft.com/en-us/azure/deployment-environments/)

Remember the core idea:

```text
Platform team
     ↓
Approved environment definitions
     ↓
Developer self-service
     ↓
Governed Azure environment
```

Useful clue:

> Developers need to create standardized, approved environments on demand.

Think:

**Azure Deployment Environments**

---

## 12. Service Connections and Workload Identity Federation

- [Azure Resource Manager service connections](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure)
- [Workload identity federation for Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/release/configure-workload-identity)
- [Microsoft Entra workload identity federation](https://learn.microsoft.com/en-us/entra/workload-id/workload-identity-federation)

Know how this flow works:

```text
Azure Pipeline
      ↓
OIDC token
      ↓
Microsoft Entra ID
      ↓
Short-lived Azure token
      ↓
Azure resources
```

Exam preference when the requirement says:

```text
No stored secrets
No long-lived credentials
```

Think:

**workload identity federation / OIDC**

---

## 13. Azure DevOps Permissions and Access Levels

- [Permissions and groups in Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/security/permissions)
- [About access levels](https://learn.microsoft.com/en-us/azure/devops/organizations/security/access-levels)
- [Add users to Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/add-organization-users)

Be able to distinguish:

```text
Access level
= which Azure DevOps capabilities are available

Permissions
= which operations the user may perform
```

Know the idea behind:

```text
Stakeholder
Basic
security groups
project permissions
repository permissions
```

---

## 14. Azure Key Vault

- [Azure Key Vault documentation](https://learn.microsoft.com/en-us/azure/key-vault/)
- [Azure Key Vault overview](https://learn.microsoft.com/en-us/azure/key-vault/general/overview)

Know the three major object types:

```text
Secrets
Keys
Certificates
```

Also understand the preferred pattern:

```text
Pipeline / Application
        ↓
Managed or federated identity
        ↓
Key Vault
```

rather than embedding credentials in YAML.

---

## 15. Azure Pipelines Secure Files

- [Secure files in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/secure-files)

Useful for protected deployment assets such as:

```text
Certificates
Signing files
Provisioning profiles
Sensitive configuration files
```

Know that a Secure File is a **protected pipeline resource**, not just another repository file.

---

## 16. GitHub Advanced Security for Azure DevOps

- [GitHub Advanced Security for Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security)
- [Configure GitHub Advanced Security](https://learn.microsoft.com/en-us/azure/devops/repos/security/configure-github-advanced-security-features)
- [Code scanning with CodeQL](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning)
- [Dependency scanning](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-dependency-scanning)
- [Secret scanning](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-secret-scanning)

Know this mapping:

```text
Secret scanning
→ exposed credentials

Dependency scanning
→ vulnerable third-party components

CodeQL
→ vulnerabilities in application source
```

Also recognize that CodeQL has:

```text
Default setup
Advanced pipeline-based setup
```

---

## 17. Azure Monitor and Application Insights

- [Azure Monitor documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/)
- [Application Insights overview](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview)
- [Azure Monitor Logs](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/)
- [Kusto Query Language overview](https://learn.microsoft.com/en-us/kusto/query/)

Know where to look for:

```text
Metrics
Logs
Traces
Alerts
KQL
Application Map
Distributed tracing
```

Common KQL operators worth recognizing:

```text
where
project
summarize
count
countif
bin
order by
join
```

---

## 18. Infrastructure and Container Monitoring

- [VM Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-overview)
- [Container Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview)
- [Monitor Azure Storage](https://learn.microsoft.com/en-us/azure/storage/common/storage-insights-overview)

Know roughly which documentation area corresponds to:

```text
VM issue
        → VM Insights

AKS/container issue
        → Container Insights

Application issue
        → Application Insights

Storage issue
        → Storage monitoring

Logs across resources
        → Azure Monitor Logs / Log Analytics
```

---

# Microsoft Learn Exam Navigation Strategy

Microsoft Learn is available during eligible Microsoft role-based certification exams such as AZ-400, but the exam timer continues while you use it.

Therefore:

> **Do not plan to research concepts from scratch during the exam.**

Instead, become familiar with the documentation hierarchy beforehand.

## Pages worth knowing almost by memory

```text
learn.microsoft.com
│
├─ Azure DevOps
│  ├─ Pipelines
│  │  ├─ YAML
│  │  ├─ Agents
│  │  ├─ Process
│  │  ├─ Artifacts
│  │  └─ Test
│  │
│  ├─ Repos
│  │  ├─ Git
│  │  └─ Security
│  │
│  └─ Artifacts
│
├─ Azure
│  ├─ Azure Monitor
│  ├─ Key Vault
│  ├─ App Configuration
│  └─ Resource Manager / Bicep
│
└─ Entra
   └─ Workload identities
```

---

# What I Would Prioritize for Exam Navigation

## Tier 1 — Know very well

These are worth opening several times during your preparation:

1. **Azure Pipelines YAML schema**
2. **Azure Pipelines conditions and expressions**
3. **Azure Pipelines templates**
4. **Approvals and checks**
5. **Azure Pipelines agents**
6. **Branch policies**
7. **Azure DevOps permissions**
8. **GitHub Advanced Security for Azure DevOps**
9. **Workload identity federation**
10. **Azure Monitor / Application Insights**

---

## Tier 2 — Know where they are

You don't need to memorize these pages, but should know how to reach them quickly:

- Azure Artifacts feeds/views/upstream sources
- code coverage
- Secure Files
- deployment slots
- Azure App Configuration feature flags
- Bicep
- Machine Configuration
- Azure Deployment Environments
- VM Insights
- Container Insights

---

## Tier 3 — Understand conceptually

These are less useful to search extensively during the exam:

- Git LFS
- git-fat
- Scalar
- Git recovery commands
- SemVer
- CalVer
- generic branching concepts
- blue-green/canary/ring concepts

You should already know these well enough that searching for them would usually waste exam time.

---

# Useful Search Terms During AZ-400

Instead of searching broad questions, use product-specific terms.

For example:

```text
Azure Pipelines conditions
Azure Pipelines approvals checks
Azure Pipelines parallel jobs
Azure Pipelines Secure Files
Azure Repos bypass policies
Azure Artifacts upstream sources
Azure DevOps Stakeholder access
Azure DevOps workload identity federation
GitHub Advanced Security Azure DevOps CodeQL
Azure App Configuration feature flags
Application Insights distributed tracing
```

This is usually much faster than:

```text
How do I make Azure DevOps do X?
```

---

# Final Recommendation

Use Microsoft Learn during preparation in the same way you intend to use it during the exam:

```text
Read scenario
    ↓
Identify product / feature
    ↓
Predict answer first
    ↓
Open relevant Microsoft Learn area
    ↓
Ctrl+F exact terminology
    ↓
Verify detail
```

The goal should be:

> **Use documentation to verify uncertain implementation details, not to discover the underlying concept during the exam.**
