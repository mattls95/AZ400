# Design and implement processes and communications

## Design and implement traceability and flow of work

### Design and implement a structure for the flow of work, including GitHub Flow
For this objective:

**Design and implement traceability and flow of work
→ Design and implement a structure for the flow of work, including GitHub Flow**

The key phrase is “structure for the flow of work.” This is broader than simply knowing Git commands. Microsoft wants you to understand how work moves from:

**Requirement / issue → branch → commits → pull request → review/checks → merge → deployment**

GitHub Flow is one way to structure that movement.

At a high level, GitHub Flow is deliberately lightweight:

**main → create short-lived branch → commit changes → open PR → review + automated checks → merge to main → deploy**

A major principle is that main should remain deployable. Unlike GitFlow, you normally don't have permanent develop, release, and hotfix branches. Instead, work happens on short-lived branches and gets integrated frequently.

For AZ-400, think beyond “How do I create a branch?” The design questions are more like:

A team deploys continuously and wants developers to integrate changes frequently while keeping the main branch releasable. Which branching strategy is appropriate?

GitHub Flow is a strong candidate.

Or:

How do you ensure that changes cannot enter main without review and successful CI?

Now you're combining GitHub Flow with **pull requests, branch protection/rulesets, required reviewers, and required status checks**. That combination is much closer to what AZ-400 cares about.

One distinction worth locking in early:

**GitHub Flow ≠ GitFlow**

GitHub Flow is roughly:
main
 **├──── feature/login ───── PR ──► main
 │
 └──── fix/payment ─────── PR ──► main**

 GitFlow has more long-lived structure:
 **main
  ↑
release
  ↑
develop
  ↑
feature branches**
GitFlow can make sense where releases are carefully staged or maintained separately, but GitHub Flow generally fits continuous integration / continuous delivery better because branches stay short-lived.

For AZ-400, I’d lock in these points:

- GitHub Flow favors short-lived branches.
- Changes reach main through pull requests.
- Rulesets/branch protection enforce the workflow.
- Required status checks turn CI from informational into a merge gate.
- Long-lived branches increase merge-conflict and integration risk.
- After merge, the feature branch is usually deleted

### Design and implement a strategy for feedback cycles, including notifications and GitHub Issues
A feedback-cycle strategy generally connects:
Something happens
       ↓
Feedback is captured
       ↓
Right people are notified
       ↓
Someone responds
       ↓
Work is tracked
       ↓
Resolution

GitHub Issues can provide the tracked work item, while notifications help get information to the people who need to respond.

**GitHub Issues** An Issue isn't just “there's a bug.” Issues can represent things like bugs, feature requests, technical debt, or other actionable work.

Instead of having:
**Bug → ??? → some code changed → ???**

we can establish:
**Feedback → Issue #42 → PR #51 → commits → CI → merge → Issue closed**

Notifications answer questions such as:

Who needs to know? When? Through which channel? For which events?
GitHub Issues capture and track feedback. Notifications surface relevant events to the appropriate people.

Now connect it to traceability

Alice fixes Issue #12 on a feature branch and creates PR #18.

We want GitHub to connect the implementation back to the original feedback and automatically close the Issue when the fix is merged.

GitHub supports special keywords in a PR description, for example:
Fixes #12
Now we've created end-to-end traceability:
User feedback
     ↓
Issue #12
     ↓
Assigned + labeled
     ↓
PR #18 — "Fixes #12"
     ↓
CI / review
     ↓
Merge to main
     ↓
Issue #12 closed

Exactly. That shows you understand the distinction:

Fixes #12 → establishes the relationship and closes the Issue when the PR is merged.

Related to #12 → useful human-readable reference/traceability, but doesn't tell GitHub to close the Issue.

One nuance for the exam: GitHub recognizes specific closing keywords such as fixes, closes, and resolves. “Related to” isn't a closing keyword. Design notifications so actionable feedback reaches the right people without flooding everyone.

- GitHub Issues capture and track feedback/work.
- Labels categorize Issues.
- Assignees establish ownership.
- Issue references such as #2 improve traceability.
- Closing keywords such as Fixes #2, Closes #2, and Resolves #2 connect PRs to Issues and can automatically close them after merge.
- PR comments/reviews create feedback loops during development.
- Notifications should get relevant information to the right people without excessive noise.
- @mentions are useful for targeted/ad-hoc attention.
- A good feedback strategy considers who needs what feedback and when, rather than simply maximizing notifications.

### Design and implement integration for tracking work, including GitHub projects, Azure Boards, and repositories
GitHub Projects — planning and organizing work around GitHub Issues/PRs, with views such as tables, boards, and roadmaps.

Azure Boards — Azure DevOps work tracking: epics, features, user stories/product backlog items, tasks, bugs, queries, backlogs, sprints, etc.

Repositories — where the implementation lives: commits, branches and PRs, whether that's GitHub repositories or Azure Repos.

With integration, we're aiming for:
Requirement
    ↓
Work item
    ↓
Repository
    ↓
Branch / commit
    ↓
Pull request
    ↓
Build / deployment

GitHub Projects vs GitHub Issues

There's also a distinction worth establishing before we touch Azure Boards.

Earlier, you created Issue #2 for updating the README.

Imagine the team now has 150 Issues across bugs, documentation, features, and technical debt.

GitHub Issues store the individual pieces of work. A GitHub Project can help organize and visualize those items, for example:

Todo          In Progress       Done

Issue #21     Issue #7          Issue #2
Issue #25     Issue #14         Issue #18
Issue #31

GitHub Issue = an individual unit of work.
GitHub Project = a planning/tracking view across work items and their status.
And importantly, Projects aren't just static boards. You can use different views and fields to organize work—for example status, priority, iteration, assignee, tables, boards, and roadmaps.

Now compare that with Azure Boards

Azure Boards solves a similar work-management problem but sits in the Azure DevOps ecosystem and supports a more structured hierarchy.
Epic
└── Feature
    └── User Story
        ├── Task
        └── Task

Don't confuse the work-tracking system with the source-control system.
integrate systems when each already serves its role well, rather than moving everything into one platform just for traceability.
the planning artifact and implementation artifact can live in different systems while remaining traceable.

- GitHub Projects → organize and visualize GitHub Issues/PRs and their status.
- Azure Boards → structured work tracking, backlog/sprint planning, work-item hierarchy.
- Repositories → implementation lives in commits, branches, and PRs.
- Integration connects the planning/work item to the actual code change.
- AB#<ID> → link GitHub activity to an Azure Boards work item.
- Fixes AB#<ID> → link it with resolution/state-transition intent.

### Design and implement source, bug, and quality traceability
Source traceability answers:

Which commit, branch, and PR implemented this work?

Bug traceability answers:

Which bug/work item caused this change, and which change resolved it?

Quality traceability answers:

What evidence shows that this specific change passed validation?
Now quality traceability extends that chain:
Bug #42
   ↓
PR #51
   ↓
Commit abc123
   ↓
CI run #900
   ├── Build ✅
   ├── Unit tests ✅
   ├── Security scan ✅
   └── Code quality ✅
   ↓
Merge

BUG TRACEABILITY
"Why was this change made?"
Bug #42 ──────► PR #51

SOURCE TRACEABILITY
"What code implemented it?"
PR #51 ──────► Commit abc123

QUALITY TRACEABILITY
"How do we know that code was good?"
Commit abc123 ──────► Build ──────► Test results

| Traceability | Question                                                            |
| ------------ | ------------------------------------------------------------------- |
| **Source**   | What code/commit/PR produced this artifact or change?               |
| **Bug**      | Which work item/bug led to the change, and what change resolved it? |
| **Quality**  | What build/tests/quality checks validated that source revision?     |


Bug #42
  │
  │  Bug traceability
  ▼
PR #51
  │
  │  Source traceability
  ▼
Commit abc123
  │
  │  Quality traceability
  ▼
Build #700
  ↓
Tests / quality checks ✅

“Which code/change/build produced this?” → source traceability
“Which change fixed this defect?” → bug traceability
“Did this version pass its tests/quality gates?” → quality traceability

| Type                     | Think                                    |
| ------------------------ | ---------------------------------------- |
| **Source traceability**  | **What code produced this?**             |
| **Bug traceability**     | **Why was this code changed?**           |
| **Quality traceability** | **What proves this code was validated?** |
