# Design and implement a structure for the flow of work, including GitHub Flow

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
