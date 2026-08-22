# Design and implement processes and communications

## Design and implement appropriate metrics and queries for DevOps
Delivery
Deployment at 14:14
      ↓
Operations
Errors spike at 14:15
      ↓
KQL / telemetry
Identify failing endpoint
      ↓
Logs / exceptions
Find root cause
      ↓
Bug/work item
      ↓
Source fix + PR
      ↓
Tests + security checks
      ↓
Deployment
      ↓
Operations
Verify error rate returns to normal
### Design and implement a dashboard, including flow of work, such as cycle times, time to recovery, and lead time

A useful mental model is:

Lead time: How long does the customer/request wait for value?

Cycle time: Once we start working, how long does it take us to finish?

The time to recovery is concerned with how quickly the team can restore service after a failure.

You'll also encounter the term MTTR. Be careful with the exact expansion because organizations/tools sometimes use it for mean time to recovery, restore, repair, or resolution. For AZ-400, concentrate on the concept: how quickly can we recover from a production failure?

Metrics should help identify bottlenecks and drive improvement, not just provide attractive charts.

Don't just collect metrics. Choose visualizations and queries that provide useful information for decisions.

- Lead time → request/creation to completion. Think customer wait for value.
- Cycle time → active work started to completion. Think delivery efficiency once work begins.
- Time to recovery → failure to service restoration. Think operational resilience.
- Queries define which data you're measuring; bad filters produce misleading metrics.
- Dashboards should expose trends, bottlenecks and actionable information—not just numbers.
- Compare metrics using equivalent teams, work-item types, and periods where appropriate.
- Consider averages, outliers, sample size, and trends before drawing conclusions.

And the diagnostic pattern you just worked through is particularly useful:

Lead ↑, Cycle stable → investigate waiting/planning/capacity before work starts.
Cycle ↑ → investigate the active delivery process.
Recovery ↑ → investigate detection, incident response, remediation and restoration.

### Design and implement appropriate metrics and queries for project planning
- How much work is waiting?
- How much can the team realistically complete?
- Are we consistently overcommitting?
- What work belongs to the next sprint/iteration?
- Which work is blocked or high priority?
- How much work remains toward a goal/release?

A query selects the work you're interested in

A metric summarizes or measures what's happening

And then dashboards/charts make those useful for planning

**Velocity**
Velocity = how much work the team historically completes.

One important planning concept is velocity.

But here's an important principle: velocity isn't supposed to become a target like:

“You did 22 last sprint, so management requires 30 next sprint.”

It's primarily a planning/forecasting signal based on historical delivery.

**Capacity**
Capacity = how much working availability the team has for the upcoming iteration.

There's also capacity, which asks how much working capacity the team actually has during an iteration.
For example, even if historical velocity is around 23 points, next sprint might contain holidays, vacations, training, etc.

**Queries**
Queries then let us extract actionable subsets of the backlog.

**Burndown**
How much work remains during the current iteration, and is it decreasing as expected?
Remaining work

40 │●
35 │  ●
30 │    ●
25 │       ●
20 │          ●
15 │             ●
10 │                ●
 5 │                   ●
 0 └──────────────────────
    Mon Tue Wed Thu Fri

| Planning question                                   | Tool/metric         |
| --------------------------------------------------- | ------------------- |
| What work matches certain criteria?                 | **Work-item query** |
| How much have we historically completed per sprint? | **Velocity**        |
| How much availability do we have next sprint?       | **Capacity**        |
| Are we on track during this sprint?                 | **Burndown**        |
| How long does work take once started?               | **Cycle time**      |
| How long from request to completion?                | **Lead time**       |

- Queries filter actionable subsets of work: priority, state, iteration, assignee, work-item type, etc.
- Velocity uses historical completed work to inform future planning.
- Capacity reflects actual team availability for an upcoming iteration.
- Burndown shows remaining work and progress during an iteration.

### Design and implement appropriate metrics and queries for development
For development, useful metrics and queries might answer questions such as: Are builds succeeding? Which PRs are waiting for review? Which builds are failing? Are tests becoming unreliable? How long are PRs sitting open? Where are development bottlenecks?

**Example: build success rate**
A useful development query could identify:
Open PRs that have been waiting for review for more than two days.

Development metrics should help expose bottlenecks and quality problems in the engineering process, then queries help identify the specific affected items or runs.

A compact set of development-focused metrics to recognize is:

- PR review/approval time
- PR age
- CI success/failure rate
- CI queue time
- CI execution duration
- Test pass/fail trends
- Flaky-test frequency
- Active/stale/unassigned bug counts
- Code-quality or security-check failures


### Design and implement appropriate metrics and queries for testing
How do we measure whether testing is effective, reliable, and giving developers useful feedback?

- Test pass rate / failure rate
- Test duration
- Flaky test rate
- Code coverage
- Number of failed tests by build/PR
- Trend of test failures over time
- Tests not run / skipped
- Failure concentration — e.g. one test causing most failures

High code coverage tells you how much code is exercised, not how effectively its behavior is validated.

Coverage asks “Did tests execute this code?”
Pass rate asks “Did the executed tests succeed?”
Test effectiveness asks “Would these tests catch meaningful defects?”

Measure → establish threshold → enforce → provide feedback.

Imagine a real Azure DevOps project has 10,000 test executions. A dashboard shows:

Test pass rate dropped from 98% to 89%.

A useful query/drill-down wouldn't simply show all 10,000 executions. We'd want something actionable, such as:

Failed tests from recent builds, grouped or filtered to identify recurring failures.

- Pass/fail rate tells you whether tests are succeeding.
- Code coverage tells you how much code is exercised, not whether the tests are good.
- Flaky-test rate helps identify unreliable tests.
- Test duration helps identify slow feedback loops.
- Failure trends help detect regressions over time.
- Queries/drill-downs help identify exactly which tests, builds, or areas are failing.
- Quality gates turn a metric into an enforced policy, such as coverage >= 80%.


### Design and implement appropriate metrics and queries for security
SECURITY METRIC
“How are we doing overall?”
        ↓
Examples:
Critical vulnerabilities: 3
High vulnerabilities: 12
Mean remediation time: 4 days
Secret detections this month: 2

SECURITY QUERY
“Which specific items need action?”
        ↓
Examples:
Critical vulnerabilities still open
High-severity findings older than 7 days
Security bugs with no assignee
Builds failing dependency scanning

Age matters as well as severity.

A better security dashboard would combine several dimensions:
Severity
   +
Age / remediation time
   +
Exposure / exploitability
   +
Trend
   ↓
Security risk picture

In a fuller DevSecOps pipeline you might have several signals:
Dependency scanning ──► vulnerable packages?
Secret scanning     ──► exposed credentials?
SAST                ──► insecure source patterns?
DAST                ──► runtime vulnerabilities?
Container scanning  ──► vulnerable image/packages?

- Metrics expose security posture and trends: vulnerability count by severity, remediation time, finding age, new findings, scan success/failure, etc.
- Queries identify actionable findings: e.g. High/Critical + unresolved + older than SLA.
- Risk/severity matters — don't prioritize purely by total count.
- Age matters — long-lived vulnerabilities can indicate remediation problems.
- Freshness matters — security results need to correspond to current changes/releases.
- Security gates can enforce policy by failing CI/CD checks.
- One scanner isn't complete security coverage — dependency scanning, SAST, secret scanning, and other controls answer different questions.

### Design and implement appropriate metrics and queries for delivery
For delivery, think primarily about the CI/CD path from a releasable change through deployment into an environment.

Useful delivery metrics include deployment frequency, deployment success/failure rate, deployment duration, lead time for changes, and rollback/recovery information. You'll notice some overlap with earlier metrics—that's normal because DevOps metrics span stages of the lifecycle.

Deployment frequency → How often are we delivering?
Success/failure rate → How reliable is delivery?
Deployment duration → How quickly does the delivery mechanism complete?

Deployment success rate → Did the deployment operation succeed?
Change failure rate → Did the deployed change cause a production failure requiring remediation?

This connects to the well-known DORA-style delivery metrics you should recognize for AZ-400:

Deployment frequency
Lead time for changes
Change failure rate
Time to restore/recover

- How often do we release?	Deployment frequency
- How long until a change reaches production?	Lead time for changes
- How often do changes cause production problems?	Change failure rate
- How quickly do we recover?	Time to recovery/restore
- Is the deployment mechanism working?	Deployment success/failure rate
- How long does deployment take?	Deployment duration
- Which deployments failed?	Query/drill-down into failed deployments

### Design and implement appropriate metrics and queries for operations
Is the production system healthy, reliable, and performing as expected?
healthy servers do not necessarily mean a healthy service.
A useful operations model to know is the four “golden signals”:
Latency      → How long are requests taking?
Traffic      → How much demand is the system receiving?
Errors       → How many requests are failing?
Saturation   → How close are resources to their limits?

Traffic explains increased demand.
Saturation tells us the system may no longer have enough capacity to handle that demand.

Typical operational metrics include:

- Availability / uptime
- Response time / latency
- Request/error rate
- Throughput
- CPU, memory, disk and network utilization
- Incident count
- Time to detect
- Time to recover/restore
- SLA/SLO compliance

A useful operational drill-down is:
Error-rate spike
   ↓
Group failures by status code
   ↓
500s dominate
   ↓
Query affected endpoints / dependencies / exceptions
   ↓
Find root cause

A typical Application Insights query to find failing requests could start with:

requests
| where success == false

Then we can summarize them:

requests
| where success == false
| summarize Failures = count() by resultCode
| order by Failures desc

Conceptually:

Telemetry
   ↓
Filter failed requests
   ↓
Group by result code
   ↓
Count
   ↓
Largest failure category

Don't worry about memorizing every KQL operator yet. For AZ-400, you should be comfortable recognizing the pattern:
where → filter
summarize → aggregate/calculate
by → group results
order by → sort

In KQL, bin() is commonly used to group timestamps into intervals.

For example:

requests
| where success == false
| summarize Failures = count() by bin(timestamp, 5m)
| order by timestamp asc

That produces failure counts in 5-minute buckets, which is suitable for a trend chart.

Conceptually:

14:00  2 failures
14:05  3 failures
14:10  4 failures
14:15  97 failures ⚠️
14:20  112 failures ⚠️

- Availability → Is the service reachable?
- Latency / P95 / P99 → How quickly are users getting responses, including tail latency?
- Error rate → How frequently are operations failing?
- Traffic/throughput → How much demand is the service handling?
- Saturation → Are resources approaching their limits?
- Time to detect/recover → How effective is incident response?
- SLA/SLO metrics → Are reliability objectives being met?
- KQL queries → drill into telemetry to determine which requests, endpoints, exceptions, dependencies, time periods, etc. explain a metric.