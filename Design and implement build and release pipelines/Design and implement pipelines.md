# Design and implement build and release pipelines

## Design and implement pipelines

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

### Design and implement a strategy for job execution order, including parallelism and multi-stage pipelines
The key rule in GitHub Actions is:

Jobs run in parallel by default unless you create dependencies with needs:.

So this:

jobs:
  unit-tests:
    runs-on: ubuntu-latest

  security-scan:
    runs-on: ubuntu-latest

means:

unit-tests ──────►
                  running in parallel
security-scan ──►

But:

jobs:
  build:
    runs-on: ubuntu-latest

  test:
    needs: build
    runs-on: ubuntu-latest

means:

Build
  ↓
Test

And needs can contain several jobs, which lets you create a fan-out/fan-in graph:

             ┌→ Unit Tests ──────┐
Build ───────┤                    ├→ Deploy
             └→ Security Scan ───┘
deploy:
  needs:
    - unit-tests
    - security-scan

By default, if a required upstream job fails or is skipped, jobs depending on it are skipped too. GitHub supports conditions such as always() when a downstream job must still execute, for example cleanup/reporting.

Multi-stage pipelines?

GitHub Actions doesn't use Azure Pipelines' explicit stage: hierarchy in the same way. You usually model lifecycle phases using jobs + needs + environments.

Conceptually:

Azure Pipelines             GitHub Actions

Stage: Build                Job: build
Stage: Test                 Job: test
Stage: Deploy               Job: deploy
                            environment: production

Matrix parallelism

This is a useful new concept.

Suppose a Python library must work with:

Python 3.10
Python 3.11
Python 3.12
Python 3.13

Instead of manually defining four jobs, GitHub Actions can use a matrix strategy. GitHub creates a job for each matrix combination and runs them in parallel subject to runner availability.

Conceptually:

                  ┌→ Python 3.10
                  ├→ Python 3.11
Test matrix ──────┼→ Python 3.12
                  └→ Python 3.13

That's particularly useful for cross-platform or multi-runtime testing.

- Jobs run in parallel by default.
needs: creates explicit dependencies.
- Multiple independent jobs should run in parallel where possible.
- A downstream job can depend on multiple upstream jobs.
- A matrix creates parallel job executions for combinations such as OS/runtime versions.
needs: test waits for the entire test matrix.
- GitHub Actions usually models multi-stage flows with jobs + dependencies + environments, rather than Azure Pipelines-style explicit stage: blocks.
- YAML order does not define GitHub job execution order.
- Separate jobs do not share a filesystem automatically.
- Don't serialize jobs that have no real dependency.
- Matrix combinations multiply: 2 OS × 2 Python = 4 runs.
- If one required matrix execution fails, downstream deployment is normally skipped.
- Use fan-out/fan-in to increase validation speed while still gating deployment on all required checks.


### Develop and implement complex pipeline scenarios, such as hybrid pipelines, VM templates, and self-hosted runners or agents
A complex pipeline often doesn't use one execution model everywhere. Different jobs have different requirements:

                 ┌→ Hosted agent → Build
GitHub/Azure ────┤
                 └→ Self-hosted agent → Private integration test
                                           ↓
                                    Production deployment

That's a hybrid pipeline: different execution environments participate in one delivery flow.

Why hybrid?

Imagine:

Build
→ standard Python/.NET tooling
→ no private connectivity

Integration Test
→ must access private SQL database

Deploy
→ must access internal production endpoint

Putting everything on self-hosted agents would work, but you'd take on unnecessary infrastructure maintenance for Build.

Putting everything on Microsoft-hosted agents may not work because the private resources aren't reachable.

A stronger design could therefore be:

Microsoft-hosted
Build + unit tests
        ↓
Artifact
        ↓
Self-hosted
Integration tests
        ↓
Self-hosted / controlled deployment

This follows a useful principle:

Use specialized infrastructure only where the workload actually requires it.

VM templates / images

You've already seen the configuration-drift problem:

Agent A → Python 3.11
Agent B → Python 3.12
Agent C → Python 3.13

Manually creating self-hosted agents doesn't scale well.

Instead, define a repeatable machine baseline:

Version-controlled definition
├── OS
├── required tools
├── agent prerequisites
├── security configuration
└── versions
        ↓
Build VM/image
        ↓
Create agents consistently

Depending on the architecture, this might involve VM images, image-building tooling, infrastructure as code, VM Scale Sets, or equivalent runner infrastructure.

This also enables ephemeral agents:

Known image
   ↓
Create agent
   ↓
Run job
   ↓
Destroy agent

rather than:

Agent VM
↓
job
↓
job
↓
job
↓
six months of accumulated state...

Ephemeral execution improves isolation and reduces configuration drift, though it adds provisioning/image-management considerations.

The design principle is use the appropriate execution environment for each workload. A complex pipeline doesn't need to be entirely hosted or entirely self-hosted.

You also connected this to VM templates: when many self-hosted agents are required, standardized images/templates help prevent configuration drift and make scaling/replacement reproducible.

Ephemeral agents improve isolation further, but they don't eliminate maintenance; you still need to maintain the underlying image.

- Hybrid doesn't mean duplicate builds. Build once and pass the artifact across execution environments.
- Self-host everything isn't automatically better just because one stage needs private connectivity.
- Ephemeral ≠ maintenance-free. The base image still needs patching and version management.
- Different agents have different filesystems. Artifacts provide an explicit handoff.

Use the least specialized execution environment that satisfies the job’s requirements.

### Create reusable pipeline elements, including YAML templates, task groups, variables, and variable groups
The four concepts are:

YAML templates → reusable pipeline structure or steps.
Task groups → reusable groups of tasks in classic pipelines.
Variables → reusable values inside pipelines.
Variable groups → centrally managed sets of variables shared across pipelines.

A simple mental model:

Reusable logic     → YAML template / task group
Reusable values    → variable / variable group
YAML templates

A template can hold repeated steps, jobs, or stages.

For example, instead of copying this into every repo:

steps:
- script: npm ci
- script: npm test
- script: npm run lint

you could put it in a template:

templates/test.yml

and reference it from multiple pipelines.

This reduces duplication and makes changes easier to apply consistently.

Task groups

Task groups are mainly associated with Classic pipelines in Azure DevOps.

They let you bundle several configured tasks together and reuse them as one logical unit.

Conceptually:

Task group: BuildAndTest
├── Restore
├── Build
├── Test
└── Publish results

For modern YAML pipelines, templates are usually the more relevant reusable mechanism.

Variables

Variables are reusable runtime values, such as:

variables:
  buildConfiguration: Release

and then:

- script: echo $(buildConfiguration)
Variable groups

A variable group centralizes values that multiple pipelines may need:

Variable group: shared-prod-settings
├── region = westeurope
├── appName = orders-api
└── someSecret = ***

Pipelines can then reference that group rather than duplicating values in YAML.

The key governance benefit is:

Change the shared value once, instead of editing many pipelines.

Parameters configure reusable pipeline logic; variables provide values to executing pipeline logic.

- YAML templates → reusable steps/jobs/stages in YAML pipelines.
- Task groups → reusable task sequences mainly for Classic pipelines.
- Variables → values used within one pipeline/run.
- Variable groups → centrally managed values shared across pipelines.
- Template parameters → inputs that configure reusable YAML before execution.
- Don’t use a variable group for every trivial one-off value.
- Don’t copy repeated YAML across many repositories if a template can centralize it.
- Parameters and variables are not interchangeable: parameters shape/configure template expansion; variables are primarily runtime values.
- For Classic pipelines, expect task groups rather than YAML templates.
- Shared secrets should be protected carefully; for stronger secret management, external secret stores such as Azure Key Vault may be appropriate.


### Design and implement checks and approvals by using YAML-based environments
The architecture is:

YAML pipeline
    ↓
deployment job
    ↓
environment: production
    ↓
Environment checks
├── approvals
├── branch control
├── business hours
├── exclusive lock
└── other configured checks
    ↓
deployment allowed

The important distinction is that the YAML targets the environment, but approvals/checks are generally configured on the protected resource in Azure DevOps rather than being defined by the application pipeline YAML itself. This separation prevents someone who can edit pipeline YAML from simply removing a production approval.

A deployment job might therefore contain:

- stage: DeployProd
  jobs:
  - deployment: Deploy
    environment: production
    strategy:
      runOnce:
        deploy:
          steps:
          - script: echo "Deploying"

The key new construct is:

deployment:

rather than an ordinary:

job:

A deployment job is specifically designed for deployments and can target an Azure DevOps environment.

Checks versus conditions

This distinction is very exam-relevant:

YAML condition
→ pipeline-defined logic
→ should this stage/job execute?

Environment check
→ protected-resource governance
→ is this deployment permitted to proceed?

For example:

condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')

could stop the stage from running for a feature branch.

An environment approval could require:

Production deployment reached
        ↓
Approval required
        ↓
Authorized approver
   ├─ Reject → stop
   └─ Approve → continue

These mechanisms complement each other rather than replacing one another.

Checks

Azure DevOps environments support checks such as approvals, branch control, business hours, REST/Azure Function checks, Azure Monitor alert checks, and exclusive locks. Checks are evaluated before a stage consuming the protected resource can proceed.

For example, an exclusive lock addresses:

Pipeline A ─┐
            ├→ production
Pipeline B ─┘

when you don't want two deployments modifying production simultaneously.