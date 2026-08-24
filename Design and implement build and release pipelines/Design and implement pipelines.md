# Design and implement build and release pipelines

## Design and implement build and release pipelines

### Select a deployment automation solution, including GitHub Actions and Azure Pipelines

The two named options are:

GitHub Actions

Native to GitHub repositories.
Natural fit when source control, PRs, issues, and CI/CD already live in GitHub.
Workflows are YAML under .github/workflows/.
Strong event-driven model around GitHub events such as push, pull_request, release, and manual dispatch.

Azure Pipelines

Native to Azure DevOps.
Natural fit when the organization already uses Azure Boards, Azure Repos, Azure Artifacts, Environments, approvals/checks, and Azure DevOps governance.
Supports YAML pipelines and classic concepts in older environments.
Strong fit for enterprise deployment controls and Azure DevOps-integrated release workflows.

A useful exam shortcut is:

GitHub-centric delivery
repos + PRs + Actions
        ↓
GitHub Actions

Azure DevOps-centric delivery
Boards + Repos + Artifacts + Environments
        ↓
Azure Pipelines

But this is not a hard rule. Hybrid setups are valid.

For example:

GitHub repo
   ↓
Azure Pipelines
   ↓
Azure deployment

is perfectly reasonable if the organization standardizes CI/CD and governance in Azure DevOps.

The real decision criteria are things like existing platform, authentication model, governance, approvals, environments, reusable templates, hosted/self-hosted execution, package integration, and team familiarity. Source-control location is only one factor. Choose the deployment platform that best fits the surrounding delivery and governance ecosystem.

You’ve now seen both sides:

GitHub Actions: native GitHub events, runs-on, uses, environments, artifact upload/download.
Azure Pipelines: Azure DevOps-native environments, approvals/checks, agent pools, pipeline artifacts.

The selection question is mostly about the surrounding ecosystem and governance requirements, not which YAML syntax you prefer.

- GitHub source does not automatically mean GitHub Actions.
- Azure Pipelines can build/deploy GitHub repositories.
- GitHub Actions and Azure Pipelines can both deploy to Azure.
- Reuse existing environments, approvals, identities, artifact systems, and governance rather than duplicating them.
- Separate jobs do not share local files automatically; use artifacts or another explicit handoff.
needs: expresses job dependencies in GitHub Actions.

Choose based on the broader delivery ecosystem and requirements—not simply where the repository lives.

### Design and implement a GitHub runner or Azure DevOps agent infrastructure, including cost, tool selection, licenses, connectivity, and maintainability
Terminology first:

GitHub Actions   → runner
Azure Pipelines  → agent

Both:
machine/environment that executes pipeline jobs

The major architectural choice is usually:

Managed/hosted
vs.
Self-hosted

For Azure Pipelines, Microsoft-hosted agents give you a fresh managed VM for jobs. GitHub similarly provides GitHub-hosted runners. Self-hosted infrastructure gives you greater control but transfers much more operational responsibility to you.

The exam point explicitly gives us five decision dimensions.

Cost

Hosted infrastructure reduces infrastructure administration, but execution/minute and concurrency entitlements matter.

Self-hosted infrastructure introduces compute and operational costs:

VM/container compute
+ storage/network
+ patching
+ monitoring
+ scaling
+ engineering time

So:

Self-hosted does not automatically mean cheaper.

At high, predictable utilization, self-hosting may make economic sense, but total cost of ownership matters.

Tool selection

Suppose every build requires:

.NET SDK
Node.js
Terraform
Azure CLI
custom proprietary compiler

Hosted images may already contain many common tools. Pipelines can install additional tools at runtime.

But if every job spends 15 minutes installing a large proprietary toolchain, a controlled self-hosted image may be more practical.

There is a trade-off:

Install dynamically
→ easier maintenance
→ slower startup

Preinstall on self-hosted image
→ faster jobs
→ image/tool maintenance responsibility
Licenses

This is easy to overlook.

The pipeline might require licensed software such as a commercial compiler, testing product, or security scanner.

You must consider:

Can it legally run on hosted infrastructure?
How is the license supplied?
Is licensing per user/machine/concurrent execution?
Can ephemeral machines activate it?
How are license credentials protected?

A tool being technically installable does not mean its licensing permits arbitrary hosted execution.

Connectivity

You've already encountered this one:

Hosted runner/agent
       X
Private internal service

If jobs need private connectivity to databases, internal APIs, on-premises systems, or restricted Azure resources, you need execution infrastructure with the necessary network path.

A self-hosted runner/agent inside an appropriate network is one solution.

But remember:

Don't expose an internal service publicly merely to make the pipeline architecture easier.

Maintainability

Hosted:

Provider
→ patches OS
→ refreshes images
→ maintains runner infrastructure

Your team
→ maintains pipeline requirements

Self-hosted:

Your team
→ OS patches
→ runner/agent upgrades
→ tool versions
→ capacity
→ security hardening
→ monitoring
→ cleanup
→ availability

For larger environments, manually maintaining ten special snowflake VMs is usually undesirable. You'd want repeatable provisioning/images and potentially ephemeral or autoscaled execution infrastructure. Don’t compare only compute price; compare total cost and operational overhead.

- Self-hosted is not automatically cheaper; compare total cost of ownership.
- Private connectivity can justify self-hosting.
- Licensing may require stable named machines.
- Manual tool installation across agents causes configuration drift.
- Persistent agents can retain workspace/state between jobs, so cleanup and hardening matter.
- For standard builds with no special needs, hosted agents/runners are often simpler.

Self-hosted infrastructure gives control, but that control must be paired with reproducible configuration, patching, monitoring, scaling, and cleanup.

### Design and implement integration between GitHub repositories and Azure Pipelines

The basic architecture is:

GitHub repository
   ↓
Azure Pipelines integration
   ↓
Pipeline triggered by push / PR
   ↓
Azure Pipelines checks out GitHub source
   ↓
Build / test / deploy
   ↓
Status shown back in GitHub

Azure Pipelines needs permission to do two things:

Receive repository events so it knows when to run.
Fetch the repository source so the agent can build it.

Microsoft currently supports GitHub App, OAuth, and PAT authentication, with the Azure Pipelines GitHub App recommended for CI. It runs using the Azure Pipelines identity rather than a personal GitHub identity, and it integrates with GitHub Checks.

That gives you an important exam distinction:

GitHub App
→ preferred
→ not tied to one developer's personal identity
→ supports GitHub Checks

OAuth / PAT
→ possible
→ tied more closely to a user's identity/credentials

Also remember the security principle you already know: give the connection access only to the repositories it actually requires rather than broadly granting every pipeline access to everything. Microsoft specifically recommends explicit pipeline authorization rather than simply granting a GitHub service connection to all pipelines.

- Prefer a GitHub App/integration identity over a personal PAT when possible.
- Grant access only to the repositories actually required.
- Reading GitHub source is only part of the integration; repository events should trigger CI appropriately.
- Azure Pipelines can report status back to GitHub so the result can become a required PR check.
- GitHub source does not mean you must use GitHub Actions.

Azure Pipelines
→ runs build/test/security logic
→ reports status back to GitHub

GitHub branch protection / ruleset
→ decides whether that status must pass
→ blocks merge if required check fails

Pipeline creates the check; repository policy enforces the check.

- Azure Pipelines can use GitHub as its source repository.
- Prefer a GitHub App/integration identity over a personal PAT when possible.
- The integration covers source checkout, event triggers, and PR status reporting.
- GitHub source does not require GitHub Actions.
- Least privilege still applies to repository access.
- A PR check existing is not the same as being required.
- GitHub branch protection/rulesets determine whether Azure Pipeline results block merging.
- Connected your GitHub repository to Azure Pipelines.
- Verified Build.Repository.Name and Build.SourceVersion.
- Confirmed a GitHub push automatically triggered Azure Pipelines.
- Confirmed a GitHub PR triggered Azure Pipelines.
- Saw the Azure Pipeline status appear directly on the GitHub PR.

That gives you the full integration loop:

GitHub PR
   ↓
Azure Pipelines
   ↓
Build/test/gates
   ↓
Status returned to GitHub
   ↓
GitHub ruleset
   ↓
Merge allowed or blocked

### Develop and implement pipeline trigger rules

A trigger rule answers:

Which repository event, branch, path, tag, or schedule should start this pipeline?

Typical trigger types include:

- Push/CI triggers → run when code is pushed.
- PR triggers → validate pull requests before merge.
- Path filters → run only when relevant files change.
- Branch filters → run only for selected branches.
- Tag triggers → run for version/release tags.
- Scheduled triggers → run at a set time.
- Manual triggers → run only when explicitly started.

The design goal is to avoid both extremes:

Too broad
→ every tiny change runs every pipeline
→ cost + noise + slow feedback

Too narrow
→ important changes aren't validated
→ risk

A common optimization is path filtering.

Imagine a monorepo:

/frontend
/backend
/docs
/infrastructure

If a pipeline only builds the backend, it usually shouldn't run when only /docs changes.

- CI/push trigger → start from commits/pushes.
- PR trigger → validate proposed changes before merge.
- Branch filter → restrict triggers to branches such as main.
- Path filter → run only when relevant files change.
- Tag trigger → useful for release workflows such as v*.
- Scheduled trigger → run based on time rather than repository activity.
- Manual execution → explicitly initiated rather than event-driven.
- Condition ≠ trigger → conditions control stages/jobs/steps after a pipeline has started.

### Develop pipelines by using YAML
A useful Azure Pipelines hierarchy is:

Pipeline
│
├── Stage: Build
│   ├── Job: BuildApp
│   │   ├── Step
│   │   └── Step
│   └── Job: BuildDocs
│
├── Stage: Test
│   └── Job: Tests
│
└── Stage: Deploy
    └── Deployment job

The hierarchy is:

stages
  ↓
jobs
  ↓
steps

A step is an individual operation, such as:

steps:
- script: python -m pytest

A job groups steps and executes them on an agent:

jobs:
- job: UnitTests
  pool:
    vmImage: ubuntu-latest
  steps:
  - script: python -m pytest

A stage is a higher-level boundary, commonly used for phases such as:

Build → Test → DeployTest → DeployProd

Stages are particularly useful for dependencies, approvals, conditions, and separating lifecycle phases.

Dependencies

By default, stages generally progress sequentially, but YAML lets you explicitly model dependencies with:

dependsOn:

For example:

Build
  ↓
Test
  ↓
Deploy

could be explicitly represented as:

- stage: Test
  dependsOn: Build

- stage: Deploy
  dependsOn: Test

But dependencies aren't always linear.

You might want:

            ┌→ UnitTests
Build ──────┤
            └→ IntegrationTests

where independent jobs can execute in parallel.

This is an important design principle:

Don't create artificial dependencies between jobs that can safely execute independently.

That can reduce pipeline duration.

Parameters
→ chosen/expanded when pipeline structure is being prepared
→ useful for controlling pipeline configuration

Variables
→ values used while the pipeline runs
→ useful for runtime configuration

Variables are more appropriate for values used during execution, such as configuration values, calculated values, or values supplied through variable groups.

Another important distinction is when expressions are evaluated:

${{ ... }}  → template/compile-time expression
$(...)      → macro/runtime variable syntax
$[...]      → runtime expression

You don't need to memorize every edge case immediately, but you should recognize that:

${{ parameters.environment }}

and:

$(environment)

are not simply two spellings of the same mechanism.

- Parameters vs variables: parameters are useful when pipeline structure/configuration is decided before runtime.
- Compile-time vs runtime: ${{ ... }} is not the same as $(...).
- Trigger vs condition: triggers decide whether the pipeline starts; conditions decide whether a stage/job/step runs.
- Dependencies should reflect real dependencies: unnecessary serialization slows pipelines.
- Central templates reduce YAML duplication and configuration drift.
- A deployment condition should usually include success logic too, not just environment == prod.