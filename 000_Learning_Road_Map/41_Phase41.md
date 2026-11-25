# Phase 41 – Advanced Git, GitHub, Version Control and Team Collaboration

**Level:** Advanced  
**Domain:** Software Engineering, Version Control and DevOps  
**Tools:** Git, GitHub, GitHub Actions, VS Code / Terminal  
**Practical Stack:** Next.js, React, Node.js, Express, MongoDB, MySQL

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand how Git works internally.
2. Manage repositories, commits and branches.
3. Work confidently with merge conflicts.
4. Understand the difference between merge and rebase.
5. Recover from incorrect commits and other Git mistakes.
6. Manage local and remote repositories.
7. Collaborate through GitHub Pull Requests.
8. Perform code reviews and manage issues.
9. Implement professional Git branching strategies.
10. Automate code checks using GitHub Actions.
11. Protect repositories and maintain secure development workflows.
12. Troubleshoot common Git and GitHub problems.

---

# 2. Version Control System Fundamentals

## 2.1 What Is Version Control?

A **Version Control System (VCS)** is a tool that tracks changes made to files over time.

It allows developers to:

- Track modifications.
- Restore previous versions.
- Compare changes.
- Collaborate with other developers.
- Maintain multiple versions of an application.
- Identify who made a change and when.

For example, suppose you are developing an e-commerce application.

Initially, you have:

```text
Ecommerce App v1
```

You add authentication:

```text
Ecommerce App v2
```

You add payment integration:

```text
Ecommerce App v3
```

You introduce a bug while implementing a new feature.

Without version control, recovering the previous working version could be difficult.

With Git, you can inspect the history and restore or revert the problematic changes.

## 2.2 Types of Version Control Systems

There are three major categories.

| Type | Description | Example |
|---|---|---|
| Local VCS | Tracks versions on a single computer | RCS |
| Centralized VCS | Uses a central server as the main repository | SVN |
| Distributed VCS | Every developer can have a complete repository history | Git |

### Centralized Version Control

```text
             Central Server
                  |
       -----------------------
       |          |          |
   Developer A Developer B Developer C
```

Developers depend on the central server for repository operations.

### Distributed Version Control

```text
          Remote Repository
              (GitHub)
                  |
       -----------------------
       |          |          |
   Developer A Developer B Developer C
   Local Git   Local Git   Local Git
   Repository  Repository  Repository
```

Each developer has a local repository containing the project's history.

**Git is a distributed version control system.**

This means you can commit changes locally without requiring an internet connection. You need a network connection when communicating with a remote repository such as GitHub.

---

# 3. Git Architecture

## 3.1 What Is Git?

Git is a distributed version control system used to track changes in source code and other files.

It was originally created by Linus Torvalds in 2005.

Git tracks changes through commits and stores repository information in the `.git` directory.

## 3.2 Git vs GitHub

These are two different things.

| Git | GitHub |
|---|---|
| Version control system | Cloud-based code hosting platform |
| Runs locally | Provides remote repository hosting |
| Tracks file changes | Hosts Git repositories |
| Supports branching and merging | Provides Pull Requests |
| Works without internet | Requires internet for online collaboration |
| Open-source software | Commercial platform owned by GitHub |

Other Git hosting platforms include GitLab and Bitbucket.

## 3.3 Git's Internal Architecture

Git has four important areas:

1. Working Directory
2. Staging Area
3. Local Repository
4. Remote Repository

```text
       Working Directory
              |
          git add
              |
              v
         Staging Area
              |
         git commit
              |
              v
        Local Repository
              |
          git push
              |
              v
       Remote Repository
            GitHub
```

### 1. Working Directory

This is where you create, modify and delete files.

Example:

```text
my-project/
│
├── src/
│   ├── app.js
│   └── server.js
│
└── package.json
```

When you modify `app.js`, Git detects that the file has changed.

### 2. Staging Area

The staging area contains changes selected for the next commit.

Command:

```bash
git add app.js
```

This tells Git that you want to include the current version of `app.js` in your next commit.

### 3. Local Repository

The local repository stores committed snapshots and their history.

Command:

```bash
git commit -m "Add authentication"
```

### 4. Remote Repository

A remote repository is another copy of the repository hosted somewhere else.

For example:

```text
https://github.com/username/ecommerce-app.git
```

You can upload local commits using:

```bash
git push
```

---

# 4. Git Internals: Objects and References

Understanding Git's internal object model helps you troubleshoot advanced problems.

Git stores four primary object types.

| Object | Purpose |
|---|---|
| Blob | Stores file content |
| Tree | Stores directory structure and references to blobs or other trees |
| Commit | Stores a snapshot reference, parent commit information and metadata |
| Tag | Provides a named reference to a specific object, commonly a commit |

### 4.1 Blob

A blob stores file content.

For example:

```javascript
console.log("Hello Git");
```

Git stores the content as a blob object.

A blob does not independently store the filename.

### 4.2 Tree

A tree represents a directory structure.

For example:

```text
project/
│
├── app.js
├── package.json
└── src/
    └── server.js
```

A tree object stores references to the files and subdirectories.

### 4.3 Commit

A commit represents a snapshot of the project.

It contains information such as:

- Reference to the root tree.
- Parent commit or commits.
- Author information.
- Committer information.
- Commit message.
- Timestamp.

### 4.4 Tag

A tag gives a meaningful name to a particular point in history.

For example:

```text
v1.0.0
v1.1.0
v2.0.0
```

### 4.5 How Git Stores These Objects

Git uses content-addressed storage.

An object's identifier is calculated from its content and object metadata using a hash algorithm.

Modern Git repositories commonly use SHA-1 object IDs, although Git also supports SHA-256 repositories.

Inspect Git objects:

```bash
git cat-file -t <object-id>
```

Show object content:

```bash
git cat-file -p <object-id>
```

List repository objects:

```bash
git count-objects -v
```

**Important:** Git does not simply store a complete independent copy of every file for every commit. It uses objects, references, compression and packing to store repository history efficiently.

---

# 5. Installing and Configuring Git

## 5.1 Check Git Installation

```bash
git --version
```

Example:

```text
git version 2.x.x
```

## 5.2 Configure Username

```bash
git config --global user.name "Your Name"
```

## 5.3 Configure Email

```bash
git config --global user.email "your-email@example.com"
```

The email is recorded in commit metadata.

It does not have to be the same as your GitHub login email, but using an email associated with your GitHub account helps GitHub associate commits with your profile.

## 5.4 View Configuration

```bash
git config --list
```

View a specific setting:

```bash
git config user.name
```

## 5.5 Configuration Levels

Git supports three main configuration scopes.

| Scope | Command | Applies To |
|---|---|---|
| System | `--system` | All users on the system |
| Global | `--global` | Current operating system user |
| Local | `--local` | Current repository |

Example:

```bash
git config --global core.editor "code --wait"
```

Repository-specific configuration:

```bash
git config --local user.email "work@example.com"
```

**Priority:** Local configuration overrides global configuration, which overrides system configuration.

Official reference: [Git Configuration Documentation](https://git-scm.com/docs/git-config?utm_source=chatgpt.com)

---

# 6. Git Repository Fundamentals

## 6.1 Initialize a Repository

Create a project:

```bash
mkdir ecommerce-app
cd ecommerce-app
```

Initialize Git:

```bash
git init
```

Git creates a hidden `.git` directory.

```text
ecommerce-app/
│
├── .git/
│
└── ...
```

The `.git` directory contains repository metadata, objects, references and configuration.

Do not manually modify its internal files unless you know exactly what you are doing.

## 6.2 Clone an Existing Repository

```bash
git clone https://github.com/username/project.git
```

Clone into a custom directory:

```bash
git clone https://github.com/username/project.git my-project
```

Clone a specific branch:

```bash
git clone -b development https://github.com/username/project.git
```

## 6.3 Check Repository Status

```bash
git status
```

This displays:

- Current branch.
- Modified files.
- Untracked files.
- Staged changes.
- Whether the branch is ahead of or behind its upstream branch.

## 6.4 Check the Repository Root

```bash
git rev-parse --show-toplevel
```

## 6.5 Check the Git Directory

```bash
git rev-parse --git-dir
```

---

# 7. Working Directory, Staging Area and Repository

This is one of the most important Git concepts.

Suppose you create:

```javascript
// app.js

console.log("Hello World");
```

Check status:

```bash
git status
```

Git reports `app.js` as untracked.

Add the file:

```bash
git add app.js
```

Now it is staged.

Commit it:

```bash
git commit -m "Add initial application file"
```

Now the change is recorded in the local repository.

## 7.1 Git File States

| State | Meaning |
|---|---|
| Untracked | Git is not currently tracking the file |
| Unmodified | File matches the version in the current commit |
| Modified | Tracked file has been changed |
| Staged | Changes are selected for the next commit |

A file can move between these states as you work.

## 7.2 Add Specific Files

```bash
git add app.js
```

Add a directory:

```bash
git add src/
```

Add all changes in the current directory:

```bash
git add .
```

Stage all tracked changes and untracked files:

```bash
git add -A
```

### Difference Between `git add .` and `git add -A`

`git add .` stages changes under the current directory.

`git add -A` stages additions, modifications and deletions across the working tree.

For most normal project workflows, `git add -A` is useful when you intend to stage all changes.

## 7.3 View Staged and Unstaged Changes

View unstaged changes:

```bash
git diff
```

View staged changes:

```bash
git diff --staged
```

Compare working directory with the latest commit:

```bash
git diff HEAD
```

**Best practice:** Review staged changes before committing.

```bash
git add -A
git diff --staged
git commit -m "Implement product filtering"
```

---

# 8. Git Commits and Commit Messages

## 8.1 What Is a Commit?

A commit is a recorded snapshot of your project at a particular point in time.

Example:

```bash
git commit -m "Add user authentication"
```

A commit contains:

- Snapshot reference.
- Author.
- Committer.
- Timestamp.
- Commit message.
- Parent commit reference or references.

## 8.2 View Commit History

```bash
git log
```

Compact history:

```bash
git log --oneline
```

Show the last five commits:

```bash
git log -5
```

Show branch graph:

```bash
git log --oneline --graph --all
```

Show files changed in each commit:

```bash
git log --stat
```

Show the changes introduced by a commit:

```bash
git show <commit-id>
```

## 8.3 Good Commit Messages

Poor examples:

```text
update
changes
fix
final
new code
```

Better examples:

```text
Add JWT authentication
Fix product filtering bug
Update payment validation
Refactor user service
Add Redis caching for products
```

A good commit message explains what the commit changes.

## 8.4 Conventional Commits

Conventional Commits is a widely used commit message convention.

Format:

```text
<type>[optional scope]: <description>
```

Examples:

```bash
git commit -m "feat(auth): add JWT authentication"
```

```bash
git commit -m "fix(cart): resolve quantity update issue"
```

```bash
git commit -m "docs(readme): update installation instructions"
```

Common types:

| Type | Meaning |
|---|---|
| feat | New feature |
| fix | Bug fix |
| docs | Documentation |
| style | Formatting-only changes |
| refactor | Code restructuring |
| test | Tests |
| perf | Performance improvement |
| build | Build system or dependency changes |
| ci | CI/CD changes |
| chore | Maintenance tasks |

Official reference: [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/?utm_source=chatgpt.com)

---

# 9. Branching and Merging

## 9.1 What Is a Branch?

A branch is a movable reference to a commit.

Branches allow developers to work on different features independently without immediately changing the main development line.

Example:

```text
main
  |
  A
  |
  B
```

You create a feature branch:

```text
main
  |
  A
  |
  B
  |
  C
```

```text
feature-login
       |
       D
       |
       E
```

Both branches can develop independently.

## 9.2 List Branches

Local branches:

```bash
git branch
```

All local and remote-tracking branches:

```bash
git branch -a
```

Remote branches only:

```bash
git branch -r
```

## 9.3 Create a Branch

```bash
git branch feature-login
```

This creates the branch but does not switch to it.

## 9.4 Switch Branch

```bash
git switch feature-login
```

Create and switch in one command:

```bash
git switch -c feature-login
```

Older alternative:

```bash
git checkout -b feature-login
```

For everyday branch operations, `git switch` is generally clearer.

## 9.5 Rename a Branch

Rename the current branch:

```bash
git branch -m new-branch-name
```

Rename another branch:

```bash
git branch -m old-name new-name
```

## 9.6 Delete a Branch

Delete a merged local branch:

```bash
git branch -d feature-login
```

Force-delete a local branch:

```bash
git branch -D feature-login
```

Use `-D` carefully because Git will not protect you from losing unmerged work.

## 9.7 Merge a Branch

Suppose you have:

```text
main
feature-login
```

Switch to the target branch:

```bash
git switch main
```

Merge the feature:

```bash
git merge feature-login
```

Git integrates the feature branch into `main`.

## 9.8 Types of Merge

### Fast-Forward Merge

A fast-forward merge happens when the target branch has not moved since the feature branch was created.

Before:

```text
A --- B --- C  (feature-login)
       |
      main
```

After:

```text
A --- B --- C  (main, feature-login)
```

No additional merge commit is required.

### Three-Way Merge

A three-way merge occurs when both branches have progressed independently.

Before:

```text
          C --- D  (feature-login)
         /
A --- B
         \
          E --- F  (main)
```

Git uses the common ancestor and both branch tips to integrate the histories.

A merge commit may be created:

```text
          C --- D
         /       \
A --- B           M  (main)
         \       /
          E --- F
```

The exact result depends on the histories and merge strategy.

---

# 10. Merge Conflicts

## 10.1 What Is a Merge Conflict?

A merge conflict occurs when Git cannot automatically determine how to combine changes.

Example:

Developer A changes:

```javascript
const price = 100;
```

Developer B changes:

```javascript
const price = 200;
```

Both modify the same line differently.

Git cannot safely decide which value is correct.

## 10.2 Conflict Example

Git may display:

```text
<<<<<<< HEAD
const price = 100;
=======
const price = 200;
>>>>>>> feature-price
```

Meaning:

- `<<<<<<< HEAD`: Beginning of the current branch's conflicting content.
- `=======`: Separator.
- `>>>>>>> feature-price`: End of the incoming branch's conflicting content.

## 10.3 Resolve the Conflict

Decide which code should remain.

For example:

```javascript
const price = 200;
```

Remove the conflict markers.

Then:

```bash
git add app.js
git commit
```

If the merge commit message editor opens, save and close it.

## 10.4 Abort a Merge

If you want to cancel an in-progress merge:

```bash
git merge --abort
```

## 10.5 Merge Conflict Best Practices

1. Understand both developers' changes.
2. Do not blindly select one side.
3. Preserve valid functionality from both sides.
4. Run tests after resolving conflicts.
5. Review the final diff.
6. Communicate with the other developer when the intended behavior is unclear.

---

# 11. Git Rebase vs Git Merge

Both commands integrate changes from one branch into another, but they handle commit history differently.

## 11.1 What Is Rebase?

Rebase moves or reapplies commits onto a different base commit.

Suppose:

```text
main:     A --- B --- C
                 \
feature:          D --- E
```

Meanwhile, `main` advances:

```text
main:     A --- B --- C --- F --- G
                 \
feature:          D --- E
```

You want to update your feature branch using the latest `main`.

Switch to your feature branch:

```bash
git switch feature
```

Run:

```bash
git rebase main
```

Conceptually, Git reapplies the feature commits on top of the latest `main`:

```text
main:     A --- B --- C --- F --- G
                                  \
feature:                           D' --- E'
```

The commits `D'` and `E'` have new commit identities because their parent history has changed.

## 11.2 Merge vs Rebase

| Feature | Merge | Rebase |
|---|---|---|
| Purpose | Integrate branch histories | Reapply commits onto a new base |
| Creates merge commit | Sometimes | No merge commit for ordinary rebase |
| History | Preserves branch structure | Produces a more linear history |
| Commit identities | Existing commits are preserved | Rebased commits receive new identities |
| Shared branch safety | Generally safe | Requires caution |
| Conflict handling | During merge | While replaying commits |

## 11.3 When Should You Use Merge?

Use merge when:

- You want to preserve branch history.
- You are integrating shared development branches.
- You want to record when branches were combined.
- Your team prefers merge commits.

## 11.4 When Should You Use Rebase?

Use rebase when:

- You want to update your local feature branch.
- You want a cleaner linear history.
- You are preparing your own feature branch for review.
- Your team follows a rebase-based workflow.

**Important rule:** Avoid rebasing commits that other developers may already have based their work on, unless your team has explicitly coordinated the history rewrite.

Official reference: [Git Rebase Documentation](https://git-scm.com/docs/git-rebase?utm_source=chatgpt.com)

---

# 12. Interactive Rebase

Interactive rebase allows you to edit, reorder, combine or remove commits.

Command:

```bash
git rebase -i HEAD~3
```

This opens an editor showing the last three commits.

Example:

```text
pick a1b2c3 Add login page
pick d4e5f6 Fix login validation
pick g7h8i9 Update login styles
```

You can change the commands.

| Command | Purpose |
|---|---|
| pick | Keep commit |
| reword | Change commit message |
| edit | Stop and modify commit |
| squash | Combine commit with previous commit |
| fixup | Combine commit and discard its message |
| drop | Remove commit |

### Example: Squash Commits

Before:

```text
Add login page
Fix login validation
Fix login button
Update login styles
```

You want one clean commit.

```text
Add login page
```

Run:

```bash
git rebase -i HEAD~4
```

Change the instructions:

```text
pick a1b2c3 Add login page
squash d4e5f6 Fix login validation
squash g7h8i9 Fix login button
squash j1k2l3 Update login styles
```

Save and close the editor.

Git will combine the commits and allow you to edit the resulting commit message.

### Abort Interactive Rebase

```bash
git rebase --abort
```

### Continue After Resolving Conflicts

```bash
git add .
git rebase --continue
```

**Important:** Interactive rebase rewrites history. Use it primarily on commits that have not been shared with other developers.

---

# 13. Cherry-Pick

## 13.1 What Is Cherry-Pick?

`git cherry-pick` applies the changes introduced by a specific commit onto your current branch.

Example:

```text
main:
A --- B --- C --- D

feature:
A --- B --- E --- F
```

You only need commit `E` in `main`.

Switch to main:

```bash
git switch main
```

Apply the commit:

```bash
git cherry-pick <commit-id>
```

Git creates a new commit on `main` containing the selected change.

## 13.2 Cherry-Pick Multiple Commits

```bash
git cherry-pick <commit-1> <commit-2>
```

## 13.3 Cherry-Pick a Range

```bash
git cherry-pick A..D
```

This applies commits after `A` through `D`, subject to the revision range's history.

## 13.4 Abort Cherry-Pick

```bash
git cherry-pick --abort
```

## 13.5 Continue After Conflict Resolution

```bash
git add .
git cherry-pick --continue
```

### Real-World Example

Suppose a developer has implemented:

- A payment bug fix.
- A new dashboard feature.
- A UI redesign.

The payment fix is urgently needed in production, but the other features are not ready.

Cherry-pick allows you to apply the payment fix without merging the entire feature branch.

---

# 14. Git Reset vs Git Revert vs Git Restore

These commands are frequently confused.

## 14.1 Git Reset

`git reset` moves the current branch reference to another commit and can also change the staging area and working directory.

Example:

```bash
git reset --soft HEAD~1
```

This moves the current branch back one commit but keeps the changes staged.

### Reset Modes

| Mode | Commit History | Staging Area | Working Directory |
|---|---|---|---|
| `--soft` | Moves branch backward | Keeps changes staged | Keeps changes |
| `--mixed` | Moves branch backward | Resets staging | Keeps changes |
| `--hard` | Moves branch backward | Resets staging | Resets tracked files |

Default:

```bash
git reset HEAD~1
```

This uses mixed mode.

### Soft Reset Example

You committed too early.

```bash
git reset --soft HEAD~1
```

The commit is removed from the current branch tip, but its changes remain staged.

### Mixed Reset Example

```bash
git reset --mixed HEAD~1
```

The commit is removed from the current branch tip, and its changes remain in the working directory but become unstaged.

### Hard Reset Example

```bash
git reset --hard HEAD~1
```

This resets the current branch, staging area and tracked working-tree files to the previous commit.

**Warning:** `--hard` can discard uncommitted changes. Do not use it casually.

## 14.2 Git Revert

`git revert` creates a new commit that reverses the changes introduced by an earlier commit.

Example:

```bash
git revert <commit-id>
```

Before:

```text
A --- B --- C --- D
```

You want to undo `C`.

After:

```text
A --- B --- C --- D --- E
```

Commit `E` reverses the changes introduced by `C`.

The original commit remains in history.

## 14.3 Git Restore

`git restore` restores file contents in the working directory or staging area.

Discard unstaged changes in a file:

```bash
git restore app.js
```

Unstage a file:

```bash
git restore --staged app.js
```

Restore a file from a specific commit:

```bash
git restore --source=<commit-id> app.js
```

## 14.4 Comparison

| Command | Main Purpose |
|---|---|
| `git reset` | Move branch history and optionally reset index/worktree |
| `git revert` | Create a new commit that reverses earlier changes |
| `git restore` | Restore file content in working directory or staging area |

### Which Should You Use?

| Situation | Recommended Command |
|---|---|
| Undo your latest local commit but keep changes staged | `git reset --soft HEAD~1` |
| Undo a local commit but keep changes unstaged | `git reset HEAD~1` |
| Undo a commit already shared with the team | `git revert <commit>` |
| Discard unstaged changes in a file | `git restore <file>` |
| Unstage a file | `git restore --staged <file>` |

Official reference: [Git Reset Documentation](https://git-scm.com/docs/git-reset?utm_source=chatgpt.com)

---

# 15. Git Reflog: Recovering Lost Commits

This is an important advanced Git topic.

## 15.1 What Is Reflog?

Reflog records updates to local references such as `HEAD` and branch tips.

It can help you recover commits that are no longer reachable through the current branch history.

View reflog:

```bash
git reflog
```

Example:

```text
a1b2c3 HEAD@{0}: reset: moving to HEAD~2
d4e5f6 HEAD@{1}: commit: Add payment gateway
g7h8i9 HEAD@{2}: commit: Add product page
```

Suppose you accidentally reset your branch and want to recover the previous commit.

You can inspect the reflog and identify the correct commit.

Create a recovery branch:

```bash
git branch recovery-branch d4e5f6
```

Or move your current branch back:

```bash
git reset --hard d4e5f6
```

The second command changes the current branch and working tree, so use it only after confirming the target commit.

**Important:** Reflog is local. It is not a remote backup, and unreachable objects may eventually be removed by Git's cleanup processes.

---

# 16. Git Stash

## 16.1 What Is Git Stash?

Git stash temporarily saves uncommitted changes so you can work on another branch without committing unfinished work.

### Example

You are working on:

```text
feature-dashboard
```

You have modified:

```text
Dashboard.jsx
Dashboard.css
```

Suddenly, you need to switch to a production bug fix.

Instead of committing unfinished changes, stash them.

```bash
git stash
```

Now your working directory is cleaned of the stashed changes.

Switch branches:

```bash
git switch main
```

After fixing the production bug, return:

```bash
git switch feature-dashboard
```

Restore the changes:

```bash
git stash pop
```

## 16.2 Useful Stash Commands

Save with a message:

```bash
git stash push -m "Dashboard development"
```

List stashes:

```bash
git stash list
```

Apply the latest stash:

```bash
git stash apply
```

Apply a particular stash:

```bash
git stash apply stash@{1}
```

Remove the latest stash:

```bash
git stash drop
```

Remove all stashes:

```bash
git stash clear
```

Include untracked files:

```bash
git stash -u
```

Create a branch from a stash:

```bash
git stash branch dashboard-recovery stash@{0}
```

### `apply` vs `pop`

| Command | Restores Changes | Removes Stash Entry |
|---|---|---|
| `git stash apply` | Yes | No |
| `git stash pop` | Yes | Normally, if successfully applied |

---

# 17. Git Tags and Releases

## 17.1 What Is a Git Tag?

A tag identifies a particular point in repository history.

Tags are commonly used to mark releases.

Examples:

```text
v1.0.0
v1.1.0
v2.0.0
```

## 17.2 Lightweight Tag

```bash
git tag v1.0.0
```

A lightweight tag is simply a named reference to a commit.

## 17.3 Annotated Tag

```bash
git tag -a v1.0.0 -m "First production release"
```

Annotated tags contain additional metadata, such as the tagger, date and message.

For formal software releases, annotated tags are generally preferred.

## 17.4 List Tags

```bash
git tag
```

## 17.5 Show Tag Information

```bash
git show v1.0.0
```

## 17.6 Push a Tag

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

## 17.7 Delete a Local Tag

```bash
git tag -d v1.0.0
```

Delete a remote tag:

```bash
git push origin --delete v1.0.0
```

## 17.8 Semantic Versioning

A common release version format is:

```text
MAJOR.MINOR.PATCH
```

Example:

```text
2.4.3
```

| Component | Meaning |
|---|---|
| MAJOR | Breaking changes |
| MINOR | Backward-compatible new functionality |
| PATCH | Backward-compatible bug fixes |

Example:

```text
1.0.0 → 1.0.1
```

Bug fix.

```text
1.0.1 → 1.1.0
```

New backward-compatible feature.

```text
1.1.0 → 2.0.0
```

Breaking change.

Reference: [Semantic Versioning](https://semver.org/?utm_source=chatgpt.com)

---

# 18. Remote Repositories

## 18.1 What Is a Remote?

A remote is a named reference to another Git repository.

Usually, developers use GitHub as the remote repository.

Example:

```text
https://github.com/username/ecommerce-app.git
```

## 18.2 Add a Remote

```bash
git remote add origin https://github.com/username/ecommerce-app.git
```

Here:

- `remote` manages remote repositories.
- `add` creates a remote configuration.
- `origin` is the conventional remote name.
- The URL identifies the remote repository.

`origin` is only a conventional name. It is not a special Git keyword.

## 18.3 List Remotes

```bash
git remote
```

Show URLs:

```bash
git remote -v
```

## 18.4 Change Remote URL

```bash
git remote set-url origin https://github.com/username/new-repository.git
```

This is useful when you change repository URLs or switch authentication methods.

## 18.5 Remove a Remote

```bash
git remote remove origin
```

## 18.6 Add Multiple Remotes

```bash
git remote add origin https://github.com/username/project.git
```

```bash
git remote add upstream https://github.com/original-owner/project.git
```

This is common when contributing to an open-source project.

| Remote | Common Meaning |
|---|---|
| origin | Your primary remote repository |
| upstream | Original repository you forked from |

---

# 19. Git Push, Pull and Fetch

These three commands are fundamental to collaboration.

## 19.1 Git Push

Uploads local commits to a remote repository.

```bash
git push origin main
```

Set the upstream branch:

```bash
git push -u origin main
```

After setting the upstream, you can normally use:

```bash
git push
```

## 19.2 Git Fetch

Downloads remote references and objects without integrating them into your current branch.

```bash
git fetch origin
```

Fetch all remote branches:

```bash
git fetch --all
```

After fetching, inspect the remote changes:

```bash
git log --oneline HEAD..origin/main
```

This displays commits reachable from `origin/main` that are not reachable from your current `HEAD`.

## 19.3 Git Pull

`git pull` fetches remote changes and then integrates them into the current branch.

```bash
git pull
```

Conceptually:

```text
git pull = git fetch + integration
```

The integration may use merge or rebase depending on configuration and options.

## 19.4 Fetch vs Pull

| Feature | Fetch | Pull |
|---|---|---|
| Downloads remote updates | Yes | Yes |
| Integrates updates into current branch | No | Yes |
| Allows inspection before integration | Yes | Less directly |
| Changes current branch | No | May change it |

## 19.5 Pull With Rebase

```bash
git pull --rebase
```

Fetches remote changes and rebases your local commits on top of the updated upstream branch.

## 19.6 Pull With Fast-Forward Only

```bash
git pull --ff-only
```

This refuses to integrate if the branches have diverged and a fast-forward is not possible.

**Recommended habit:** Use `git fetch` when you want to inspect remote changes before deciding how to integrate them.

Official references:

- [Git Fetch Documentation](https://git-scm.com/docs/git-fetch?utm_source=chatgpt.com)
- [Git Pull Documentation](https://git-scm.com/docs/git-pull?utm_source=chatgpt.com)
- [Git Push Documentation](https://git-scm.com/docs/git-push?utm_source=chatgpt.com)

---

# 20. GitHub Repository Management

## 20.1 What Is GitHub?

GitHub is a platform for hosting Git repositories and collaborating on software development.

It provides features such as:

- Repository hosting.
- Pull Requests.
- Code reviews.
- Issues.
- Projects.
- GitHub Actions.
- Branch protection.
- Release management.
- Security scanning.

## 20.2 Create a Repository

Typical steps:

1. Sign in to GitHub.
2. Select **New repository**.
3. Enter the repository name.
4. Choose Public or Private.
5. Optionally add a README.
6. Create the repository.

## 20.3 Connect a Local Repository

If you already have a local project:

```bash
git init
git add -A
git commit -m "Initial commit"
```

Connect GitHub:

```bash
git remote add origin https://github.com/username/project.git
```

Rename the current branch if required:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

## 20.4 Clone a GitHub Repository

```bash
git clone https://github.com/username/project.git
```

## 20.5 HTTPS vs SSH

GitHub supports both HTTPS and SSH authentication.

| HTTPS | SSH |
|---|---|
| Uses HTTPS repository URL | Uses SSH repository URL |
| Authentication commonly uses a credential manager or token | Uses SSH keys |
| Often easier to configure initially | Convenient for repeated development |
| Works over standard HTTPS connectivity | Requires SSH connectivity |

Example HTTPS:

```bash
git clone https://github.com/username/project.git
```

Example SSH:

```bash
git clone git@github.com:username/project.git
```

**Security note:** Do not embed personal access tokens directly in repository URLs, scripts or source code.

---

# 21. Pull Requests

## 21.1 What Is a Pull Request?

A Pull Request (PR) is a GitHub collaboration feature used to propose changes from one branch into another.

It allows developers to review changes before merging them.

Example:

```text
feature-login
      |
      | Pull Request
      v
     main
```

## 21.2 Typical Pull Request Workflow

Suppose you are developing a login feature.

### Step 1: Update Main

```bash
git switch main
git pull origin main
```

### Step 2: Create a Feature Branch

```bash
git switch -c feature/login
```

### Step 3: Implement the Feature

Modify your application.

### Step 4: Stage and Commit

```bash
git add -A
git commit -m "feat(auth): implement login"
```

### Step 5: Push the Branch

```bash
git push -u origin feature/login
```

### Step 6: Create a Pull Request

On GitHub:

1. Open the repository.
2. Select **Pull Requests**.
3. Click **New Pull Request**.
4. Select the base branch: `main`.
5. Select the compare branch: `feature/login`.
6. Review the changes.
7. Create the Pull Request.

### Step 7: Code Review

Other developers can:

- Approve the changes.
- Request changes.
- Add comments.
- Suggest improvements.

### Step 8: Merge

Once requirements are satisfied, merge the Pull Request.

## 21.3 Base Branch vs Compare Branch

| Field | Meaning |
|---|---|
| Base | Branch receiving the changes |
| Compare | Branch containing the proposed changes |

Example:

```text
base: main
compare: feature/login
```

This means you want to merge `feature/login` into `main`.

## 21.4 Merge Methods on GitHub

GitHub supports several merge methods, depending on repository settings.

| Method | Description |
|---|---|
| Merge commit | Combines histories with a merge commit |
| Squash and merge | Combines PR commits into one commit on the base branch |
| Rebase and merge | Replays commits onto the base branch |

### Merge Commit

Preserves the branch structure.

### Squash and Merge

Useful when a feature branch contains many small development commits.

### Rebase and Merge

Creates a linear history by applying commits onto the base branch.

The correct method depends on the team's history and release-management conventions.

Official reference: [GitHub Pull Requests Documentation](https://docs.github.com/en/pull-requests?utm_source=chatgpt.com)

---

# 22. Code Reviews

## 22.1 What Is a Code Review?

A code review is the process of examining code written by another developer before accepting it into the main codebase.

The goal is not merely to find syntax errors.

It also checks:

- Correctness.
- Readability.
- Maintainability.
- Security.
- Performance.
- Test coverage.
- Architecture.
- Consistency with project conventions.

## 22.2 What Should You Review?

Suppose a developer creates a login API.

Review the following:

**Functionality**
- Does login work correctly?
- Are invalid credentials handled?

**Security**
- Are passwords hashed?
- Are errors safe?
- Are authorization checks present?

**Validation**
- Is input validated?
- Are missing fields handled?

**Database**
- Are queries parameterized?
- Are database errors handled?

**Testing**
- Are success and failure cases tested?

**Maintainability**
- Is the code understandable?
- Are responsibilities separated?

## 22.3 Review Comments

Good review comment:

```text
The endpoint currently returns the user object
without excluding the password hash.

Please select only the required public fields
before returning the response.
```

Poor review comment:

```text
Bad code. Fix it.
```

Code reviews should be specific, constructive and actionable.

## 22.4 Code Owners

A `CODEOWNERS` file can identify the people or teams responsible for particular parts of a repository.

Example:

```text
# All files
* @team-lead

# Authentication
/src/auth/ @security-team

# Infrastructure
/infra/ @devops-team
```

GitHub can use this information to request appropriate reviewers.

---

# 23. GitHub Issues and Projects

## 23.1 What Is a GitHub Issue?

An Issue is used to track work, bugs, feature requests or discussions related to a repository.

Example:

```text
Issue #21: Login API returns incorrect status code
```

An issue can contain:

- Title.
- Description.
- Labels.
- Assignee.
- Milestone.
- Comments.
- Related Pull Requests.

## 23.2 Common Issue Labels

| Label | Purpose |
|---|---|
| bug | Something is not working |
| enhancement | New feature or improvement |
| documentation | Documentation work |
| help wanted | Contributions are welcome |
| good first issue | Suitable for new contributors |
| security | Security-related work |

## 23.3 Linking Issues to Pull Requests

In a Pull Request description, you can write:

```text
Closes #21
```

When the PR is merged into the repository's default branch, GitHub can automatically close the linked issue.

Other supported keywords include:

```text
Fixes #21
Resolves #21
```

## 23.4 What Is GitHub Projects?

GitHub Projects provides project-management functionality.

You can organize work using:

- Tables.
- Boards.
- Roadmaps.
- Custom fields.
- Filters.
- Automations.

Example project board:

```text
Todo
  |
  |--- Build login API
  |--- Create product page
  |
In Progress
  |
  |--- Implement payment gateway
  |
Review
  |
  |--- Review authentication PR
  |
Done
  |
  |--- Setup project
```

Official references:

- [GitHub Issues Documentation](https://docs.github.com/en/issues?utm_source=chatgpt.com)
- [GitHub Projects Documentation](https://docs.github.com/en/issues/planning-and-tracking-with-projects?utm_source=chatgpt.com)

---

# 24. GitHub Actions and CI/CD Integration

## 24.1 What Is GitHub Actions?

GitHub Actions is an automation platform integrated into GitHub.

It can automatically execute tasks when events occur in your repository.

Examples:

- Run tests when code is pushed.
- Build a Next.js application.
- Run linting.
- Check TypeScript errors.
- Build Docker images.
- Deploy applications.
- Scan dependencies.

## 24.2 Important GitHub Actions Concepts

| Concept | Description |
|---|---|
| Workflow | Automated process |
| Event | Trigger that starts a workflow |
| Job | Group of steps executed on a runner |
| Step | Individual action or shell command |
| Runner | Machine that executes the workflow |
| Action | Reusable automation component |
| Artifact | File or output retained by a workflow |

## 24.3 Workflow Directory

GitHub Actions workflows are stored in:

```text
.github/
│
└── workflows/
    │
    ├── ci.yml
    └── deploy.yml
```

## 24.4 Example: Next.js CI Workflow

Create:

```text
.github/workflows/ci.yml
```

Add:

```yaml
name: Next.js CI

on:
  push:
    branches:
      - main
      - development

  pull_request:
    branches:
      - main
      - development

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install Dependencies
        run: npm ci

      - name: Run Lint
        run: npm run lint

      - name: Run Tests
        run: npm test --if-present

      - name: Build Application
        run: npm run build
```

**Note:** This example assumes the project defines the relevant npm scripts and uses a compatible Node.js version. Pin third-party actions to reviewed versions or immutable commit SHAs according to your organization's security policy.

## 24.5 How Does It Work?

Suppose you push code:

```bash
git push origin feature/login
```

GitHub receives the push event.

The workflow starts when its configured event and branch conditions match.

```text
Developer Push
      |
      v
   GitHub
      |
      v
 GitHub Actions
      |
      v
 Install Dependencies
      |
      v
    Run Lint
      |
      v
    Run Tests
      |
      v
  Build Application
      |
      v
  Success / Failure
```

If a required CI check fails, repository rules can prevent the Pull Request from being merged.

## 24.6 CI vs CD

| Term | Meaning |
|---|---|
| Continuous Integration | Frequently integrate code and automatically validate changes |
| Continuous Delivery | Keep software ready for release, with production deployment typically requiring approval |
| Continuous Deployment | Automatically deploy qualifying changes to production |

## 24.7 GitHub Actions Secrets

Do not hardcode credentials inside workflow files.

Instead, configure repository or environment secrets.

Example:

```yaml
- name: Deploy Application
  env:
    DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
  run: ./scripts/deploy.sh
```

Avoid printing secrets in workflow logs.

Official references:

- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [Workflow Syntax Documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)

---

# 25. Branching Strategies

A branching strategy defines how developers organize branches and integrate changes.

## 25.1 Main-Based Development

The repository maintains a primary branch, usually:

```text
main
```

Developers integrate small changes frequently.

Advantages:

- Simple workflow.
- Fewer long-lived branches.
- Encourages frequent integration.
- Works well with automated CI/CD.

## 25.2 Feature Branch Workflow

Each feature is developed in its own branch.

Example:

```text
main
 |
 |--- feature/login
 |
 |--- feature/payment
 |
 |--- feature/product-search
```

Each feature branch is merged through a Pull Request.

Advantages:

- Isolated development.
- Easier code review.
- Independent feature work.

Disadvantage:

- Long-lived feature branches can become difficult to integrate.

## 25.3 GitHub Flow

GitHub Flow is a lightweight branching workflow.

Typical process:

1. Start from the main branch.
2. Create a descriptive branch.
3. Make and commit changes.
4. Push the branch.
5. Open a Pull Request.
6. Review and test.
7. Merge into main.
8. Deploy according to the team's release process.

Example branch names:

```text
feature/user-registration
fix/payment-timeout
docs/api-documentation
```

## 25.4 Git Flow

Git Flow uses several long-lived and short-lived branch types.

Common branches:

| Branch | Purpose |
|---|---|
| main | Production-ready code |
| develop | Integration branch |
| feature/* | New features |
| release/* | Release preparation |
| hotfix/* | Urgent production fixes |

Example:

```text
main
 |
develop
 |
 |--- feature/login
 |
 |--- feature/payment
 |
release/1.0.0
 |
main
```

Git Flow can be useful for teams with scheduled releases and maintained release branches.

However, it introduces more branch-management complexity than simpler workflows.

## 25.5 GitHub Flow vs Git Flow

| Feature | GitHub Flow | Git Flow |
|---|---|---|
| Complexity | Low | Higher |
| Main branches | Usually one primary branch | Multiple long-lived branches |
| Release model | Often continuous deployment | Often scheduled releases |
| Feature branches | Yes | Yes |
| Release branches | Not required by default | Common |
| Suitable for | Many web applications | More structured release processes |

**Recommendation:** Do not adopt Git Flow automatically. Select a branching strategy based on deployment frequency, release requirements and team size.

---

# 26. Git Workflows for Teams

A professional team should follow consistent Git practices.

## 26.1 Recommended Branch Naming

```text
feature/user-authentication
feature/payment-integration

fix/login-validation
fix/cart-calculation

hotfix/payment-failure

refactor/user-service

docs/api-documentation
```

## 26.2 Recommended Commit Workflow

Before committing:

```bash
git status
```

Review changes:

```bash
git diff
```

Stage:

```bash
git add -A
```

Review staged changes:

```bash
git diff --staged
```

Commit:

```bash
git commit -m "feat(auth): implement user login"
```

Push:

```bash
git push
```

## 26.3 Recommended Feature Development Workflow

```text
                 main
                   |
                   |
             Create Branch
                   |
                   v
          feature/user-profile
                   |
                   |
             Write Code
                   |
                   v
              Run Tests
                   |
                   v
                Commit
                   |
                   v
                 Push
                   |
                   v
             Pull Request
                   |
                   v
              Code Review
                   |
                   v
             CI Validation
                   |
                   v
                 Merge
                   |
                   v
                  main
                   |
                   v
                 Deploy
```

## 26.4 Keep Feature Branches Updated

Suppose you are working on:

```text
feature/payment
```

Meanwhile, other developers update `main`.

You can update your feature branch using merge:

```bash
git fetch origin
git switch feature/payment
git merge origin/main
```

Or rebase:

```bash
git fetch origin
git switch feature/payment
git rebase origin/main
```

Choose according to your team's workflow.

---

# 27. Git Security and Best Practices

Git repositories can contain sensitive information. Repository security is an essential part of professional development.

## 27.1 Never Commit Environment Files

For example:

```text
.env
.env.local
.env.production
```

These files may contain:

```env
DATABASE_URL=...
JWT_SECRET=...
OPENAI_API_KEY=...
AWS_SECRET_ACCESS_KEY=...
```

Add sensitive environment files to `.gitignore`.

Example:

```gitignore
node_modules/
.next/
.env
.env.*
!.env.example
dist/
build/
coverage/
```

This example ignores environment files while allowing `.env.example` to be tracked.

Remember that `.gitignore` does not remove files that are already tracked.

## 27.2 What If You Accidentally Commit a Secret?

Simply deleting the file in a later commit is not enough.

The secret may remain in Git history.

Follow this process:

1. Immediately revoke or rotate the exposed credential.
2. Identify which repositories, branches and forks contain it.
3. Assess whether unauthorized access occurred.
4. Remove the secret from repository history where appropriate.
5. Coordinate history rewriting with collaborators.
6. Verify that the secret is no longer exposed.

**The first priority is revoking the credential, not rewriting Git history.**

## 27.3 Use `.gitignore`

Example for a Next.js application:

```gitignore
# Dependencies
node_modules/

# Next.js
.next/
out/

# Production build
build/
dist/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Logs
logs/
*.log

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Test output
coverage/
```

Do not blindly ignore every environment file if your project intentionally tracks a safe template such as `.env.example`.

## 27.4 Avoid Force Push on Shared Branches

Dangerous command:

```bash
git push --force
```

It can overwrite remote branch history.

If you must update a branch after rewriting your own history, prefer:

```bash
git push --force-with-lease
```

`--force-with-lease` checks that the remote reference has not changed unexpectedly compared with what your local Git believes it should be.

It is safer than ordinary force push, but it is still a history rewrite.

Do not use it casually on shared branches.

## 27.5 Protect the Main Branch

GitHub repository administrators can configure branch protection or rulesets.

Common requirements include:

- Require Pull Requests.
- Require approving reviews.
- Require successful status checks.
- Require conversations to be resolved.
- Restrict force pushes.
- Restrict branch deletion.
- Require signed commits.
- Require linear history.

The exact available controls depend on repository configuration and GitHub plan. [GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches?utm_source=chatgpt.com)

## 27.6 Use Least Privilege

Give developers only the repository permissions they need.

Avoid giving every contributor administrator access.

## 27.7 Review Dependencies

Useful commands:

```bash
npm audit
```

```bash
npm outdated
```

These help identify dependency vulnerabilities and outdated packages.

They do not replace a complete software supply-chain security process.

## 27.8 Sign Commits

Git supports commit signing.

For example, with GPG:

```bash
git config --global commit.gpgsign true
```

Commit signing helps verify the authenticity of a commit's signature.

GitHub also supports SSH-based commit signing.

A signed commit does not automatically mean the code is secure or correct.

---

# 28. Advanced Git Troubleshooting

This section covers common real-world problems.

## 28.1 Problem: Nothing to Commit

Message:

```text
nothing to commit, working tree clean
```

Meaning:

Git does not detect any changes that need to be committed.

Check:

```bash
git status
```

## 28.2 Problem: Rejected Push

Message:

```text
! [rejected] main -> main (fetch first)
```

Possible reason:

The remote branch contains commits that your local branch does not have.

Solution:

```bash
git fetch origin
```

Inspect changes:

```bash
git log --oneline --graph --all
```

Then integrate the remote changes:

```bash
git pull --rebase origin main
```

Finally:

```bash
git push origin main
```

If conflicts occur, resolve them before continuing.

## 28.3 Problem: Merge Conflict

Check:

```bash
git status
```

Resolve conflict markers in the affected files.

Then:

```bash
git add .
git commit
```

If the merge is still in progress and you want to cancel:

```bash
git merge --abort
```

## 28.4 Problem: Wrong Commit Message

If the latest commit has not been shared:

```bash
git commit --amend -m "Correct commit message"
```

If the commit has already been pushed, coordinate with your team before rewriting history.

## 28.5 Problem: Accidentally Committed the Wrong File

If you have not pushed the commit:

```bash
git reset --soft HEAD~1
```

Remove the file from staging:

```bash
git restore --staged sensitive-file.txt
```

Then update `.gitignore` if necessary.

If a secret was committed, rotate it immediately.

## 28.6 Problem: Accidentally Deleted a File

Restore it from the current commit:

```bash
git restore app.js
```

Or restore it from a specific commit:

```bash
git restore --source=<commit-id> app.js
```

This restores the file's content. It does not recover unrelated uncommitted work.

## 28.7 Problem: Detached HEAD

You may see:

```text
You are in 'detached HEAD' state
```

This means you checked out a commit directly rather than working on a branch.

Create a branch to preserve new work:

```bash
git switch -c recovery-branch
```

## 28.8 Problem: Wrong Branch

Check your current branch:

```bash
git branch --show-current
```

If you have not committed and want to move your changes, Git may allow you to switch branches directly:

```bash
git switch correct-branch
```

If Git refuses because of conflicting changes, inspect `git status` and decide whether to commit, stash or restore them.

## 28.9 Problem: Large Files Accidentally Committed

Git is not designed to efficiently manage large binary assets as ordinary source files.

Options include:

- Remove unnecessary files.
- Use Git LFS for supported large files.
- Store generated artifacts outside the Git repository.
- Use object storage for application uploads.

Git LFS: [Git Large File Storage](https://git-lfs.com/?utm_source=chatgpt.com)

## 28.10 Problem: Find Who Changed a Line

Use:

```bash
git blame app.js
```

This displays the commit and author associated with each line.

You can also inspect a particular line range:

```bash
git blame -L 10,30 app.js
```

## 28.11 Problem: Search Commit History

Search commit messages:

```bash
git log --grep="authentication"
```

Search for changes affecting a particular file:

```bash
git log -- app.js
```

Search for a string in Git history:

```bash
git log -S "JWT_SECRET" --all
```

Be careful: searching for a leaked secret does not remove it from history.

---

# 29. Useful Advanced Git Commands

Here is a quick reference of additional commands.

| Command | Purpose |
|---|---|
| `git status -sb` | Short branch and status view |
| `git branch -vv` | Show branches and upstream information |
| `git show HEAD` | Show latest commit |
| `git diff main..feature` | Compare branch differences |
| `git diff --name-only` | List changed file names |
| `git shortlog -sn` | Summarize commits by author |
| `git reflog` | Inspect local reference history |
| `git bisect` | Find the commit that introduced a bug |
| `git blame file.js` | Inspect line-level change history |
| `git clean -n` | Preview untracked files that would be removed |
| `git clean -fd` | Remove untracked files and directories |
| `git worktree list` | List working trees |
| `git archive` | Create an archive from a repository tree |

### Git Bisect Example

Suppose a bug appeared somewhere in the last 20 commits.

Instead of checking every commit manually, use binary search.

Start:

```bash
git bisect start
```

Mark the current commit as bad:

```bash
git bisect bad
```

Mark an older known-good commit:

```bash
git bisect good <commit-id>
```

Git checks out intermediate commits.

Test the application and mark each result:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Continue until Git identifies the first bad commit.

Finish:

```bash
git bisect reset
```

---

# 30. Real-World Git Workflow for a Full-Stack Application

Let's combine the concepts into a realistic workflow.

Suppose you are developing an e-commerce application using:

```text
Next.js
Node.js
Express
MongoDB
Redis
```

Your repository structure:

```text
ecommerce-app/
│
├── frontend/
│   ├── app/
│   ├── components/
│   └── package.json
│
├── backend/
│   ├── controllers/
│   ├── routes/
│   ├── models/
│   └── package.json
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── .gitignore
├── README.md
└── package.json
```

## Step 1: Clone Repository

```bash
git clone https://github.com/username/ecommerce-app.git
```

## Step 2: Enter Project

```bash
cd ecommerce-app
```

## Step 3: Update Main

```bash
git switch main
git pull origin main
```

## Step 4: Create Feature Branch

```bash
git switch -c feature/product-search
```

## Step 5: Implement Feature

You implement:

- Search input in Next.js.
- Search API in Express.
- MongoDB query.
- Redis caching.

## Step 6: Test

Run frontend and backend tests.

## Step 7: Commit

```bash
git add -A
git commit -m "feat(products): implement product search"
```

## Step 8: Push

```bash
git push -u origin feature/product-search
```

## Step 9: Create Pull Request

Open a PR targeting `main`.

Description:

```markdown
## Summary

Implemented product search functionality.

## Changes

- Added product search UI.
- Added backend search endpoint.
- Integrated MongoDB search.
- Added Redis caching.

## Testing

- Tested search with valid keywords.
- Tested empty search input.
- Tested no-result scenarios.
- Tested Redis cache behavior.
```

## Step 10: Code Review and CI

Other developers review your code.

GitHub Actions runs:

```text
Lint
  |
Tests
  |
Build
```

## Step 11: Merge

After approvals and required checks pass, merge the PR.

## Step 12: Deploy

Your deployment workflow can deploy the updated application to the production environment.

This is a standard example of how Git, GitHub and CI/CD work together.

---

# 31. Common Git Mistakes to Avoid

| Mistake | Better Practice |
|---|---|
| Working directly on `main` | Use feature branches and Pull Requests |
| Committing everything without review | Inspect `git diff --staged` |
| Using meaningless commit messages | Write descriptive messages |
| Force-pushing shared branches | Avoid rewriting shared history |
| Committing `.env` files | Configure `.gitignore` |
| Rebasing shared commits without coordination | Rebase private feature branches |
| Merging without testing | Run relevant tests first |
| Keeping feature branches open too long | Integrate changes frequently |
| Ignoring merge conflicts | Understand both sides before resolving |
| Using `git reset --hard` casually | Understand its impact before executing |
| Treating GitHub as a backup system | Maintain separate backups |
| Giving every developer admin access | Follow least privilege |
| Merging without code review | Use repository review requirements |

---

# 32. Interview Questions and Answers

## Basic-Level Questions

### Q1. What is Git?

Git is a distributed version control system that tracks changes in files and enables developers to collaborate on projects.

### Q2. What is the difference between Git and GitHub?

Git is the version control system. GitHub is a platform for hosting Git repositories and providing collaboration tools.

### Q3. What is a repository?

A repository is a Git-managed project containing files, commits, branches and repository history.

### Q4. What is a commit?

A commit is a recorded snapshot of a repository at a particular point in time.

### Q5. What is a branch?

A branch is a movable reference to a commit that allows independent development.

### Q6. What is the staging area?

The staging area contains changes selected for the next commit.

### Q7. What is `.gitignore`?

It specifies intentionally untracked files that Git should ignore.

### Q8. What is the purpose of `git clone`?

It creates a local copy of an existing Git repository.

### Q9. What is the purpose of `git fetch`?

It downloads remote updates without integrating them into the current branch.

### Q10. What is the purpose of `git pull`?

It fetches remote updates and integrates them into the current branch.

## Intermediate-Level Questions

### Q11. What is the difference between merge and rebase?

Merge integrates branch histories and may create a merge commit. Rebase reapplies commits onto a new base, creating new commit identities.

### Q12. What is a merge conflict?

A merge conflict occurs when Git cannot automatically reconcile competing changes.

### Q13. What is cherry-pick?

Cherry-pick applies the changes introduced by a selected commit to the current branch.

### Q14. What is Git stash?

Git stash temporarily stores uncommitted changes so you can work on something else.

### Q15. What is the difference between reset and revert?

Reset moves the branch reference and can alter the index and working tree. Revert creates a new commit that reverses an earlier commit's changes.

### Q16. What is a Pull Request?

A Pull Request is a proposal to merge changes from one branch into another, usually with review and automated checks.

### Q17. What is a detached HEAD?

It means `HEAD` points directly to a commit instead of a branch reference.

### Q18. What is Git reflog?

Reflog records local reference updates and can help recover commits after certain mistakes.

### Q19. What is a Git tag?

A tag is a named reference to a specific Git object, commonly used to identify releases.

### Q20. What is a remote repository?

A remote repository is another Git repository that your local repository can communicate with.

## Advanced-Level Questions

### Q21. What is interactive rebase?

Interactive rebase allows developers to reorder, edit, combine, reword or remove commits.

### Q22. What is the difference between `git reset --soft`, `--mixed` and `--hard`?

- Soft: Moves the branch but keeps changes staged.
- Mixed: Moves the branch and resets staging.
- Hard: Moves the branch and resets staging and tracked working-tree files.

### Q23. Why should you avoid rebasing shared branches?

Rebase rewrites commit history. Other developers may have commits based on the old history, causing divergence and integration problems.

### Q24. What is `git push --force-with-lease`?

It is a safer force-push option that checks whether the remote reference still matches the expected value before updating it.

### Q25. What is Git bisect?

Git bisect uses binary search to identify the commit that introduced a bug.

### Q26. What is the difference between a lightweight and annotated tag?

A lightweight tag is a simple reference. An annotated tag is a separate Git object containing metadata and a message.

### Q27. What is GitHub Actions?

GitHub Actions is a workflow automation platform used for CI/CD, testing, building and deployment.

### Q28. What is the difference between GitHub Flow and Git Flow?

GitHub Flow is a simpler workflow centered around a primary branch and Pull Requests. Git Flow uses additional branches for development, releases and hotfixes.

### Q29. How do you protect the main branch?

Use repository rules or branch protection to require Pull Requests, approvals, successful CI checks and other conditions.

### Q30. How do you recover a commit after an accidental reset?

Inspect `git reflog`, identify the correct commit, and create a recovery branch or move the branch reference back to that commit.

---

# 33. Practical Assignments

Complete these assignments to develop real-world Git skills.

## Assignment 1: Repository Setup

Create a new repository for a full-stack application.

Requirements:

- Initialize Git.
- Configure username and email.
- Create a `.gitignore`.
- Add a README.
- Create the initial commit.
- Push to GitHub.

## Assignment 2: Branch Management

Create these branches:

```text
main
feature/authentication
feature/products
feature/payment
```

Implement a small change in each branch.

Merge them into `main` using Pull Requests.

## Assignment 3: Merge Conflict Practice

1. Create two branches.
2. Modify the same line in the same file.
3. Commit both changes.
4. Merge the branches.
5. Resolve the conflict.
6. Test the final result.

## Assignment 4: Rebase Practice

1. Create a feature branch.
2. Add multiple commits.
3. Update `main`.
4. Rebase the feature branch onto `main`.
5. Inspect the history using:

```bash
git log --oneline --graph --all
```

## Assignment 5: Reset, Revert and Reflog

Practice:

```bash
git reset --soft HEAD~1
```

```bash
git reset HEAD~1
```

```bash
git revert <commit-id>
```

```bash
git reflog
```

Perform these exercises in a test repository, not a production repository.

## Assignment 6: GitHub Pull Request

Create a feature branch and submit a Pull Request.

Requirements:

- Add a clear description.
- Include testing details.
- Review the diff.
- Request a review.
- Resolve review comments.
- Merge the PR.

## Assignment 7: GitHub Actions

Create a CI workflow that:

- Checks out the repository.
- Sets up Node.js.
- Installs dependencies.
- Runs linting.
- Runs tests.
- Builds the application.

Make sure the workflow runs on Pull Requests targeting `main`.

## Assignment 8: Repository Security

Configure:

- `.gitignore`.
- Protected `main` branch or a ruleset.
- Required Pull Request reviews.
- Required CI checks.
- Appropriate repository permissions.

---

# 34. Quick Revision Notes

| Concept | Important Point |
|---|---|
| Git | Distributed version control system |
| GitHub | Git repository hosting and collaboration platform |
| Repository | Project and its Git history |
| Working Directory | Files where you work |
| Staging Area | Changes prepared for commit |
| Commit | Recorded project snapshot |
| Branch | Movable reference to a commit |
| Merge | Integrates branch histories |
| Rebase | Reapplies commits onto a new base |
| Cherry-Pick | Applies a selected commit's changes |
| Reset | Moves branch history and optionally resets index/worktree |
| Revert | Creates a commit reversing earlier changes |
| Restore | Restores file contents |
| Reflog | Records local reference updates |
| Stash | Temporarily stores uncommitted changes |
| Tag | Names a specific Git object |
| Remote | Reference to another repository |
| Fetch | Downloads remote changes |
| Pull | Fetches and integrates changes |
| Push | Uploads local commits |
| Pull Request | Proposal to merge changes |
| Code Review | Review of proposed code changes |
| GitHub Actions | Workflow automation |
| Git Flow | Structured branching strategy |
| GitHub Flow | Lightweight Pull Request workflow |
| Git Bisect | Finds the commit that introduced a bug |

---

# 35. Completion Checklist

Use this checklist to assess your understanding.

- [ ] Understand Version Control Systems.
- [ ] Understand Git vs GitHub.
- [ ] Understand Git's internal architecture.
- [ ] Understand Blob, Tree, Commit and Tag objects.
- [ ] Install and configure Git.
- [ ] Initialize and clone repositories.
- [ ] Understand Working Directory, Staging Area and Repository.
- [ ] Use `git add`, `git commit` and `git status`.
- [ ] Inspect commit history.
- [ ] Create, switch, rename and delete branches.
- [ ] Merge branches.
- [ ] Resolve merge conflicts.
- [ ] Understand merge vs rebase.
- [ ] Perform interactive rebase.
- [ ] Use cherry-pick.
- [ ] Understand reset, revert and restore.
- [ ] Recover commits using reflog.
- [ ] Use Git stash.
- [ ] Create and manage tags.
- [ ] Configure remote repositories.
- [ ] Use fetch, pull and push.
- [ ] Create GitHub repositories.
- [ ] Open and manage Pull Requests.
- [ ] Perform code reviews.
- [ ] Manage GitHub Issues and Projects.
- [ ] Understand GitHub Actions.
- [ ] Implement a CI workflow.
- [ ] Understand GitHub Flow and Git Flow.
- [ ] Follow professional Git collaboration practices.
- [ ] Secure repositories and protect important branches.
- [ ] Troubleshoot common Git problems.

---

# 36. Official Documentation and Learning Resources

These are the primary references for this phase.

### Git Documentation

- [Official Git Documentation](https://git-scm.com/docs/git?utm_source=chatgpt.com)
- [Git Reference Manual](https://git-scm.com/docs?utm_source=chatgpt.com)
- [Pro Git Book](https://git-scm.com/book/en/v2?utm_source=chatgpt.com)
- [Git Branching Documentation](https://git-scm.com/docs/git-branch?utm_source=chatgpt.com)
- [Git Merge Documentation](https://git-scm.com/docs/git-merge?utm_source=chatgpt.com)

### GitHub Documentation

- [GitHub Docs](https://docs.github.com/?utm_source=chatgpt.com)
- [GitHub Pull Requests](https://docs.github.com/en/pull-requests?utm_source=chatgpt.com)
- [GitHub Actions](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Issues](https://docs.github.com/en/issues?utm_source=chatgpt.com)
- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches?ref=blog.vibemindsolutions.ai&utm_source=chatgpt.com)

---

# Phase 41 Completed

You have now covered **Advanced Git, GitHub, Version Control and Team Collaboration**.

The most important practical skills to master are:

1. Branching and merging.
2. Merge conflict resolution.
3. Merge vs rebase.
4. Reset vs revert.
5. Remote repository management.
6. Pull Requests and code reviews.
7. GitHub Actions and CI workflows.
8. Repository security and branch protection.

## Next: Phase 42 – Advanced CI/CD Pipelines, GitHub Actions and Automated Deployment

Upcoming topics:

1. CI/CD Architecture and Fundamentals.
2. GitHub Actions Advanced Concepts.
3. Workflow Triggers and Event Filters.
4. Jobs, Steps and Runners.
5. Workflow Dependencies and Parallel Execution.
6. Environment Variables and Secrets.
7. Reusable Workflows and Composite Actions.
8. Matrix Builds and Test Strategies.
9. Build Artifacts and Caching.
10. Automated Testing Pipelines.
11. Docker Image Build and Push Automation.
12. Automated Deployment to VPS.
13. Deployment to AWS.
14. Deployment to Vercel and Render.
15. Environment-Based Deployment.
16. SSH-Based Deployment Automation.
17. Rolling, Blue-Green and Canary Deployments.
18. Rollback Strategies.
19. Deployment Security and Supply Chain Security.
20. Production CI/CD Pipeline Design.

**When you're ready, say: "OK, Phase 42".**