# Implement an instrumentation strategy

## Analyze metrics from instrumentation

### Inspect infrastructure performance indicators, including CPU, memory, disk, and network
We've already covered collecting infrastructure telemetry with Azure Monitor, VM Insights, AMA and DCR.

This objective shifts from:

"How do I collect telemetry?"
          ↓
"How do I interpret infrastructure telemetry?"

The four core signals are CPU, memory, disk, and network. The exam will often give you several indicators and ask which resource is the likely bottleneck.

CPU

CPU indicates processor utilization.

High CPU
→ workload may be compute-bound
→ slow processing
→ increased request duration

But one spike isn't necessarily a problem.

CPU
100% |             ┌─┐
 50% |───────┬─────┘ └────
  0% +---------------------

Brief spike → potentially normal

Sustained 90–100%
→ much more interesting

So think trend and duration, not just maximum value.

Memory

Memory pressure is slightly trickier.

Suppose:

CPU:       35%
Memory:    96%
Disk:      normal
Network:   normal

CPU isn't the bottleneck. The machine may instead be under memory pressure.

Possible consequences include paging/swapping, application slowdown, or out-of-memory failures.

An important Azure monitoring distinction is that host/platform metrics and guest OS metrics aren't identical. CPU can commonly be observed from Azure platform metrics, while detailed guest memory information may require guest-level monitoring such as VM Insights/AMA.

That's a common exam trap:

"Why can't I find the detailed memory metric I expected?"

Think guest OS telemetry collection.

Disk

Disk problems aren't just about:

"Is the disk full?"

Two dimensions matter:

Capacity
→ how much storage is consumed?

Performance
→ can storage handle the workload?

Useful performance indicators include:

IOPS
→ operations per second

Throughput
→ amount of data transferred per second

Latency
→ time required for I/O

Queue depth
→ work waiting for disk I/O

For example:

CPU:          30%
Memory:       55%
Disk latency: very high
Disk queue:   growing

The likely bottleneck is storage I/O, even though the VM itself has plenty of CPU.

Network

Network indicators help identify communication bottlenecks.

Think about:

Bytes sent / received
Packets
Throughput
Connection behavior
Drops/errors where applicable

But remember our previous objective:

Infrastructure network metrics
→ "How much network activity is occurring?"

Network troubleshooting
→ "Why can't A reach B?"

The second may require network-specific diagnostic capabilities rather than merely looking at a bytes-received graph.

Correlation is the real skill

Don't inspect each graph in isolation.

Imagine an API becomes slow at 14:00:

Request latency ↑
       +
CPU ↑ to 98%
       +
Memory normal
       +
Disk normal
       +
Network normal

That strongly suggests a CPU bottleneck.

But:

Request latency ↑
       +
CPU 25%
       +
Memory normal
       +
Disk latency ↑ dramatically
       +
Disk queue ↑

points toward disk I/O.

This is why instrumentation is useful to DevOps:

Deployment
    ↓
Performance changes
    ↓
Correlate metrics
    ↓
Identify bottleneck
    ↓
Fix / scale / rollback

Don't diagnose from a single high number. Correlate CPU, memory, disk, network, time window, limits, and application behavior.

### Analyze metrics by using collected telemetry, including usage and application performance
We've moved through three stages:

Collect telemetry
→ Inspect telemetry
→ Analyze telemetry to make decisions
Usage metrics

Usage telemetry answers how users interact with the application, rather than whether the infrastructure is healthy.

Think:

How many users?
How many sessions?
Which features/endpoints are used?
How frequently?
What user flows occur?

For example:

/api/orders     15,000 requests
/api/reports       120 requests
/api/search      8,000 requests

That tells us something about usage patterns, but not necessarily performance.

Application Insights can use telemetry such as requests, page views, users/sessions, and custom events to understand application usage.

A useful distinction:

Usage
→ WHAT are users doing?

Performance
→ HOW WELL is the application responding?
Application performance

You've already inspected AppRequests with fields such as:

Name
ResultCode
DurationMs
Success

Now the important skill is interpreting them together.

Imagine:

             Requests    Avg duration    Failure rate
/checkout      10,000       300 ms            1%
/search        25,000      2,800 ms            1%
/admin            50       200 ms            0%

/search deserves attention because it's both heavily used and slow.

That's stronger evidence for prioritization than simply saying:

“The slowest request we ever recorded took 10 seconds.”

One extreme request could be an outlier.

Average vs percentiles

This is an important monitoring concept.

Suppose request durations are mostly:

100 ms
110 ms
120 ms
130 ms
5000 ms

The very slow request pulls the average upward.

Performance monitoring therefore often uses percentiles, such as:

P50 → 50% of observations are at or below this value
P95 → 95% are at or below this value
P99 → 99% are at or below this value

So if:

Average = 400 ms
P95     = 2.5 sec

the average alone can hide a poor experience affecting the slower tail of requests.

For exam questions, phrases such as "slowest users," "tail latency," or "95% of requests" should make you think about percentiles.

Correlation matters again

You practiced this with CPU/memory/disk/network. The same idea applies at the application layer.

For example:

Deployment at 14:00
        ↓
Request duration ↑
Failure rate ↑
        ↓
Application Insights telemetry
        ↓
Investigate deployment/change

Or:

Usage ↑ 300%
     +
Request latency ↑
     +
CPU ↑
     ↓
Possible capacity/scaling problem

So the goal isn't merely to create dashboards. It's to use telemetry to answer:

What changed, who is affected, how badly, and what evidence points toward the cause?

Don't just find the worst number. Determine how many users/requests are affected, correlate it with other telemetry and changes, and prioritize based on impact.

### Inspect distributed tracing by using Azure Monitor Application Insights
What problem does distributed tracing solve?

Consider:

Client
  ↓
Orders API
  ↓
Payment API
  ↓
SQL Database

A user reports:

"POST /orders took 4 seconds."

Knowing the total duration isn't enough. We want to know where those 4 seconds were spent.

Distributed tracing follows the request across components:

POST /orders                 4.0 s
│
├── Validate order           0.1 s
│
├── Payment API              3.5 s  ⚠️
│
└── SQL                      0.2 s

Now the evidence points toward the Payment API/dependency.

Requests vs dependencies

This distinction is fundamental.

For the Orders API:

Incoming:
Client → Orders API
         ↑
       Request telemetry

Outgoing:
Orders API → Payment API
             ↑
          Dependency telemetry

So:

Request telemetry = work received by the application.

Dependency telemetry = external/downstream work called by the application, such as HTTP services or databases.

Application Insights can correlate these pieces so they belong to the same end-to-end operation.

Correlation

Underneath the visual experience, telemetry contains correlation information.

Conceptually:

Operation
│
├── Request A
│     └── Dependency B
│           └── Request B
│                 └── Dependency C

This parent/child relationship allows Application Insights to reconstruct a distributed operation rather than showing four unrelated telemetry records.

Modern distributed tracing commonly uses W3C Trace Context, with concepts such as a trace ID identifying the overall distributed trace and span IDs identifying individual operations.

For AZ-400, focus on the concept rather than memorizing every header:

Trace ID
→ correlate operations belonging to one distributed trace

Span
→ one operation within that trace

Parent/child relationship
→ reconstruct call hierarchy
Application Map vs Transaction Search

You've already used Transaction search.

Now distinguish it from Application Map.

Transaction Search
→ investigate individual telemetry/transactions

Application Map
→ visualize application components
  and their dependencies

An Application Map might conceptually show:

Orders API
   │
   ├──── Payment API
   │
   └──── SQL Database

It helps identify dependency relationships and problematic components.

Then end-to-end transaction details let you drill into a particular distributed operation.

A useful workflow is:

Application Map
→ identify suspicious component/dependency
→ inspect transactions
→ follow distributed trace
→ locate slow/failing operation
Distributed tracing vs infrastructure monitoring

Important exam trap:

"Which VM has high CPU?"
→ infrastructure metrics / VM Insights

"Which service in this request chain is slow?"
→ Application Insights distributed tracing

And distributed tracing isn't simply centralized logging either.

Logs might say:

Orders API log
Payment API log
Database log

Distributed tracing adds the crucial relationship:

These operations all belong to the same request.

### Interrogate logs using basic Kusto Query Language (KQL) queries
You've already used KQL in the previous labs:

AppRequests
| where TimeGenerated > ago(30m)
| project TimeGenerated, Name, ResultCode, DurationMs, Success
| order by TimeGenerated desc
| take 20

So we'll focus on being able to read, modify, and construct basic KQL, rather than repeating the Log Analytics setup.

The mental model is a pipeline:

Table
  |
  | where ...
  | project ...
  | summarize ...
  | order by ...

Each | passes the current result into the next operator.

The operators to know

where — filter rows

AppRequests
| where TimeGenerated > ago(1h)
| where Success == false

Think:

Keep only requests from the last hour that failed.

project — choose columns

AppRequests
| project TimeGenerated, Name, ResultCode, DurationMs

This doesn't filter requests. It controls which fields appear in the result.

where   → which ROWS?
project → which COLUMNS?

That's an easy exam distinction.

order by — sort

AppRequests
| order by DurationMs desc

desc means largest → smallest.

Useful for:

Show me the slowest requests.

take / limit — return a small number

AppRequests
| take 10

Useful for exploration, but don't confuse it with filtering or aggregation.

summarize — the important one

This is where KQL becomes analysis rather than just searching.

AppRequests
| summarize RequestCount = count() by Name

Conceptually:

Thousands of individual requests
        ↓
group by Name
        ↓
Name              RequestCount
GET /                  1200
GET /api/orders         850
POST /api/orders        320

You can calculate things such as:

AppRequests
| summarize
    Requests = count(),
    AvgDuration = avg(DurationMs)
    by Name

Now you're combining usage + performance, exactly like the previous objective.

Time buckets with bin()

Very useful for monitoring.

Instead of:

10:01 request
10:02 request
10:03 request
...

you can group observations into time intervals:

AppRequests
| summarize Requests = count() by bin(TimeGenerated, 5m)

Meaning:

Count requests in 5-minute buckets.

This is particularly useful for trends and alert queries.

extend — create a calculated column

project selects output columns. extend adds a calculated column.

For example:

AppRequests
| extend DurationSeconds = DurationMs / 1000.0

Think:

project → select columns
extend  → calculate/add a column
Common conditions

You'll also encounter:

| where ResultCode == "500"
| where DurationMs > 2000
| where Name contains "orders"
| where ResultCode startswith "5"

And boolean logic:

| where Success == false and DurationMs > 2000

Or:

| where ResultCode == "500" or ResultCode == "503"

The exam generally cares much more about understanding the intent of a query than remembering obscure KQL syntax.

where       → filter rows
project     → select columns
extend      → create calculated columns
summarize   → aggregate/group
count()     → count records
avg()       → calculate average
bin()       → create time buckets
order by    → sort
take/limit  → restrict number of results
ago()       → relative time filtering

TABLE
| where ...
| summarize ... by ...
| order by ...