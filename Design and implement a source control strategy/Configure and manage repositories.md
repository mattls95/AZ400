# Design and implement a source control strategy

## Configure and manage repositories

### Design and implement a strategy for managing large files, including Git Large File Storage (LFS) and git-fat
The core problem is that Git is optimized for source-like content and retains history. Large binary files—videos, PSDs, archives, datasets, binaries—can make repositories grow very quickly because historical versions remain part of the repository. git-fat describes exactly this problem: large binary history can make repository size impractical.

Git LFS solves it by keeping a small pointer file in Git while storing the real large object separately. GitHub then uses that pointer to retrieve the actual file when needed.
Conceptually:

Normal Git

repo
├── source.py
├── huge-video-v1.mp4
├── huge-video-v2.mp4
└── huge-video-v3.mp4

Git history becomes very large

versus:

Git + LFS

Git repository
├── source.py
└── video.mp4 pointer
          │
          ▼
     LFS storage
     actual video.mp4
A Git LFS pointer is tiny metadata containing things such as the object's identifier and size rather than the actual binary content.

Git LFS workflow

After installing Git LFS, you initialize it:

git lfs install

Then decide which file patterns should use LFS:

git lfs track "*.psd"

Git LFS records that tracking rule in .gitattributes, so that file should also be committed. GitHub's documentation recommends configuring file types through git lfs track.

You then work approximately as normal:

git add
git commit
git push

but LFS transparently handles the large object separately.
One important collaboration point: someone cloning the repo without Git LFS installed may only get the pointer files, not the actual large objects.

What about git-fat?

git-fat follows a similar general philosophy: keep large binary content outside normal Git history and store references in the repository.

Its model typically uses .gitattributes plus an external object store reachable through rsync/SSH.

So roughly:

Git LFS
Git pointer
   ↓
LFS server/storage

git-fat
Git placeholder/reference
   ↓
external fat-file store
often rsync/SSH

For AZ-400, I'd treat Git LFS as the more important technology to understand operationally, while recognizing what git-fat is and the problem it solves.

If a large file was accidentally committed into normal Git history, you may need history rewriting/migration to actually remove or migrate the historical object. Git LFS provides migration functionality for this type of situation.

So distinguish:

Untracking/removing changes what Git tracks going forward.
History rewriting/migration changes what previous commits contain.

This fetches only a limited amount of commit history, so it can reduce what a client downloads. But that's a clone optimization, not a fix for the underlying repository history.

Think of the distinction:

git lfs track
→ Prevent/manage future large-file storage

git clone --depth N
→ Download less history to this clone

History migration/rewrite
→ Change/remove large files already stored
  in historical commits

There's another option you'll encounter in CI: a pipeline checkout may use a shallow fetch because the build doesn't need years of history.

Keep large binary objects outside ordinary Git object history while retaining references/versioning through Git.

Git LFS is integrated into major Git hosting platforms and uses LFS pointer objects/protocol. git-fat is an older external approach where large objects can live in separate storage, commonly accessed using mechanisms such as rsync.

Large source asset that needs versioning
→ Git LFS

Generated build artifact
→ Artifact/package storage

Historical large blobs already in Git
→ History migration/rewrite

CI only needs latest revision
→ Shallow clone/fetch with depth

### Design a strategy for scaling and optimizing a Git repository, including Scalar and cross-repository sharing
What do you do when the repository itself is so large that normal Git operations become slow, even if individual files are not the main issue?

Scalar

Scalar is aimed at very large Git repositories, especially monorepos. It configures Git features that reduce how much data and work the client has to do up front.

Conceptually:

Normal clone
→ fetch lots of objects/history
→ index huge working tree
→ expensive maintenance

Scalar-managed repo
→ partial/sparse behavior
→ background maintenance
→ optimized fetch/index operations

So Scalar is not “another source-control system.” It is an optimization layer around Git for very large repositories.

Cross-repository sharing

This is about avoiding unnecessary duplication when multiple related repositories share Git objects/history.

Instead of:

Repo A clone
→ stores object X

Repo B clone
→ stores the same object X again

Repo C clone
→ stores the same object X again

you can use mechanisms where repositories can reuse or lazily obtain objects rather than independently storing/fetching everything.

If several huge repositories share a lot of history/data, design the repository topology so clients don't repeatedly download/store identical objects unnecessarily.

One more optimization: sparse checkout
Suppose your monorepo contains:

/frontend
/backend
/mobile
/infrastructure
/docs

A mobile developer only needs /mobile and perhaps /docs.

Instead of populating the entire repository into their working directory, sparse checkout can restrict which paths are checked out.

So distinguish:

Partial clone → reduce which Git objects are initially downloaded.
Sparse checkout → reduce which files/directories appear in the working tree.

These techniques complement Scalar for very large repositories.

The distinction is:

- Sparse checkout reduces what appears in the working tree.
- Partial clone avoids downloading many Git objects until they're actually needed.
- Shallow clone / --depth limits how much commit history you fetch.

AZ-400 takeaway: scaling and optimizing Git

Keep this decision map:

Problem	Technique
Large individual binary files	Git LFS
Huge repository overall	Scalar
Only certain directories needed	Sparse checkout
Don't need all Git objects immediately	Partial clone
Don't need old commit history	Shallow clone / fetch depth
Related repos duplicate Git objects	Cross-repository sharing
Generated build artifacts	Artifact/package storage, not Git

And remember that Scalar isn't a replacement for Git. It's designed to configure and manage Git repositories for very large-scale scenarios.

One useful exam habit is to identify what dimension is actually large before choosing an optimization: file size, working-tree size, object transfer, history depth, or duplicated objects.

### Configure permissions in the source control repository
The core principle is least privilege:

Give users and service identities only the permissions they need to perform their role.

Typical repository permissions include things like:

Read/clone
Create branches
Contribute/push
Create/manage pull requests
Approve/review
Manage repository settings
Bypass branch policies
Delete branches/tags
Force push

A useful mental model:

Developer
→ read
→ create branches
→ push to own branches
→ create PRs
→ no direct push to main

Reviewer
→ developer permissions
→ review/approve PRs

Release manager
→ merge protected branches
→ maybe controlled bypass

Repository admin
→ manage settings/policies
→ very limited membership

Explicit deny vs allow

Azure DevOps permissions introduce an important concept: permissions can be inherited through groups, and explicit Deny generally takes precedence over Allow.

Service identities

Permissions aren't just for humans.

Your CI/CD pipeline might need to:

Pipeline identity
├── Read source       ✅
├── Create tag        maybe
├── Update repo       maybe
└── Admin repo        ❌

Again, grant only what the automation actually needs.

For “Configure permissions in the source control repository”, remember:

- Follow least privilege.
- Prefer groups/teams over assigning permissions individually.
- Scope permissions appropriately: organization/project → repository → branch, depending on the platform.
- Separate normal contributor permissions from administrative permissions.
- Protect important branches even when developers have repository write access.
- Give service identities only the permissions their automation requires.
- Understand permission inheritance and that an explicit Deny can override an Allow in Azure DevOps.
- Keep bypass/admin permissions tightly restricted.
- Periodically review access when roles or employment/contract status changes.

The recurring exam question is essentially:

Who needs access, to what resource, and what is the minimum permission they need to do their job?

### Configure tags to organize the source control repository
Git tags are important because they give a meaningful name to a specific point in repository history.

commit A ── commit B ── commit C ── commit D
                         ↑
                       v1.0.0

Instead of saying:

“Production version is commit a84c7...”

you can say:

“Production version is v1.0.0.”

A branch reference moves as new commits are added. A tag normally remains attached to the particular commit it identifies.

This makes tags useful for releases, version identification, deployment traceability, and marking important repository milestones.

Lightweight vs annotated tags

Git supports two types you should recognize.

A lightweight tag is essentially just a name pointing to a commit:

git tag v1.0.0

An annotated tag contains additional metadata such as tagger, date, and message:

git tag -a v1.0.0 -m "Release version 1.0.0"

Annotated tags are generally more appropriate for formal releases because they carry that additional metadata.

You then push a tag to the remote, for example:

git push origin v1.0.0

Tags also connect directly to what you practiced earlier with release notes:

v1.0.0
   │
   │ changes since previous release
   ▼
v1.1.0
   ↓
Release notes

Tag naming strategy

Tags become much more useful when they're consistent. A common approach is Semantic Versioning:

v2.4.1
 │ │ │
 │ │ └─ PATCH
 │ └─── MINOR
 └───── MAJOR

Broadly:

MAJOR → incompatible/breaking changes
MINOR → backward-compatible functionality
PATCH → backward-compatible fixes

For “Configure tags to organize the source control repository”, remember:

- A tag identifies a specific commit.
- Unlike a branch, a tag normally doesn't move forward with development.
- Use tags for releases, versions, and important milestones.
- Annotated tags contain metadata/message and are useful for formal releases.
- Push local tags when they need to be available remotely.
- Use a consistent naming/versioning convention such as Semantic Versioning.
- Protect important release tags from unauthorized deletion/modification.
- Tags provide release → exact source revision traceability.

### Recover specific data by using Git commands
Typical recovery scenarios include:

- Recover a deleted file.
- Restore a file from an earlier commit.
- Undo an accidental commit.
- Recover a commit after a reset/rebase.
- Restore a branch that was deleted.
- Recover content from another branch or commit.

A few commands are especially important:

git restore
git checkout   (older syntax, still common)
git revert
git reset
git reflog
git show

Scenario 1: deleted file, not committed yet

Suppose you accidentally delete:

config.yaml

but you have not committed the deletion.

What would you want Git to do: rewrite history, or simply restore config.yaml from the current committed version?
You could use:

git restore config.yaml

Conceptually:

HEAD
config.yaml exists
      ↓
Working tree
config.yaml accidentally deleted ❌
      ↓
git restore config.yaml
      ↓
Working tree restored ✅

Scenario 2: recover an older version

Now suppose config.yaml still exists, but someone changed it several commits ago.

You discover that the version from commit:

abc123

contained the configuration you need.

You don't want to move the entire repository back to abc123. You only want that version of config.yaml.

What do you think we should restore: the whole commit/branch, or just that specific file from abc123?

You can restore that specific file from the older commit:

git restore --source=abc123 config.yaml

Conceptually:

abc123
├── config.yaml  ← version we want
├── app.py
└── README.md

         ↓ restore only this file

Current branch
├── config.yaml  ← recovered from abc123
├── app.py        ← remains current
└── README.md     ← remains current

The restored file becomes a change in your current working tree. You can inspect it and then commit it normally if it's correct.

Older Git workflows may use:

git checkout abc123 -- config.yaml

Scenario 3: committed mistake on a shared branch

Now suppose a bad commit has already been pushed to main, and other developers may already have pulled it.

Would you prefer to rewrite main history with git reset, or create a new commit that safely reverses the bad commit?
On a shared branch, preserving history is normally safer. That's what:

git revert <commit>

is designed for.

Suppose:

A ─ B ─ C ─ D
        ↑
     bad commit

Running:

git revert C

doesn't delete C. Git creates a new commit containing the inverse changes:

A ─ B ─ C ─ D ─ E
        ↑           ↑
       bad        revert C

That preserves traceability and avoids rewriting history other developers may already depend on.

So remember:

restore → recover file content
revert → safely undo a committed change with another commit

Scenario 4: the scary one

You're working locally and run:

git reset --hard HEAD~1

Then you realize the commit you just removed contained two hours of work.

git log no longer shows it.

Do you think Git has necessarily destroyed that commit immediately, or might Git still have a record of where HEAD previously pointed?

Git may still have a record of where HEAD pointed before the reset. That’s what git reflog is for.

You could run:

git reflog

and look for the commit before the reset. You might see something like:

abc123 HEAD@{1}: commit: add webhook receiver
def456 HEAD@{0}: reset: moving to HEAD~1

If abc123 is the lost commit, you can recover it by creating a branch from it:

git switch -c recover-work abc123

or move your current branch back to it if that’s really what you want.

The important distinction is:

git log shows normal reachable history.
git reflog shows where local refs like HEAD pointed recently.

reset and rebase can move or rewrite your local branch history, so the commit may disappear from normal git log output even though Git still remembers where HEAD or the branch pointed recently.

That makes git reflog a recovery tool for cases like:

reset
rebase
accidental branch move
deleted local branch

You can inspect the reflog, find the old commit hash, then recover it by creating a branch or resetting back to it.

AZ-400 recovery map

At this point, you can distinguish the major cases:

Situation	Tool
Accidentally changed/deleted an uncommitted file	git restore
Need one file from an older commit	git restore --source=<commit>
Undo a commit safely on shared history	git revert
Move local branch/HEAD to another commit	git reset
Find commits lost after reset/rebase/branch deletion	git reflog
Inspect content from a commit	git show

The important exam distinction is shared vs local history. On shared/protected history, prefer operations such as revert that preserve history. For local mistakes, tools such as reset and reflog give you more freedom.

### Remove specific data from source control
Removing something from the current repository state is not the same as removing it from Git history.

We touched this when discussing large files.

Suppose someone accidentally commits:

config/secrets.json

and it contains a credential.

They then run:

git rm config/secrets.json
git commit -m "Remove secrets"

The current version no longer contains the file, but:

A ─ B ─ C
    ↑   ↑
 secret removed
 still
 exists
 here

Anyone with access to commit B may still be able to retrieve the secret.

So there are two different requirements:

"Stop tracking this file going forward"
→ remove/untrack + .gitignore

"Remove this data from historical commits"
→ rewrite repository history

For history cleanup, modern Git workflows commonly use tools designed to rewrite/filter history, such as git filter-repo; you may also encounter older mechanisms such as git filter-branch or tools like BFG Repo-Cleaner.

There is an additional security step that's especially important for credentials:

Removing a secret from Git history does not make the exposed credential safe again.

If an API key was committed, assume it may have been copied. The credential should be revoked/rotated, independently of cleaning Git history.

Scenario 1
A developer accidentally adds a local build directory:

/build/

to Git. It has been committed once, but there's no sensitive data and you don't care about purging that old commit. You simply want Git to stop tracking /build/ from now on while keeping the local directory.

Would you delete the directory entirely, or untrack it and add it to .gitignore?
Since we don't care that /build/ exists in old history, there's no need for a disruptive history rewrite.

You'd add it to .gitignore:

/build/

and remove it from Git's index while keeping the local files:

git rm -r --cached build/

Then commit that change.

Local /build/     → remains ✅
Git tracking      → removed ✅
.gitignore        → prevents accidental re-add ✅
Old Git history   → unchanged

Scenario 2
A developer accidentally committed an Azure client secret to the repository and pushed it to the remote. What's the first security action you'd take: rewrite Git history, or revoke/rotate the exposed credential?
History cleanup matters, but the exposed secret may already have been copied. Removing it from Git does not invalidate it.

The priority is:

1. Revoke/rotate secret
2. Remove secret from current source
3. Rewrite history if required
4. Force-update affected remote history
5. Have collaborators re-clone/reconcile
6. Add prevention controls

Those prevention controls could include .gitignore, secret scanning, pre-commit checks, and CI security gates.

One nuance: rewriting shared Git history is disruptive because commit hashes change, so it should be coordinated carefully.
Current state cleanup ≠ history cleanup ≠ credential remediation
Removing from the working tree is not the same as removing from repository history. And if history is rewritten on a shared repo, collaborators may need to re-clone or carefully realign because rewritten commits get new IDs.