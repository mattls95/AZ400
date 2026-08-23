# Design and implement a source control strategy

## Design and implement branching strategies for the source code

### Design a branch strategy, including trunk-based, feature branch, and release branch
At a high level, the exam wants you to choose a branching model that fits the team's release cadence, integration needs, and risk tolerance.

Trunk-based:
Trunk-based development minimizes divergence from the main integration branch. Trunk-based does not necessarily mean “everyone commits directly to main with no review.” Teams can still use very short-lived branches and PRs—as long as they merge quickly and don’t become long-lived isolation branches.
main
 ├─ very short-lived branch
 ├─ very short-lived branch
 └─ frequent merges back to main

Feature branch:
The feature branch provides temporary isolation for a specific change, then disappears after integration. It isn't intended to maintain a separate product version.
main
 ├──────── feature/login
 ├──── feature/orders
 └──────── feature/search

Release branch:
So release branches are useful when you truly need parallel supported versions, but they increase the risk of branch drift and missed fixes.
main
 └──── release/2.4
          ├─ stabilization fixes
          └─ release-specific changes

Trunk-based development keeps everyone integrating into one primary branch very frequently. Branches, if used, are extremely short-lived. It fits continuous integration well because developers avoid drifting far from the shared codebase.

Feature branching isolates a piece of work on its own branch until it's ready to merge. That's essentially what you practiced with GitHub Flow. It gives isolation and PR-based review, but long-lived feature branches increase merge and integration risk.

Release branches are created when a particular release needs to be stabilized or maintained independently from ongoing development. For example:
main
  │
  ├── new features for 3.0 continue here
  │
  └── release/2.5
        ├── bug fix
        ├── security patch
        └── testing/stabilization

A useful design shortcut is:

Need very frequent integration? → trunk-based
Need isolated development per change? → feature branches
Need to stabilize or maintain a release separately? → release branch

Strategy	Primary purpose
Trunk-based	Integrate small changes very frequently
Feature branch	Temporarily isolate development of a change
Release branch	Maintain/stabilize a release independently

Feature branches become problematic when they live too long:
main       A──B──C──D──E──F──G
            \
feature      X──Y────────────Z
                              ↑
                      large divergence

Meanwhile, main has changed significantly. When the feature finally comes back, you may face large merge conflicts and discover integration problems very late. Feature flags become important here. Rather than keeping:

feature/new-checkout

isolated for three weeks, they could merge incomplete pieces into main behind a feature flag:

main
 ├── checkout code increment 1
 ├── checkout code increment 2
 ├── checkout code increment 3
 │
 └── Feature flag: NewCheckout = OFF

 Production can continue using the old behavior while development code is continuously integrated.

 Frequent integration
small changes
continuous delivery
        ↓
TRUNK-BASED


Temporary isolation
for a particular feature/change
        ↓
FEATURE BRANCH


Maintain/stabilize
a release independently
        ↓
RELEASE BRANCH

Trunk-based → frequent integration; branches, if used, are short-lived. Works particularly well with strong CI/CD and feature flags.
Feature branches → temporary isolation of a specific change. Avoid letting them become unnecessarily long-lived.
Release branches → independently stabilize or maintain a particular release/version.
Hybrid strategies → completely reasonable when requirements justify them.
Feature flags → allow incomplete functionality to be continuously integrated without exposing it to users.
Branch policies → protect whichever strategy you choose with PR reviews, status checks, tests, security checks, etc.

### Design and implement a pull request workflow by using branch policies and branch protection rules

Define how a pull request is validated and approved before code can enter a protected branch. A PR workflow is not just “open PR → merge.” It is usually:
Developer branch
      ↓
Pull Request
      ↓
Automated validation
      ↓
Review / approval
      ↓
Policy checks
      ↓
Merge to protected branch

The two main enforcement mechanisms are:
- GitHub branch protection / rulesets
- Azure Repos branch policies

Typical controls include:
- Require a PR before merging
- Require one or more reviewers
- Require successful build/status checks
- Require branch to be up to date
- Restrict who can push or bypass policies
- Require conversation resolution
- Require specific reviewers or code owners

For AZ-400, the key distinction is between technical validation and human review:
- Status checks answer: “Does the code build/test successfully?”
- Review policies answer: “Has an appropriate person examined and approved the change?”
- Latest code → latest checks → valid approvals → merge
- Branch policies are only as strong as their bypass model.

- protected target branches
- PR required before merge
- required reviewers
- stale approvals invalidated after new commits
- required status/build checks
- up-to-date validation where appropriate
- resolved review conversations
- tightly controlled bypass permissions

### Implement branch merging restrictions by using branch policies and branch protection rules
Who is allowed to merge, under what conditions, and by which merge methods?
Typical restrictions include:

Require PRs before merge
- Require specific reviewers or a minimum number of approvals
- Block merge if required status checks fail
- Require conversations to be resolved
- Require the branch to be up to date
- Restrict who can push or merge to protected branches
- Control allowed merge methods, such as merge commit, squash, or rebase
- Limit or audit bypass permissions

The last two points are what make this bullet a bit different from the previous one.

PR ready?
   ↓
Checks passed?
   ↓
Approvals valid?
   ↓
Conversations resolved?
   ↓
User allowed to merge?
   ↓
Allowed merge method?
   ↓
Merge


Merge methods

Another part of merge restrictions is deciding how history should be integrated.

Consider these three common approaches:

Merge commit
feature ──A──B──┐
                M── main

Squash merge
feature ──A──B──C
                ↓
main ───────────S

Rebase
feature commits replayed
onto latest main

For this AZ-400 bullet, keep these distinctions clear:
- Approval restriction → who must review/approve?
- Merge permission → who may actually merge?
- Direct-push restriction → can anyone bypass PRs?
- Status/review requirements → what conditions must be satisfied?
- Merge-method restriction → squash, merge commit, rebase, etc.
- Bypass control → who can override protections?