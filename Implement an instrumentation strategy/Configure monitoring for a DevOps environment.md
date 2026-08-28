# Implement an instrumentation strategy

## Configure monitoring for a DevOps environment

### Configure Azure Monitor and Azure Monitor Logs to integrate with DevOps tools
The security section asked:

“Is our software secure and compliant?”

Instrumentation asks:

“What is happening in our applications and infrastructure, and how do DevOps teams use that information?”

Azure Monitor — umbrella service

Start with this mental model:

Azure Monitor
│
├── Metrics
│   → numerical time-series data
│
├── Logs
│   → queryable records/events
│
├── Application Insights
│   → application telemetry/APM
│
└── Alerts
    → react when conditions are met

Azure Monitor is the broad monitoring platform. Azure Monitor Logs is the log-data/query side of it.

Metrics vs logs

This is an important exam distinction.

Metrics are optimized for numerical measurements over time:

CPU Percentage
Requests/sec
Memory usage
Response time

Think:

“What is the value right now/over time?”

Logs contain richer records:

timestamp
operation
resource
status
message
duration
custom fields

Think:

“What happened, and what details can I investigate?”

Logs are queried using Kusto Query Language (KQL).

For example, conceptually:

AzureActivity
| where TimeGenerated > ago(1h)
| summarize count() by OperationNameValue

You don't need to become a KQL expert for this objective, but you should recognize what KQL is used for.

Log Analytics workspace

Azure Monitor Logs commonly stores/query data through a Log Analytics workspace.

Azure resources
      │
      │ telemetry/logs
      ▼
Log Analytics workspace
      │
      ▼
KQL queries
      │
      ├── investigation
      ├── dashboards/workbooks
      └── alerts

A common exam trap is thinking:

“Creating a Log Analytics workspace automatically collects every log from every Azure resource.”

It doesn't.

You still need to configure what telemetry gets sent where, depending on the resource and telemetry type.

Diagnostic settings

A major Azure Monitor concept is diagnostic settings.

Conceptually:

Azure resource
     ↓
Diagnostic setting
     ↓
choose logs/metrics
     ↓
Destination
     ├── Log Analytics workspace
     ├── Storage
     └── Event Hub

Different destinations serve different purposes.

For our DevOps monitoring scenario, Log Analytics is especially useful because we want centralized querying and investigation.

Where does DevOps fit?

Monitoring shouldn't be something only operations staff inspect manually in the Azure portal.

A useful DevOps loop is:

Deploy
  ↓
Application runs
  ↓
Azure Monitor collects telemetry
  ↓
Logs / metrics analyzed
  ↓
Alert / finding
  ↓
DevOps team investigates
  ↓
Work item / incident / remediation
  ↓
New deployment

That's the key phrase in the objective: integrate with DevOps tools.

For example, monitoring information can drive notifications, incidents, automation, dashboards, or development work rather than simply sitting in Azure Monitor.

Don't confuse Application Insights and Log Analytics

We'll cover Application Insights more deeply in later instrumentation objectives, but establish this now:

Application Insights
→ application performance/behavior telemetry
→ requests, dependencies, exceptions, traces

Log Analytics workspace
→ centralized log store/query environment
→ KQL across collected monitoring data

Modern Application Insights is workspace-based, so the two integrate closely—but they aren't simply two names for the same thing.

- Azure Monitor is the overall monitoring platform; Azure Monitor Logs handles queryable log data.
- Creating a Log Analytics workspace doesn't automatically collect every resource's logs.
- Diagnostic settings determine what supported resource telemetry is routed and where.
- Metrics suit numerical time-series monitoring; Logs + KQL suit detailed investigation.
- An alert rule defines when to react; an action group defines the downstream response.


### Configure collection of telemetry by using Azure Monitor Application Insights, Azure VM Insights, Azure Container Insights, Azure Monitor for Storage, and Azure Monitor for Networks
The previous objective established the plumbing:

Resource
→ telemetry
→ Azure Monitor
→ Log Analytics
→ KQL
→ alerts/actions

This objective asks which Azure Monitor capability you use for different workload types and what telemetry each gives you.

A useful AZ-400 map is:

Application
→ Application Insights

Virtual machines
→ VM Insights

AKS / containers
→ Container Insights

Storage accounts
→ Azure Monitor for Storage

Network resources
→ Azure Monitor for Networks
Application Insights

Application Insights is application performance monitoring (APM).

Instead of primarily asking whether the Azure resource itself is healthy, it asks questions such as:

How many requests?
How long do requests take?
Which requests fail?
Which exceptions occur?
What dependencies does the app call?

Typical telemetry includes:

Requests
Dependencies
Exceptions
Traces
Availability
Performance data

An important concept is distributed tracing.

Imagine:

Browser
   ↓
Orders API
   ↓
Payment API
   ↓
SQL database

Application Insights can help trace a request through dependencies, which is much more useful for application troubleshooting than simply knowing that CPU is 42%.

Application instrumentation can be performed using approaches such as automatic instrumentation or the Azure Monitor OpenTelemetry libraries/distribution, depending on the workload.

VM Insights

VM Insights focuses on virtual machine performance and health.

Think:

VM
├── CPU
├── memory
├── disk
├── processes/dependencies
└── performance trends

This commonly involves the Azure Monitor Agent (AMA) and a Data Collection Rule (DCR).

That's a new architectural concept worth knowing:

Azure Monitor Agent
        ↓
Data Collection Rule
        ↓
defines what data to collect
and where it should go
        ↓
Azure Monitor / Log Analytics

A DCR is therefore different from the diagnostic settings we used previously, even though both participate in telemetry collection.

Container Insights

Container Insights is designed for containerized environments, particularly Kubernetes/AKS monitoring.

Think:

AKS cluster
├── nodes
├── pods
├── containers
├── workloads
└── container logs/performance
       ↓
Container Insights

This helps answer questions like:

Which pod is restarting?

Which node is under resource pressure?

Which container generated this log?

Don't confuse it with the container security scanning you just studied:

Container image scanning
→ security vulnerabilities BEFORE/around deployment

Container Insights
→ telemetry from RUNNING container workloads

Very different objectives.

Azure Monitor for Storage

Storage monitoring focuses on Azure Storage services and their behavior.

Typical concerns include:

Availability
Latency
Transactions
Capacity
Errors
Throttling

For example, if an application suddenly becomes slow because Storage requests are being throttled, Storage monitoring can help establish that the problem isn't necessarily in your application code.

Azure Monitor for Networks

Network monitoring focuses on the network layer and network resources.

Conceptually:

Application reports:
"Backend unreachable"
        ↓
Is application broken?
Is VM down?
Is DNS wrong?
Is network path blocked?
        ↓
Network monitoring

Depending on the Azure networking scenario, you may use monitoring capabilities around connectivity, topology, network resources, metrics, logs, and Network Watcher capabilities.

For the exam, the important first decision is often simply:

What layer are we troubleshooting?

One workload can need several

Don't assume you pick exactly one monitoring product per application.

For example:

Web API on AKS
│
├── Application Insights
│   → request performance
│
├── Container Insights
│   → pods/nodes/containers
│
├── Storage monitoring
│   → Blob/Queue performance
│
└── Network monitoring
    → connectivity/network behavior

This is layered observability.

If users report that an API is slow:

Application Insights
→ request duration is high

Container Insights
→ pod CPU normal

Storage monitoring
→ storage latency very high

Now you have evidence that helps narrow down the bottleneck.

Application Insights = APM, requests, dependencies, failures, performance.
VM Insights = VM/guest performance and monitoring.
Container Insights = running Kubernetes/container workloads.
Storage monitoring = storage transactions, latency, availability, capacity/errors.
Network monitoring = connectivity and network-resource behavior.
AMA + DCR = agent-based collection where the DCR controls what is collected and where it's sent.

### Configure monitoring in GitHub, including enabling insights and creating and configuring charts
This objective changes layers again. We aren't monitoring the Azure application itself; we're monitoring development activity and project work inside GitHub.

There are two GitHub concepts worth separating:

Repository Insights
→ repository activity and usage

GitHub Projects Insights
→ project/work-management charts
Repository Insights

A repository's Insights area provides built-in graphs for understanding repository activity. Depending on repository/plan, this can include things such as Pulse, Contributors, Traffic, Commits, Code frequency, Network, Forks, and Dependency graph.

Examples:

Question                              Insight

Who contributes to this repo?        Contributors
How often is code committed?         Commits
How much code changes over time?     Code frequency
Who visits/clones the repository?    Traffic
What's happening recently?           Pulse
What does the project depend on?     Dependency graph

For example, Traffic can show repository visitors, full clones, referring sites, and popular content. GitHub currently retains the visitor/clone view for the past 14 days.

We've already encountered Dependency graph during the Dependabot objective. That's overlap, so we won't repeat it.

GitHub Projects Insights and charts

This is the more important "creating and configuring charts" part.

A GitHub Project contains structured work items:

Issues / PRs
     ↓
GitHub Project
     ↓
Fields

Status
Priority
Assignee
Iteration
etc.
     ↓
Insights
     ↓
Charts

Project Insights lets you create and customize charts based on the items in the project. You can configure filters, chart type, and the information displayed.

There are two important chart concepts:

Current charts answer questions about the project now, such as:

How many items are in each status?
How much work is assigned to each person?
How many bugs are in each iteration?

Historical charts show changes over time. GitHub's built-in Burn up chart is an example and can show completed work versus remaining/open work over time.

You can also filter chart data. For example:

label:bug

could restrict the chart to bugs, while multiple different fields effectively combine as AND conditions.

So remember:

Project items + fields
        ↓
filter
        ↓
group / axes / chart configuration
        ↓
useful engineering insight

### Configure alerts for events in GitHub Actions and Azure Pipelines
This overlaps somewhat with Azure Monitor alerts, so we'll focus on what's new: pipeline/workflow events and how DevOps teams get notified when they occur.

The central pattern is:

Pipeline/workflow event
        ↓
Condition/event occurs
        ↓
Notification mechanism
        ↓
Developer / team / external system

Examples of events:

Workflow failed
Pipeline completed
Deployment failed
PR validation failed
Build status changed
Approval required
GitHub Actions

GitHub provides built-in notifications for Actions workflow activity, particularly workflow failures and related repository activity.

But there's an important distinction:

GitHub notification
→ notify a GitHub user

Workflow automation
→ react programmatically to an event

For more customized behavior, workflows can react to events and conditions.

For example:

- name: Notify on failure
  if: failure()
  run: ./notify-team.sh

The important concept isn't the script. It's:

if: failure()

The notification step runs only when earlier work has failed.

There are several useful status-check functions in GitHub Actions:

failure()   → previous work failed
success()   → previous work succeeded
cancelled() → workflow/job was cancelled
always()    → run regardless of outcome

Be careful with always(): it means the step/job should run regardless of success or failure, so it isn't synonymous with "notify on failure."

GitHub can also send events to external systems through mechanisms such as webhooks, which is useful when monitoring needs to feed another DevOps/incident-management system.

Azure Pipelines

Azure DevOps has a built-in Notifications system based on subscriptions.

Conceptually:

Event
→ notification subscription
→ filters
→ recipient

There are built-in/default subscriptions, and users/teams can create custom notification subscriptions.

For example:

Event:
Build completes

Filter:
Status = Failed
AND
Pipeline = Production-API

Recipient:
Operations team

Azure DevOps also has service hooks when an event needs to trigger an external service rather than simply notify a person.

Think:

Notifications
→ humans need to know

Service hooks
→ another system needs to react

For example:

Pipeline fails
     ↓
Service hook
     ↓
External incident/automation system

This is similar conceptually to what you learned with Azure Monitor:

Azure Monitor:
Alert rule → Action group

Azure DevOps:
Event → Notification subscription / Service hook

GitHub:
Workflow/repository event → Notification / workflow logic / webhook

Different implementations, same DevOps principle: detect an event and route an appropriate response.
