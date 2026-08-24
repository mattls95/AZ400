# Design and implement build and release pipelines

## Design and implement a testing strategy for pipelines

### Design and implement quality and release gates, including security and governance
This point is about deciding what must be true before a change is allowed to progress through a pipeline.

A gate is essentially a policy checkpoint:

Build
  ↓
Quality checks
  ↓
Security checks
  ↓
Governance checks
  ↓
Release gate
  ↓
Deploy

Examples:

unit/integration tests must pass
code coverage must exceed a threshold
SAST/dependency scan has no Critical findings
required approvals are present
change-management approval exists
deployment is allowed only during an approved window
environment-specific checks succeed

A check becomes a gate when failure blocks progression.

Quality gates

These focus on software correctness and maintainability, such as:

Tests pass
Coverage ≥ 80%
No lint/build errors
Integration tests pass
Security gates

These enforce security policy:

Critical vulnerabilities = 0
High vulnerabilities = 0
Secret scan = clean
SAST policy = passed
Governance gates

These are more about organizational control:

Production approval required
Only authorized group can approve
Change ticket linked
Required policy checks completed

One important distinction:

automated gate → evaluated by tooling
manual approval → evaluated by an authorized person

Both may exist in the same release flow. Check = detect/evaluate. Gate = enforce a decision based on the result. Put critical governance gates at the resource/environment boundary they protect, and restrict who can administer those gates.

Quality gates → tests, coverage, integration checks, code quality
Security gates → vulnerability thresholds, secret scanning, SAST, dependency scanning
Governance gates → approvals, change-management requirements, deployment windows, protected environments

The core principle is:

Check = evaluate. Gate = enforce progression based on the result.

- A warning-only security scan is not an effective gate.
- Put gates at the boundary they protect.
- Run automated checks before asking for human approval where possible.
- Environment-level/resource-level checks are stronger than YAML-only controls when developers can edit the YAML.
- Central templates can enforce policy more consistently than copying security logic into every repo.
- Human approvals are best for decisions requiring judgment; objective rules should usually be automated.

### Design a comprehensive testing strategy, including local tests, unit tests, integration tests, and load tests

A useful progression is:

Local tests
   ↓
Unit tests
   ↓
Integration tests
   ↓
Load tests
   ↓
Release confidence increases

Local tests run on the developer machine before code reaches CI. They give the fastest feedback and can include unit tests, linting, formatting, or targeted checks.

Unit tests validate small pieces of code in isolation. They should be fast, deterministic, and run frequently—typically on every PR/build.

Integration tests validate that components work together: app ↔ database, service ↔ API, app ↔ queue, etc. They are slower and need more environment/setup.

Load tests validate behavior under expected or extreme traffic: latency, throughput, errors, saturation, and breaking points. They are usually too expensive to run on every tiny commit.

Think of the pyramid:

        Load tests
       /          \
   Integration tests
   /              \
      Unit tests
 /                  \
Fast + many      Slow + fewer

The strategy should balance feedback speed, cost, and realism.

Developer machine
→ local tests

Every PR
→ unit tests
→ fast quality checks

After build / test environment
→ integration tests

Before important release / scheduled
→ load tests in representative environment

Unit tests passing does not prove integrations work.
Integration tests passing does not prove performance under load.
Local tests do not replace CI validation.
Load tests should have explicit thresholds such as failure rate and P95 latency.
Heavy load tests usually should not run against production on every PR.
Run fast/cheap tests early; slower/more expensive tests later or on appropriate triggers.

### Implement tests in a pipeline, including configuring test tasks, configuring test agents, and integration of test results
Previously:

Developer
   ↓
pytest
   ↓
unit + integration tests

Now we want:

Azure Pipeline
   ↓
Test agent
   ↓
Install dependencies
   ↓
Execute tests
   ↓
Generate test results
   ↓
Publish results to Azure DevOps
   ↓
Pass/fail pipeline

There are three pieces in the exam point.

Test tasks execute or publish tests. The exact tooling depends on the technology: pytest for Python, dotnet test for .NET, Maven/Gradle for Java, and so on. Azure Pipelines also has tasks for publishing standardized result formats such as JUnit.

Test agents are the machines where those tests actually execute. You need an agent with the appropriate OS, runtime, dependencies, networking, and resources.

For example:

Microsoft-hosted Ubuntu agent
├── Python
├── install requirements
└── pytest

Self-hosted agent
├── organization's network access
├── custom software
└── perhaps access to private test systems

The important design question isn't simply “hosted or self-hosted?” It is:

What environment does the test require?

For ordinary isolated unit tests, a Microsoft-hosted agent is usually convenient. An integration test that must reach a private service inside a corporate network may require a suitably networked self-hosted agent.

Finally, test-result integration is different from merely printing test output.

This:

pytest

can fail the pipeline, but Azure DevOps gets much more useful information if pytest produces structured results:

pytest
   ↓
JUnit XML
   ↓
PublishTestResults
   ↓
Azure DevOps Tests UI
├── passed
├── failed
├── duration
└── individual test results

That gives you reporting, history, troubleshooting information, and visibility beyond raw console logs. The broader principle is: choose the agent based on the test's execution requirements, not because one agent type is universally better.

Use Microsoft-hosted agents when tests have standard requirements and don't need special network access.
Use self-hosted agents when you need private network connectivity, custom tooling, special hardware, or tighter environment control.
pytest output in logs is not the same as published test results.
Generate a supported format such as JUnit XML, then publish it.
Use condition: succeededOrFailed() so failed tests are still published and diagnosable.
An in-process Flask test client does not require a separately deployed API; a real external integration test would.

### Implement code coverage analysis
For pipelines, the goal is to make coverage:

generated automatically
attached to the pipeline run
visible in Azure DevOps
optionally enforced with a threshold

A typical flow is:

Tests run
   ↓
Coverage tool collects data
   ↓
Coverage report generated
   ↓
Pipeline publishes report
   ↓
Azure DevOps shows coverage summary
   ↓
Optional gate blocks low coverage

The key distinction is the same one you learned before:

Coverage reporting tells you the number.
Coverage enforcement turns that number into a gate.

And remember: high coverage does not prove high test quality. It only proves that tests executed the measured code.

100% passing tests can still coexist with low coverage.
High coverage does not guarantee good tests.
Generating coverage.xml does not automatically publish it.
Test results and coverage reports are separate outputs.
Use a condition like succeededOrFailed() so diagnostics remain available after failures.
A threshold such as --cov-fail-under=95 turns coverage into an enforced quality gate.

Fail the pipeline on policy violations, but preserve the diagnostic evidence needed to understand why it failed.