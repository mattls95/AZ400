# Design and implement build and release pipelines

## Design and implement a package management strategy

### Recommend package management tools including GitHub Packages and Azure Artifacts

Source control stores source. Package management stores versioned reusable artifacts.
Examples of packages:

NuGet packages
npm packages
Maven packages
Python packages
Universal Packages
Container images, depending on the platform/service

A typical flow is:

Source code
   ↓
Build
   ↓
Package
   ↓
Package registry/feed
   ↓
Other apps/pipelines consume versioned package

Two services explicitly named by the exam are:

GitHub Packages

- Closely integrated with GitHub repositories and GitHub Actions.
- Natural fit for teams already centered on GitHub.
- Supports multiple package ecosystems.

Azure Artifacts

Part of Azure DevOps.
- Integrates naturally with Azure Pipelines and Azure Boards/Azure DevOps organizations.
- Supports feeds for package ecosystems such as NuGet, npm, Maven, -Python, plus Universal Packages.
- Useful when an organization already uses Azure DevOps heavily or wants centrally managed feeds there.

The design question is usually not:

“Which one is objectively better?”

It is:

“Which package service best fits the existing ecosystem, permissions, pipelines, and consumers?”

A useful first mental model is:

GitHub-centric org
repos + PRs + Actions
        ↓
GitHub Packages often fits naturally

Azure DevOps-centric org
Repos/Boards/Pipelines
        ↓
Azure Artifacts often fits naturally

A major trap is confusing packages with pipeline artifacts.

Suppose a build produces a reusable library:

OrderValidation v1.4.2

Five different applications need to reference that library by version.

That's a package-management problem:

OrderValidation
├── 1.4.0
├── 1.4.1
└── 1.4.2
       ↓
Applications declare dependency

Compare that with:

Pipeline run #742
      ↓
application.zip
      ↓
Deploy this exact build

That's more naturally a pipeline/build artifact.

A package is generally intended to be versioned, discoverable, and consumed as a dependency. A pipeline artifact is commonly an output passed between stages or retained from a particular build.

Choose the package registry based on the package-consumption ecosystem and governance needs, not only on where the Git repo lives.

A proper package feed gives you something more like:

Package registry
OrderValidation
├── 1.0.0
├── 1.1.0
└── 1.2.0

Consumer
→ requests OrderValidation 1.2.0
→ package manager resolves/downloads it

So another exam trap is:

Git repository ≠ package registry.

Your first point is partly right, but let's sharpen it: the feed isn't automatically updated merely because it exists. A developer or, preferably, a pipeline publishes a new package version.

The bigger advantages are:

Git repository
→ source history

Build/publish pipeline
→ produces versioned package

Azure Artifacts
→ az400-utils 0.1.0
→ az400-utils 0.2.0
→ az400-utils 1.0.0

Consumer
→ requests a defined package/version

Consumers don't need your source repository structure or build process. They consume a standard Python package using normal package-management tooling. The feed also gives you centralized versioning, access control, and dependency management.

A few exam traps to lock in:

Git repo vs package feed: source lives in Git; reusable versioned dependencies belong in a package registry.
Package vs pipeline artifact: a package is meant to be consumed/versioned by other projects; a pipeline artifact is usually an output of a specific build/run.
GitHub Packages vs Azure Artifacts: choose based on ecosystem, governance, existing feeds, CI/CD tooling, and consumers—not just repo location.
Versioning matters: az400-utils==0.1.0 gives the consumer a reproducible dependency instead of “whatever is latest.”
Publishing is explicit: the feed does not magically update; a developer or pipeline must publish the new version.


### Design and implement package feeds and views for local and upstream packages
A useful mental model is:

Consumer
   ↓
Azure Artifacts feed
   ├── locally published packages
   └── upstream packages
          ↓
   NuGet.org / PyPI / npmjs / another Azure Artifacts feed

A feed is the package repository itself. It can contain packages you publish directly and packages cached/saved from upstream sources. Azure Artifacts supports project-scoped and organization-scoped feeds, with permissions controlling who can read, publish, or administer them.

An upstream source lets your feed act as a single entry point for dependencies that actually originate somewhere else. For example:

Application
   ↓ pip install
az400-team-feed
   ├── az400-utils 0.1.0      ← internal/local
   └── requests 2.x           ← originally from PyPI

The consumer only needs to connect to the Azure Artifacts feed. When an authorized user first installs an upstream package, Azure Artifacts saves a copy into the feed. That cached copy can remain available even if the original upstream source is temporarily unavailable.

That gives upstream sources two major benefits:

One dependency endpoint → consumers don't need separate configuration for every registry.

Dependency caching/availability → once an upstream version is saved, your feed retains a copy.

Now the other important concept: views.

Every feed has these default views:

@Local
@Prerelease
@Release

@Local contains packages published directly into the feed plus packages saved from upstream sources. @Prerelease and @Release are suggested views you can use to expose selected package versions to different consumers. Views are effectively read-only subsets of the feed; packages are published to the base feed and then promoted into a view.

Think:

Feed
az400-team-feed
│
├── @Local
│   ├── utils 1.0.0
│   ├── utils 1.1.0-beta
│   └── utils 1.1.0
│
├── @Prerelease
│   └── utils 1.1.0-beta
│
└── @Release
    ├── utils 1.0.0
    └── utils 1.1.0

That lets you separate:

“Package exists”

from:

“Package has been validated and is approved for this audience.”

A subtle exam trap: once an upstream package version has been saved into the feed, it becomes part of the feed’s available package set. That means your organization can continue consuming that saved version even if the public upstream is temporarily unavailable.

A view like @Release is not a completely separate package repository. It is a filtered/promoted view over the same feed.

So:

Base feed
├── package A 1.0
├── package A 1.1-beta
└── package A 1.1

@Release
├── package A 1.0
└── package A 1.1

The package isn’t rebuilt when promoted. You’re changing its visibility/lifecycle status for consumers of that view.

Views are filtered/promoted views over the same feed, not separate physical package stores.
Keep these distinctions clear:

Requirement	Appropriate concept
Store internal packages	Feed
Consume internal + public packages through one endpoint	Upstream source
Cache a public dependency internally	Upstream source
Mark a validated package as production-ready	Promote to @Release
Expose preproduction versions separately	@Prerelease
Package merely exists in the feed	@Local

The particularly important trap is:

Promotion does not rebuild, copy, or move the package.

The same az400-utils 0.1.0 you tested is promoted:

Build once
    ↓
az400-utils 0.1.0
    ↓
Test exact package
    ↓
Promote exact package
    ↓
@Release

That's much safer than rebuilding after validation.

Upstream packages:

pip
 ↓
Azure Artifacts
 ↓
PyPI upstream
 ↓
pendulum retrieved
 ↓
saved in your feed

Local package + views:

az400-utils 0.1.0
 ↓
published to feed
 ↓
@Local
 ↓
validated
 ↓
promoted
 ↓
@Release

Same package remains in @Local

Source validation and package validation are related, but not identical.

### Design and implement a dependency versioning strategy for code assets and packages, including semantic versioning (SemVer) and date-based (CalVer)
SemVer

Semantic Versioning uses:

MAJOR.MINOR.PATCH

For example:

2.4.1
│ │ │
│ │ └─ PATCH → backward-compatible bug fix
│ └─── MINOR → backward-compatible functionality
└───── MAJOR → breaking/incompatible change

Examples:

1.2.3 → 1.2.4
bug fix

1.2.3 → 1.3.0
new backward-compatible feature

1.2.3 → 2.0.0
breaking change

Pre-release versions can also be expressed, for example:

2.0.0-alpha.1
2.0.0-beta.2
2.0.0-rc.1

The big advantage of SemVer is that the version number communicates compatibility intent to consumers.

CalVer

Calendar Versioning derives version numbers from the date.

Examples might look like:

2026.8
2026.08.23
26.8.1

The exact pattern is an organizational choice.

CalVer is useful when the key information is when the release happened, rather than API compatibility semantics.

For example, a frequently updated platform or tool might use:

2026.08
2026.09
2026.10

That immediately communicates release chronology.

The key decision

Think:

Need version number to communicate compatibility?
→ SemVer

Need version number to communicate release date/cadence?
→ CalVer

Neither is universally better.

Now connect this to package dependencies. Suppose application A depends on:

az400-utils 1.4.2

If az400-utils follows SemVer, version 1.5.0 should normally add functionality without intentionally breaking existing 1.x consumers, while 2.0.0 signals that consumers may need to change.

Suppose you're developing that breaking 4.0.0 release, but it isn't production-ready yet.

Instead of publishing 4.0.0 immediately, SemVer supports prerelease identifiers:

4.0.0-alpha.1
      ↓
4.0.0-beta.1
      ↓
4.0.0-rc.1
      ↓
4.0.0

Conceptually:

alpha → early/incomplete
beta → more mature but still being validated
rc → release candidate
no suffix → stable release

Package version and Azure Artifacts view are independent. Promotion changes the view, not the package's version.


- SemVer communicates compatibility intent with MAJOR.MINOR.PATCH.
- CalVer communicates release chronology using dates.
- Exact version pinning improves reproducibility.
- Prerelease suffixes such as -beta or -rc are part of the version itself.

- Breaking change → increment MAJOR.
- New backward-compatible feature → increment MINOR.
-Backward-compatible fix → increment PATCH.
- 4.0.0-rc.1 and 4.0.0 are different package versions.
- Promoting a package to @Release does not rename its version.
- SemVer does not guarantee a newer compatible version is bug-free.


### Design and implement a versioning strategy for pipeline artifacts
The key distinction from the package-versioning point is:

Package versioning identifies a reusable dependency.
Pipeline artifact versioning identifies the output of a particular pipeline run/build.

For example:

Source commit abc123
        ↓
Pipeline run 842
        ↓
Artifact
eshop-web-842.zip
        ↓
Deployment
        ↓
Production


The important property is traceability:

“Which source revision produced the artifact currently running in production?”

A weak strategy would be:

app.zip

for every build.

If every pipeline overwrites the same name, it becomes difficult to distinguish:

app.zip ← build 841?
app.zip ← build 842?
app.zip ← build 843?

A stronger strategy gives each build output a unique identity, such as:

eshop-web-842.zip
eshop-web-843.zip
eshop-web-844.zip

or something more descriptive:

eshop-web-1.4.2+842.zip

where you combine a product/release version with a unique build/run identifier.

Common inputs to an artifact version

A pipeline artifact identity can be derived from things such as:

Pipeline run/build ID
Build number
Git tag
Commit SHA
Branch
Semantic version
Calendar/date

For example:

1.8.0-build.842
2026.08.23.842
commit-a84c7d2

You don't necessarily put every one of these into the filename. The pipeline system itself can retain metadata mapping the artifact back to the run, commit, branch, and timestamps.

Build once, identify the artifact, validate it, and promote/deploy that exact artifact.

Version and identify pipeline artifacts so you can trace them to the exact build and source revision, then promote the same validated artifact through environments.

- Application/package version communicates the software version.
- Build ID/run ID uniquely identifies the pipeline execution.
- Build number can provide a human-readable pipeline version.
- Commit SHA identifies the exact source revision.
- Combining application version + build identity gives strong traceability.
- Artifacts should be treated as immutable outputs.
- Build once, promote the same artifact through test/staging/production.
- Don't rely on latest when reproducibility and traceability matter.
- Don't rebuild for each environment when the requirement is to deploy the tested artifact.