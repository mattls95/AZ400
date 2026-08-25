# Design and implement build and release pipelines

## Design and implement deployments


### Design a deployment strategy, including blue-green, canary, ring, progressive exposure, feature flags, and A/B testing
The strategies differ mainly in how much traffic/user exposure the new version gets, how quickly that exposure grows, and how rollback works.

A useful map is:

Blue-green
→ two complete environments
→ switch traffic from old to new

Canary
→ small percentage gets new version first
→ increase traffic if healthy

Ring
→ predefined user/environment groups
→ Ring 0 → Ring 1 → Ring 2...

Progressive exposure
→ umbrella idea
→ gradually increase exposure based on validation

Feature flags
→ deploy code separately from enabling functionality

A/B testing
→ intentionally expose different variants
→ compare user/business outcomes

Blue-green

You maintain two equivalent environments:

Blue  → v1 currently serving users
Green → v2 deployed and validated

Then switch traffic:

Before:
Users → Blue v1

After:
Users → Green v2

If v2 fails, you can potentially route traffic back to Blue quickly.

The trade-off is that maintaining two production-capable environments can cost more.

Canary

With canary deployment, only a small portion receives the new release first:

v1 → 95% traffic
v2 → 5% traffic

If telemetry looks healthy:

5% → 20% → 50% → 100%

If error rate or latency becomes unacceptable, stop or roll back before everyone is affected.

Ring deployment

Ring deployment uses groups rather than primarily percentages.

For example:

Ring 0 → internal engineering
Ring 1 → employees
Ring 2 → selected customers
Ring 3 → everyone

This is particularly useful when you want increasingly broad populations with known characteristics.

Progressive exposure

This is the broader pattern behind canary and rings:

Start with limited exposure, observe, then expand only if telemetry says it is safe.

So canary and ring strategies are both forms of progressive exposure.

Feature flags

Feature flags separate:

Deployment
≠
Feature release

You can deploy:

v2 code → production
NewCheckout flag = OFF

and later enable it:

NewCheckout
→ internal users
→ 5%
→ 25%
→ 100%

This also connects to the trunk-based development work you did earlier.

A/B testing

A/B testing has a different goal.

It is not primarily:

“Is version B technically safe?”

It is:

“Which experience performs better?”

For example:

Group A → old checkout
Group B → new checkout

Measure:
conversion rate
cart abandonment
revenue/user

So remember:

Canary optimizes release risk.
A/B testing compares outcomes between variants.

One exam nuance: blue-green doesn't magically make every rollback safe. Database/schema changes and external side effects must remain compatible with switching back.

Important exam distinction
Deployment
→ getting code into an environment

Release
→ making functionality available to users

- Blue-green → two complete environments; switch traffic between them.
- Canary → small traffic percentage first, then gradually increase.
- Ring → expose to predefined user/population groups.
- Progressive exposure → broader strategy combining staged exposure, telemetry, flags, rings, canaries, etc.
- Feature flags → deploy code independently from enabling the feature.
- A/B testing → compare different variants based on user/business outcomes.
- Canary vs A/B: canary is about release safety; A/B is about comparing outcomes.
- Ring vs canary: rings are predefined populations; canary commonly uses traffic percentages.
- Deployment vs release: feature flags can separate them.
- Blue-green rollback can be fast, but database/schema compatibility can still make rollback difficult.
- Progressive rollout should be driven by telemetry and thresholds, not merely by waiting a fixed amount of time.

### Design a pipeline to ensure that dependency deployments are reliably ordered
Think:

Database schema
   ↓
Backend API
   ↓
Frontend

If the frontend depends on a new backend API, and the backend depends on a new database schema, deploying them in the wrong order can break the application even if every individual deployment succeeds.

The core principle is:

Model real deployment dependencies explicitly instead of relying on timing or YAML order.

For example:

DeployDatabase
      ↓
DeployBackend
      ↓
DeployFrontend

In Azure Pipelines this can be modeled with stage dependencies such as dependsOn. In GitHub Actions you'd use needs: between jobs.

But ordering alone isn't enough. A reliable dependency deployment strategy should also consider:

- whether the dependency actually deployed successfully
- whether it is ready/healthy, not merely “deployment command returned 0”
- backward compatibility between old and new components
- rollback implications
- whether independent components can deploy in parallel

A stronger flow is therefore:

Deploy DB
   ↓
Validate DB migration
   ↓
Deploy API
   ↓
Health check API
   ↓
Deploy frontend

Compatibility is important

Suppose version 2 of the backend requires a database column that doesn't exist yet.

This is unsafe:

Backend v2
   ↓
Database migration

because there is a period where the new backend can hit the old schema.

A safer strategy is often:

Backward-compatible DB change
   ↓
Backend v2 deployed
   ↓
Old schema removed later

This is sometimes called an expand-and-contract style migration.

Correct order alone is not enough; each dependency should be proven ready before the next deployment starts. Encode actual dependencies explicitly, validate that dependencies are ready, and parallelize components that don't depend on each other.

- YAML order alone shouldn't be relied on for complex deployment dependencies; model them explicitly with dependsOn.
- Deployment succeeded ≠ dependency is ready. Health/readiness validation may be required.
- Don't serialize independent deployments unnecessarily.
- Database changes often need to precede application code that depends on them.
- Prefer backward-compatible database evolution where possible; deployment ordering alone cannot solve incompatible schema changes.
- A downstream component depending on several services should wait for all required dependencies.

### Plan for minimizing downtime during deployments by using load balancing, rolling deployments, and deployment slot usage and swap

→ keep multiple instances serving traffic

Rolling deployment
→ replace/update instances gradually

Deployment slots
→ prepare new version separately, then switch it into production
Load balancing

Imagine three application instances:

            Load balancer
          /      |       \
       App A   App B    App C

If App A is being updated, a good deployment process can take it out of rotation:

Traffic
  ↓
Load balancer
  ├─ App A → updating, no traffic
  ├─ App B → serving
  └─ App C → serving

Once A is healthy again, it rejoins the pool.

The important pieces are multiple instances + health checks + traffic routing.

Rolling deployment

Instead of:

Stop all 10 instances
→ deploy v2 everywhere
→ restart all

you do something like:

10 × v1

Update 2
↓
8 × v1 + 2 × v2

Update next 2
↓
6 × v1 + 4 × v2

...

10 × v2

Users continue being served by the remaining healthy instances.

The trade-off is that, during rollout, v1 and v2 may run at the same time, so compatibility matters.

Deployment slots

Azure App Service slots give you separate live app environments such as:

Production slot → v1
Staging slot    → v2

You deploy v2 to staging first:

Deploy v2
   ↓
Staging slot
   ↓
Warm up
   ↓
Smoke/integration tests
   ↓
Swap
   ↓
Production now serves v2

That reduces cold-start and deployment downtime because the new version is already running before traffic is switched.

It also gives you a useful rollback path: in many scenarios you can swap back.

One important exam nuance is slot settings. Some configuration values should stay tied to a slot instead of swapping with the application—for example environment-specific settings or secrets.

- Rolling deployments require v1 and v2 compatibility while both versions coexist.
- A running instance isn't necessarily healthy; use health/readiness checks before routing traffic to it.
- Deployment slots allow validation before production exposure.
- Slot-specific settings stay with their slot; normal swappable settings can move during a swap.
- A slot swap can provide a fast rollback path by swapping back, but incompatible database changes can still complicate rollback.
- Deployment slots require an App Service tier that supports them.

### Design a hotfix path plan for responding to high-priority code fixes
A hotfix path is a deliberately shortened but still controlled route for urgent production fixes.

The goal is:

Reduce time-to-recovery without throwing away traceability, validation, or governance.

A normal path might be:

feature branch
   ↓
full PR workflow
   ↓
all tests
   ↓
staging
   ↓
scheduled release
   ↓
production

A hotfix path may instead be:

production issue
   ↓
hotfix branch from production version
   ↓
targeted fix
   ↓
fast CI + critical security/tests
   ↓
expedited approval
   ↓
production
   ↓
merge fix back to main/release branches

The important point is that hotfix does not mean bypass everything. You usually keep the controls that matter most:
- source traceability
- peer review where possible
- targeted automated tests
- critical security checks
- controlled production approval
- rollback plan
- post-deployment verification

The path is shorter because lower-value delays are removed, not because safety disappears.

A second important concept is where to branch from. If production is running v2.4.1, and main already contains unfinished v3.0 work, creating the hotfix from main could accidentally pull unreleased changes into production.

So a safer model is:

main → v3 development

v2.4.1 tag/release branch
        ↓
    hotfix/2.4.2

Then after release, the fix should be propagated forward so it isn't lost from future versions.

- branch from the current production baseline
- keep the change narrowly scoped
- retain essential tests/security checks
- use expedited but controlled approval
- deploy with rollback available
- verify production health
- propagate the fix back to main and any other supported release branches
- Hotfix does not mean bypass all controls.
- Branch from the production release/tag, not from unreleased main.
- If rollback is faster and safe, rollback may be the better first recovery action.
- After the emergency is resolved, forward-port the fix so it isn't reintroduced later.
- Keep traceability through commits, PRs, pipeline runs, approvals, and incident/change records.

You practiced the two key Git operations:

git switch -c hotfix/2.4.2 v2.4.1

to branch from the production version, and:

git switch main
git cherry-pick abc123

to propagate the targeted fix forward. 

Rollback plans must account for stateful dependencies such as databases, not just application binaries.

### Design and implement a resiliency strategy for deployment
A resilient deployment pipeline should handle expected failures safely instead of assuming every deployment operation succeeds on the first attempt.

Think about failures such as:

Deployment pipeline
   ↓
Azure API call → transient timeout
   ↓
Health check → temporary failure
   ↓
Deployment interrupted halfway
   ↓
What happens now?

A resiliency strategy usually combines several mechanisms:

Prevention
→ immutable/versioned artifacts
→ dependency validation
→ backward-compatible changes

Tolerance
→ retries for transient failures
→ timeouts
→ idempotent deployment operations

Detection
→ health checks
→ telemetry
→ post-deployment validation

Recovery
→ rollback
→ redeploy known-good artifact
→ slot swap-back
→ forward fix when rollback isn't safe
Idempotency

This is particularly important and hasn't been a major focus in our previous labs.

An idempotent deployment can safely be executed again without creating an incorrect state.

Imagine a pipeline fails halfway through:

1. Create infrastructure       ✅
2. Configure application       ✅
3. Configure monitoring        ❌ timeout

You rerun it.

A poor deployment implementation might fail immediately:

Create infrastructure
→ ERROR: already exists

A resilient/idempotent implementation instead recognizes the desired state:

Infrastructure exists correctly → no change
Configuration correct           → no change
Monitoring missing              → create it

Infrastructure-as-code tools are useful here because they generally work toward a declared desired state.

Retries

Retries are useful for transient failures:

Azure API
   ↓
HTTP 503
   ↓
wait
   ↓
retry
   ↓
success

But retries aren't appropriate for every failure.

Authentication denied
Invalid ARM/Bicep
Failed unit test

Running those 20 more times probably won't help.

A common approach is retry with backoff:

attempt 1
   ↓ fail
wait 2s
   ↓
attempt 2
   ↓ fail
wait 4s
   ↓
attempt 3

This avoids hammering a struggling dependency.

Versioned artifacts

You've already practiced passing artifacts between stages. For resiliency, the important extension is that the deployment should use a known, immutable/versioned artifact.

If production fails:

v43 ❌
 ↓
redeploy v42

you want the exact previously validated v42, not:

checkout old source
→ rebuild it today
→ hope it's identical

So:

Build once, version the artifact, promote that exact artifact, retain known-good versions for recovery. A resilient deployment should tolerate recoverable failures, detect unrecoverable ones, and fail safely rather than retrying indefinitely or leaving deployment state ambiguous.

- Retries with backoff for transient failures.
- Timeouts/bounded attempts so pipelines don't hang indefinitely.
- Idempotent deployment operations so retries and reruns are safe.
- Health/readiness checks before progressing.
- Immutable known-good artifacts for recovery.
- Rollback or forward-fix paths depending on compatibility.
- Progressive exposure/slots/rolling deployment to reduce blast radius.
- Don't retry permanent errors such as invalid configuration indefinitely.
- A deployment command returning success does not necessarily mean the application is healthy.
- Rerunning a non-idempotent deployment can make the situation worse.
- Rebuild-from-source is weaker recovery than redeploying an already validated artifact.
- Retry logic should be bounded and eventually produce a clear failure.


### Implement feature flags by using Azure App Configuration Feature Manager
Now the new objective is specifically implementing them with Azure App Configuration Feature Management.

The architecture is roughly:

Application
     ↓
Azure App Configuration
     ↓
Feature flag: BetaCheckout
├── Disabled
└── Enabled
     ↓
Feature Manager in application
     ↓
Old or new behavior

Instead of hard-coding:

BetaCheckout = true

the application evaluates a feature flag stored in Azure App Configuration. Microsoft provides feature-management libraries for application frameworks, including .NET, Java, Python, and JavaScript.

Basic flag

The simplest flag is Boolean:

BetaCheckout
├── OFF → old checkout
└── ON  → new checkout

But feature management becomes more useful when you add filters.

For example:

BetaCheckout
   ↓
Targeting
├── Group: employees
├── User: test-user
└── Percentage rollout

This connects directly to the progressive-exposure strategy you just studied.

Feature filters

A feature flag doesn't necessarily mean:

everyone ON
or
everyone OFF

Filters can determine whether the feature is enabled for the current context.

For example, percentage-based rollout:

BetaCheckout
5% users
   ↓ telemetry good
25%
   ↓
50%
   ↓
100%

Or targeting:

Employees       → ON
Beta customers  → ON
Everyone else   → OFF

Another useful option is a time window, where activation can be constrained to particular times.

Feature flags versus application configuration

Azure App Configuration can hold both ordinary configuration and feature flags, but conceptually keep them separate:

Configuration
connectionTimeout = 30
region = swedencentral

Feature management
BetaCheckout = enabled/disabled

Feature flags represent behavioral decisions, not just arbitrary configuration values.

Operational advantage

A major advantage is that changing a flag doesn't inherently require rebuilding and redeploying the application.

Code already deployed
      ↓
BetaCheckout OFF
      ↓
change flag
      ↓
BetaCheckout ON

Depending on the application's configuration refresh behavior, it can pick up updated feature state without a new application deployment.

Azure App Configuration’s targeting model can include:

specific users
groups
excluded users/groups
rollout percentages within groups
a default rollout percentage for everyone else

Conceptually:

BetaCheckout
├── Developers group → ON
├── alice            → ON
├── bob              → ON
└── everyone else    → OFF

- Feature flag → controls whether functionality is exposed.
- Pipeline condition → controls whether deployment logic executes.
- Targeting filter → expose to specific users/groups.
- Percentage rollout → progressively expose to a percentage.
- Experiment → compare variants/outcomes.
- Feature flags can act as a kill switch without rolling back the whole application.
- Avoid hard-coded access keys; prefer identity-based authentication such as DefaultAzureCredential.

### Implement application deployment by using containers, binaries, and scripts
This objective is about the form your deployable application takes and how the pipeline delivers it.

You've already worked extensively with artifacts, so we won't repeat build/publish/download fundamentals. The new distinction is between three deployment approaches:

Containers
→ deploy an immutable container image

Binaries/packages
→ deploy a built application artifact

Scripts
→ execute deployment/configuration commands
Container deployment

With containers, the pipeline normally builds once:

Source
  ↓
docker build
  ↓
Image: orders-api:1.4.7
  ↓
Container registry
  ↓
Deploy that image
  ↓
App Service / AKS / Container Apps

The important principle is again build once, promote the same artifact.

You generally don't want:

Dev  → build image
Test → rebuild image
Prod → rebuild image

Instead:

orders-api:1.4.7
  ├→ Test
  ├→ Staging
  └→ Production

A container bundles the application and its runtime dependencies into a consistent deployable unit.

Binary/package deployment

Not every application needs containers.

A .NET application, for example, could produce:

orders-api.zip

The pipeline can deploy that package directly to something such as Azure App Service.

The flow becomes:

Build
  ↓
orders-api.zip
  ↓
Pipeline artifact
  ↓
Deployment task
  ↓
App Service

Again, the binary should be produced during build, not rebuilt during production deployment.

Script-based deployment

Sometimes deployment requires commands rather than a dedicated deployment task.

For example:

- script: |
    ./deploy.sh

or Azure CLI:

- task: AzureCLI@2
  inputs:
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az ...

Scripts provide flexibility, but that flexibility comes with responsibility:

Deployment script
├── error handling
├── exit codes
├── authentication
├── idempotency
└── logging

This connects directly to the resiliency lab you just completed.

A script that silently ignores a failed command can make the pipeline report:

Deployment succeeded ✅

when the application wasn't actually deployed correctly.

Choosing between them

Don't think:

Containers are modern, therefore containers are always correct.

Instead consider the application's deployment target and requirements.

Existing App Service application + ZIP artifact
→ binary/package deployment may be simplest

Standardized microservice deployed to AKS
→ container image makes sense

Special infrastructure/configuration operation
→ deployment script may make sense

And they aren't mutually exclusive. A pipeline might deploy a container and then execute scripts for post-deployment validation. Container image size is dominated by the runtime/base image, not just by your application code. Build once, validate the artifact, and deploy that same artifact.

- Don't containerize an application purely because containers exist.
- Don't rebuild an already-tested image for production.
- A container tag such as latest can move; an image digest identifies exact content.
- RUN happens during image build; CMD/ENTRYPOINT control container startup.
- Deployment scripts must propagate failures with meaningful exit codes.
- Scripts should also be idempotent and handle authentication/secrets safely.

### Implement a deployment that includes database tasks
A deployment that includes a database often needs to coordinate:

Application artifact
      +
Database migration/schema change
      ↓
Deployment pipeline

Typical database tasks include:

applying schema migrations
running SQL scripts
updating stored procedures/views
seeding reference data
validating connectivity and permissions
backing up or taking a recovery point before risky changes
verifying the new schema after deployment

The big design principle is:

Database changes are stateful, so they need more care than replacing stateless application binaries.

A safer pattern is often:

Backup/recovery readiness
        ↓
Apply backward-compatible DB change
        ↓
Validate migration
        ↓
Deploy application
        ↓
Smoke/integration test
        ↓
Remove obsolete schema later

That last part is the expand-and-contract idea you already touched on: add compatible schema first, deploy code that can use it, then remove old schema only after old application versions are gone.

Common implementation styles

Depending on the stack, database deployment might use:

EF Core migrations
Flyway/Liquibase
SQL scripts
DACPAC / SqlPackage for SQL Server
Azure CLI or PowerShell wrapping DB tools
dedicated Azure Pipelines database tasks

The tool is less important than the guarantees around it:

- migration is versioned
- migration is repeatable
- failure is surfaced
- credentials are protected
- result is validated
- rollback/forward-fix is planned

- Apply migrations in a known order.
- Prefer backward-compatible/expand-first schema changes when old and new app versions may coexist.
- Validate the resulting schema/permissions before application deployment.
- Treat the database as stateful infrastructure with rollback/forward-fix considerations.
- If using local DB files in CI, remember that separate jobs may not share filesystem state.

dependsOn guarantees execution order, but it does not guarantee shared local files between jobs.