# Design and implement build and release pipelines

## Maintain pipelines

### Monitor pipeline health, including failure rate, duration, and flaky tests
A pipeline being green right now doesn't necessarily mean it's healthy. Pipeline health is about behavior over time.

The three metrics explicitly called out in this objective are:

Failure rate
→ How often does the pipeline fail?

Duration
→ How long does it take?

Flaky tests
→ Which tests fail inconsistently?
Failure rate

A simple measure is:

failure rate = failed runs / total runs

For example, if a pipeline has 100 runs and 15 fail, its failure rate is 15%.

But the number alone doesn't tell you the cause. You'd want to distinguish things such as:

Pipeline failures
├── application/test failure
├── deployment failure
├── infrastructure failure
├── agent failure
└── external dependency failure

A sudden increase is especially useful:

Normal:  3% failures
Week 1:  4%
Week 2:  5%
Week 3: 22%  ← investigate
Pipeline duration

Don't only monitor the average.

Suppose:

Typical pipeline → 8 minutes
Occasional runs  → 35 minutes

An average can hide those slow outliers.

Useful things to investigate include:

queue time
stage duration
job duration
test duration
artifact operations
deployment duration

This helps identify bottlenecks.

For example:

Build       2m
Unit tests  3m
Security    4m
Integration 18m  ← bottleneck
Deploy      2m

You might then investigate test parallelization, caching, agent capacity, or unnecessary work.

Flaky tests

A flaky test is especially damaging because the same code can produce inconsistent results:

Commit abc123

Run 1 → TestPayment ❌
Run 2 → TestPayment ✅
Run 3 → TestPayment ✅

The code didn't change, yet the test outcome did.

Common causes include timing/race conditions, shared state, test-order dependencies, unreliable external services, and environment differences.

The dangerous response is:

“Just rerun failed tests until they're green.”

Retries can reduce disruption temporarily, but they can also hide flaky tests. You should still track and fix the underlying instability.
A healthy pipeline gives developers confidence:

Red pipeline
→ likely real problem

Green pipeline
→ likely safe

If everyone assumes “it's probably just flaky”, the pipeline loses much of its value.

The useful operational response is not just “the pipeline is worse,” but to correlate the increase with changes such as:

- new or flaky tests
- dependency/tool version changes
- agent image updates
- new deployment logic
- unstable external services
- infrastructure capacity issues
- Failure rate tells you how often runs fail.
- Duration tells you whether feedback is getting slower.
- Flaky tests reveal inconsistent validation that can erode trust in CI/CD.
- A lower visible failure rate caused by retries does not necessarily mean pipeline health improved.
- Average duration can hide slow outliers; drill into stage/job/task timings.
- Same commit + inconsistent test result is a strong flaky-test signal.
- A green pipeline is only useful if developers trust that failures are meaningful.
- Monitor trends over time, not isolated runs.

So for this objective, a useful troubleshooting order is:

- Failure increase → inspect failed stages/tests/dependencies.
- Duration increase → inspect stage/job duration and queue/agent time.
- Inconsistent tests on unchanged code → investigate flakiness.

### Optimize a pipeline for cost, time, performance, and reliability
Now the question is:

How do we improve it without sacrificing reliability?

There are four competing dimensions:

Cost ←──────── Pipeline ────────→ Performance
                  │
             Time │ Reliability

Optimization is usually a trade-off, not simply “make everything faster.”

Time and performance

Suppose:

Build          3m
Unit tests     8m
Integration   15m
Security       4m
Deploy         3m
───────────────
Sequential    33m

If unit tests, integration tests, and security scanning don't depend on one another, running them sequentially wastes time.

You could use:

              ┌→ Unit tests ──────┐
Build ────────┼→ Integration ─────┼→ Deploy
              └→ Security scan ───┘

Now pipeline duration is closer to the longest parallel path, rather than their sum.

But this may increase cost/concurrency consumption because multiple agents execute simultaneously.

Caching

Another major optimization is avoiding repeated work.

Examples include dependency/package caches for npm, NuGet, Maven, pip, and Gradle.

Every run:
download 2 GB dependencies
→ slow + network cost

With cache:
cache hit
→ reuse dependencies
→ faster

The important exam trap is the cache key. If dependencies change, you need the cache to reflect that.

For example:

requirements.txt changes
        ↓
cache key changes
        ↓
new dependencies restored

A stale cache shouldn't cause the pipeline to use incorrect dependencies.

Artifacts: build once

This should now be familiar:
uild once
   ↓
immutable artifact
   ├→ Test
   ├→ Staging
   └→ Production

This improves several dimensions simultaneously:

less repeated compute → cost
no rebuilding → time
identical artifact → reliability
better traceability → operability
Agent strategy

Agent selection affects all four dimensions.

Microsoft-hosted agents offer:

+ low maintenance
+ clean environment each run
+ easy scaling

- environment recreated each job
- limited persistence
- queue/concurrency considerations

Self-hosted agents can offer:

+ persistent caches/tools
+ custom hardware/software
+ network access to private resources
+ potentially better performance

- you patch and secure them
- capacity planning required
- maintenance cost
- persistent state can create contamination

So “self-hosted is faster” does not automatically mean “self-hosted is better.”

Reliability must constrain optimization

Suppose someone proposes:

“Production deployments take too long. Remove integration tests, security scanning and approval checks.”

That certainly improves duration.

It also potentially destroys reliability.

A better optimization might be:

parallelize independent validation
cache dependencies
avoid redundant builds
run expensive tests only when relevant
reuse immutable artifacts
optimize agent capacity

rather than simply deleting controls.

Conditional execution

Not every change needs every expensive pipeline operation.

For example:

README.md changed
→ compile entire application?
→ run 45-minute integration environment?

Path filters and conditions can avoid unnecessary work.

But be careful: overly aggressive conditions can skip validation that actually matters.

Reliability optimizations

Optimization isn't only about speed.

Techniques you've already practiced include:

transient operation
→ bounded retry + backoff

deployment
→ health validation

production
→ progressive rollout / slots

artifact
→ immutable and tested

tests
→ detect and fix flakiness

Sometimes spending another two minutes on validation dramatically improves deployment reliability—and that's a good optimization trade-off.

Conceptually:

cache key
= OS + hash(package-lock.json)

So:

package-lock.json unchanged
→ cache hit
→ faster restore

package-lock.json changed
→ cache miss/new cache
→ dependencies restored correctly

Separate job
→ acquire/start agent
→ initialize job
→ checkout/setup
→ run a few seconds of work
→ tear down

investigate dependency caching

### Optimize pipeline concurrency for performance and cost
The new focus is specifically how much pipeline work should execute concurrently and how concurrency affects throughput, queue time, agent capacity, and cost.

Parallelism vs concurrency

Keep these related concepts separate:

Parallelism
→ splitting one pipeline's work across simultaneous jobs

Concurrency
→ how much pipeline work the organization can execute simultaneously

For example, one pipeline could fan out:

             ┌→ UnitTests
Build ───────┼→ Security
             └→ Integration

That needs up to three concurrent execution slots/agents for those jobs to actually run simultaneously.

If capacity only permits one job at a time:

UnitTests → Security → Integration

Your YAML describes parallelism, but the available concurrency becomes the constraint.

Optimize throughput, not just one pipeline

This is the major new concept.

Imagine you have four concurrent agents and ten developers running pipelines.

Pipeline A could consume all four:

Agent 1 → A UnitTests
Agent 2 → A Security
Agent 3 → A Integration
Agent 4 → A Analysis

Pipeline B → queued
Pipeline C → queued
Pipeline D → queued

Pipeline A finishes quickly, but organization-wide throughput may suffer.

Alternatively:

Agent 1 → Pipeline A
Agent 2 → Pipeline B
Agent 3 → Pipeline C
Agent 4 → Pipeline D

Individual pipelines may take longer, but more developers receive feedback concurrently.

So:

Minimizing one pipeline's duration is not necessarily the same as maximizing system throughput.

Queue time matters

You already identified this in the previous objective.

Suppose:

Execution time → 8 min
Queue time     → 14 min

Optimizing another minute out of the build isn't addressing the main problem.

The concurrency bottleneck is agent/parallel-job capacity.

Microsoft-hosted vs self-hosted capacity

With Microsoft-hosted agents, concurrency is affected by the available parallel-job capacity for the organization.

With self-hosted agents, you additionally need enough actual agents:

10 parallel jobs permitted
+
2 self-hosted agents available
=
only 2 jobs can actually execute simultaneously

So distinguish:

Concurrency entitlement/capacity
            +
available agents
            ↓
effective concurrency
Cost trade-off

Increasing concurrency can reduce:

queue time
feedback time
deployment lead time

But it can increase:

parallel-job/compute cost
number of self-hosted machines
maintenance overhead
simultaneous load on external systems

And sometimes the target system itself is the bottleneck.

Running 20 integration jobs simultaneously against one shared test database might actually make everything slower or unreliable.

- Parallel YAML does not guarantee simultaneous execution if capacity is unavailable.
- More concurrency can reduce queue time but increase cost.
- Giving one pipeline every agent can hurt organization-wide feedback time.
- More test workers can make performance worse if a shared database/API becomes the bottleneck.
- Self-hosted concurrency is constrained by available agents as well as parallel-job capacity.
- High-priority production work may justify prioritization over routine workloads.

That difference between wall-clock performance and resource consumption/cost is central to optimizing pipeline concurrency.

### Design and implement a retention strategy for pipeline artifacts and dependencies
You've already used the principle:

Build once → promote the same immutable artifact.

Retention adds another requirement:

Keep important artifacts long enough that you can still trace, redeploy, audit, or recover them when needed.

What might need retention?

Don't think only about ZIP files:

Pipeline outputs
├── pipeline/build artifacts
│   └── app.zip, binaries
├── container images
├── packages
│   └── NuGet, npm, Maven, Python
├── test results/logs
├── deployment metadata
└── dependencies used to reproduce builds

These may live in different systems. A pipeline artifact might be in Azure Pipelines, a package in Azure Artifacts, and a container image in Azure Container Registry.

Retention should follow purpose

A useful strategy separates short-lived CI output from important releases.

PR build
→ useful briefly
→ short retention

main branch CI build
→ potentially deployable
→ medium retention

production release
→ rollback/audit/redeployment value
→ longer retention

Keeping everything forever increases storage cost and clutter.

Deleting everything quickly can destroy your ability to redeploy a known-good artifact.

Production rollback

Suppose production currently runs:

v4.2

You deploy v4.3, discover a severe issue, and decide to roll back.

The safest path is generally:

retrieve retained, previously validated v4.2 artifact
        ↓
redeploy v4.2

not:

checkout old commit
        ↓
rebuild v4.2 today
        ↓
hope dependencies/tooling produce identical output

This connects directly to your container digest work.

Dependencies are different from caches

This distinction matters:

Cache
→ performance optimization
→ disposable

Artifact/package
→ delivery/reproducibility asset
→ may require deliberate retention

If an npm cache disappears, the pipeline should still be able to restore dependencies from an authoritative package source.

A cache should generally not be your only copy of a required dependency.

Retention and compliance

Retention isn't purely technical.

Requirements may include:

Operational
→ rollback for 30 days

Audit
→ retain release evidence for 1 year

Legal/regulatory
→ retain particular records longer

Cost
→ delete ordinary PR artifacts after 7 days

Therefore, a good strategy often uses different policies for different artifact classes.

Dependencies and reproducibility

Suppose your build depends on:

Contoso.Library 3.7.2

and six months later that version is unavailable from its original external source.

Your source code still exists, but the build may no longer be reproducible.

This is why package feeds, version pinning/lock files, upstream-source strategies, and dependency retention can matter alongside pipeline artifact retention.

Lock file
→ WHAT exact dependency version do I need?

Package feed
→ WHERE can I obtain that dependency?

For example, your lock file might contain:

Contoso.Library → 3.7.2

The build then asks the package feed:

"Give me Contoso.Library 3.7.2"

So retaining only the lock file isn't enough if nobody has a copy of package 3.7.2.

A useful analogy:

Lock file = shopping list. Package feed = warehouse.

The shopping list tells you exactly what you need, but doesn't guarantee the warehouse still has it.

- Git commit ≠ deployable artifact. Retaining source does not replace retaining a known-good production artifact.
- Lock file ≠ package storage. It records which versions are needed; the feed supplies them.
- Cache ≠ authoritative dependency source. Losing a cache should affect performance, not build correctness.
- Don't retain everything forever; classify by operational and compliance value.
- Don't delete dependencies while supported releases still require them.
- For rollback, prefer the previously validated immutable artifact rather than rebuilding an old commit.

### Migrate a pipeline from classic to YAML in Azure Pipelines
The starting distinction is:

Classic pipeline
→ configured mainly through Azure DevOps UI

YAML pipeline
→ pipeline definition stored as code
→ typically azure-pipelines.yml in Git
Why migrate?

The major benefit is that the pipeline definition becomes part of the repository:

Git repository
├── application code
├── infrastructure code
└── azure-pipelines.yml

That gives you normal source-control capabilities around the pipeline: history, branches, pull-request review, and easier reuse through YAML templates.

But migration shouldn't mean:

“Open the Classic pipeline and rewrite whatever I remember.”

You first need to inventory its behavior.

Think through:

Classic pipeline
├── triggers
├── agent pools
├── variables / variable groups
├── tasks
├── artifacts
├── stages
├── conditions
├── approvals/checks
├── service connections
└── environment-specific configuration

Then reproduce the required behavior in YAML.

Mapping the concepts

A rough translation looks like:

Classic                    YAML
─────────────────────────────────────────
Agent job            →     job:
Task                  →     task:
Build steps           →     steps:
Release stages        →     stages:
Variables             →     variables:
Task conditions       →     condition:
Dependencies          →     dependsOn:

But some things should not simply be moved into YAML.

For example, credentials shouldn't become:

variables:
  productionPassword: 'SuperSecret123'

Existing secret stores, secret variable groups, Key Vault integrations, service connections, and workload identities should remain appropriately protected.

Classic releases → multistage YAML

A Classic release might look like:

Build artifact
     ↓
Dev
     ↓
Test
     ↓
Production

A multistage YAML pipeline can model the same lifecycle:

stages:
- stage: Build

- stage: Dev
  dependsOn: Build

- stage: Test
  dependsOn: Dev

- stage: Production
  dependsOn: Test

You've already worked with dependsOn, so the new skill is recognizing how that maps from the Classic visual pipeline.

Approvals are an important migration trap

Suppose the Classic release has:

Test
  ↓
[Production approval]
  ↓
Production

A common mistake is assuming every governance control belongs directly in azure-pipelines.yml.

Azure DevOps Environments and protected resources can have approvals/checks configured outside the YAML pipeline. This separation helps prevent someone who can edit pipeline YAML from simply deleting a production approval from the file.

Conceptually:

YAML
→ requests deployment to Production environment

Production environment
→ approval/check
→ controls whether deployment proceeds
Don't combine migration and redesign blindly

Imagine the Classic pipeline has been running production successfully for three years.

During migration someone decides to simultaneously:

Classic → YAML
.NET 8 → .NET 10
Microsoft-hosted → self-hosted
NuGet → different package system
deployment strategy → completely redesigned

Now if production breaks, which change caused it?

A safer migration approach is:

Understand existing behavior
        ↓
Translate to YAML
        ↓
Validate equivalent behavior
        ↓
Cut over
        ↓
Optimize/refactor afterward

This is similar to your database migration work: reduce the number of independent changes happening at once.

- Inventory the existing pipeline before migrating: triggers, variables, tasks, artifacts, conditions, dependencies, agents, service connections, and approvals.
- Aim for behavioral equivalence first, then optimize.
- Non-secret configuration can move into YAML; credentials should remain in protected mechanisms.
- Don't accidentally change task behavior during translation.
- Classic stages/jobs/tasks map naturally to YAML stages/jobs/steps, but not every - Classic setting belongs in YAML.
- Production approvals/checks are better enforced on protected resources such as Azure - DevOps Environments rather than implemented as removable YAML logic.
- After migration, YAML gives you source-control history, PR review, branching, and reusable templates.