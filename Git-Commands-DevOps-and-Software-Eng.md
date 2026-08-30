Absolutely. I’ll structure this like a **Senior DevOps Engineer’s Git command reference**, not just a list of Git commands.

Your document already has many useful commands, but some are **old-style, unsafe, or technically incorrect**. I’ll also point those out so you don’t build bad habits.

# Git Commands for DevOps & Software Engineers

A good Git learning path is:

**Beginner → Daily Git → Intermediate → Advanced/Recovery → DevOps workflows**

---

# 1. Beginner Level — Must Know

These are commands every developer/DevOps engineer should know without looking them up.

| Command                    | What it does             | Example                                    | Common use                    |
| -------------------------- | ------------------------ | ------------------------------------------ | ----------------------------- |
| `git init`                 | Create a Git repository  | `git init`                                 | Start Git in a project        |
| `git clone`                | Clone remote repository  | `git clone https://github.com/org/app.git` | Get project locally           |
| `git status`               | Show working-tree status | `git status`                               | **Daily command**             |
| `git add`                  | Stage changes            | `git add app.js`                           | Prepare changes for commit    |
| `git add .`                | Stage all changes        | `git add .`                                | Stage entire project          |
| `git commit`               | Create commit            | `git commit -m "Add login API"`            | Save logical change           |
| `git log`                  | View commit history      | `git log`                                  | Investigate history           |
| `git log --oneline`        | Compact history          | `git log --oneline`                        | Daily history check           |
| `git diff`                 | Show unstaged changes    | `git diff`                                 | Review before commit          |
| `git diff --staged`        | Show staged changes      | `git diff --staged`                        | Review what will be committed |
| `git branch`               | List branches            | `git branch`                               | See local branches            |
| `git branch feature/login` | Create branch            | `git branch feature/login`                 | Create feature branch         |
| `git switch`               | Switch branch            | `git switch dev`                           | Change branch                 |
| `git switch -c`            | Create + switch          | `git switch -c feature/login`              | **Recommended**               |
| `git merge`                | Merge branch             | `git merge feature/login`                  | Integrate work                |
| `git fetch`                | Download remote changes  | `git fetch origin`                         | Safely inspect remote updates |
| `git pull`                 | Fetch + integrate        | `git pull origin dev`                      | Update local branch           |
| `git push`                 | Upload commits           | `git push origin dev`                      | Send changes to remote        |
| `git remote -v`            | Show remote URLs         | `git remote -v`                            | Check GitHub/GitLab repo      |

### Basic daily workflow

```bash
git status

git switch -c feature/payment

# make changes

git diff

git add .

git diff --staged

git commit -m "Add payment validation"

git push -u origin feature/payment
```

After the first push:

```bash
git push
```

---

# 2. Git Configuration

These are important when setting up a new machine.

| Command                                            | Purpose                   |
| -------------------------------------------------- | ------------------------- |
| `git config --list`                                | Show configuration        |
| `git config --global user.name "Ravinder Singh"`   | Set global username       |
| `git config --global user.email "you@example.com"` | Set global email          |
| `git config user.name "Ravinder Singh"`            | Repository-specific name  |
| `git config user.email "you@example.com"`          | Repository-specific email |
| `git config --get remote.origin.url`               | Show origin URL           |
| `git remote -v`                                    | Show all remotes          |

### Recommended setup

```bash
git config --global user.name "Ravinder Singh"
git config --global user.email "your-email@example.com"
```

Check:

```bash
git config --global --list
```

---

# 3. Beginner — Branching

Branching is fundamental for DevOps engineers because CI/CD pipelines are normally triggered by branch/PR activity.

### Create branch

```bash
git branch feature/payment
```

Switch:

```bash
git switch feature/payment
```

Or simply:

```bash
git switch -c feature/payment
```

Older syntax:

```bash
git checkout -b feature/payment
```

`checkout -b` still works, but I recommend learning:

```bash
git switch -c feature/payment
```

---

### List branches

```bash
git branch
```

All local + remote:

```bash
git branch -a
```

Remote only:

```bash
git branch -r
```

---

### Delete branch

Safe delete:

```bash
git branch -d feature/payment
```

Force delete:

```bash
git branch -D feature/payment
```

**Important:**

```bash
-d
```

means Git checks whether the branch has been merged.

```bash
-D
```

forces deletion.

---

# 4. Remote Repository

### Add remote

```bash
git remote add origin git@github.com:username/repository.git
```

Check:

```bash
git remote -v
```

Change remote:

```bash
git remote set-url origin git@github.com:username/new-repository.git
```

Remove remote:

```bash
git remote remove origin
```

---

# 5. Fetch vs Pull — Very Important for DevOps

This distinction should be crystal clear.

## `git fetch`

```bash
git fetch origin
```

Downloads remote changes but **doesn't modify your current branch**.

For example:

```text
Remote:
A---B---C---D

Local:
A---B---C
```

After:

```bash
git fetch origin
```

you have:

```text
origin/main:
A---B---C---D

local main:
A---B---C
```

You can inspect before merging.

---

## `git merge`

```bash
git merge origin/main
```

Now local branch incorporates the remote changes.

---

## `git pull`

Essentially:

```bash
git fetch
git merge
```

So:

```bash
git pull origin main
```

is common.

### Your document has this:

```bash
git pull origin/master
```

❌ This is not the normal syntax.

Use:

```bash
git pull origin master
```

or:

```bash
git pull origin main
```

depending on the branch name.

---

# 6. Working With Changes

## See changes

```bash
git diff
```

Unstaged changes.

```bash
git diff --staged
```

Staged changes.

Compare branches:

```bash
git diff main..feature/payment
```

Compare specific commits:

```bash
git diff abc123..def456
```

---

# 7. Undo Changes — Extremely Important

This is where developers often destroy work.

Understand these three concepts:

```text
Working Directory
       ↓
     Staging
       ↓
    Commit
```

---

## Discard changes in one file

Modern command:

```bash
git restore app.js
```

Your old command:

```bash
git checkout -- app.js
```

still works, but `git restore` is clearer.

---

## Discard ALL uncommitted changes

```bash
git restore .
```

⚠️ This destroys your uncommitted modifications.

---

# 8. Undo `git add`

Suppose:

```bash
git add app.js
```

but you don't want it staged anymore.

Use:

```bash
git restore --staged app.js
```

This **doesn't delete your changes**.

It simply moves:

```text
Staged → Unstaged
```

---

# 9. Reset — Beginner/Intermediate

You should understand:

```bash
git reset
```

There are three major modes.

### Soft

```bash
git reset --soft HEAD~1
```

Undo commit but keep changes staged.

```text
Commit
  ↓
Staging
```

---

### Mixed

```bash
git reset HEAD~1
```

or:

```bash
git reset --mixed HEAD~1
```

Undo commit and unstage changes, but keep files modified.

```text
Commit
  ↓
Working Directory
```

This is what your:

```bash
git reset HEAD~
```

is doing.

---

### Hard

```bash
git reset --hard HEAD~1
```

Undo commit **and delete the changes from the working tree**.

⚠️ Dangerous.

---

# 10. Undo Last Commit

If you haven't pushed:

### Keep changes staged

```bash
git reset --soft HEAD~1
```

### Keep changes but unstaged

```bash
git reset HEAD~1
```

### Completely delete commit + changes

```bash
git reset --hard HEAD~1
```

---

# 11. Revert — Very Important for DevOps

When a commit is already pushed to a shared branch, generally prefer:

```bash
git revert <commit>
```

Example:

```bash
git revert abc123
```

This creates a **new commit that reverses the old commit**.

This is much safer than:

```bash
git reset --hard
git push --force
```

### Rule of thumb

| Situation              | Prefer                                |
| ---------------------- | ------------------------------------- |
| Local commit           | `git reset`                           |
| Shared/pushed commit   | `git revert`                          |
| Production branch      | Usually `git revert`                  |
| Rewrite private branch | `git reset` + force push if necessary |

---

# 12. Merge

Merge current branch with another:

```bash
git switch main
git merge feature/payment
```

This means:

```text
feature/payment
       ↓
     main
```

---

## No Fast Forward

```bash
git merge --no-ff feature/payment
```

Useful when you want Git to explicitly preserve the feature-branch merge commit.

Your command:

```bash
git pull --no-ff
```

can be used, but don't confuse **pull** with **merge**.

Usually you'll encounter:

```bash
git merge --no-ff feature/payment
```

more often when controlling merge behavior.

---

# 13. Merge Conflict

When Git cannot automatically merge:

```bash
git status
```

Find conflicted files.

Edit them.

Then:

```bash
git add .
git commit
```

Or:

```bash
git merge --continue
```

depending on the operation/workflow.

### Abort merge

If you want to abandon an **ongoing merge**:

```bash
git merge --abort
```

Important correction to your document:

> `git merge --abort` does **not** undo an already completed merge.

It only aborts an active merge in progress.

---

# 14. Stash — Daily DevOps Skill

Suppose you're working on:

```text
feature/payment
```

and suddenly need to switch to production hotfix.

You have uncommitted changes.

### Save changes

```bash
git stash
```

Switch:

```bash
git switch main
```

Do your hotfix.

Return:

```bash
git switch feature/payment
```

Restore:

```bash
git stash pop
```

---

## Stash with message

```bash
git stash push -m "payment API work"
```

List:

```bash
git stash list
```

Example:

```text
stash@{0}: On feature/payment: payment API work
stash@{1}: On feature/login: login validation
```

Apply without deleting stash:

```bash
git stash apply stash@{0}
```

Apply and remove:

```bash
git stash pop stash@{0}
```

Delete:

```bash
git stash drop stash@{0}
```

Delete all:

```bash
git stash clear
```

⚠️ Be careful with `stash clear`.

---

# 15. Remove Untracked Files

See what would be removed:

```bash
git clean -n
```

Actually remove files:

```bash
git clean -f
```

Remove untracked directories too:

```bash
git clean -fd
```

Very dangerous:

```bash
git clean -fdx
```

`-x` also removes ignored files.

For example:

```text
node_modules/
.env
dist/
```

could be removed if ignored.

### Always preview first:

```bash
git clean -ndx
```

Then:

```bash
git clean -fdx
```

---

# 16. Tags — Important for DevOps/Release

Tags are commonly used for releases.

Create:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

List:

```bash
git tag
```

Push:

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

Delete local tag:

```bash
git tag -d v1.0.0
```

Delete remote tag:

```bash
git push origin --delete v1.0.0
```

Typical CI/CD workflow:

```text
commit
   ↓
merge main
   ↓
tag v1.2.0
   ↓
CI/CD
   ↓
production
```

---

# 17. Intermediate Level — History Investigation

These commands become extremely useful when you're debugging production issues.

## Pretty log

```bash
git log --oneline --graph --decorate --all
```

One of the **best Git commands to know**.

Example:

```text
*   a91d2e Merge feature/payment
|\
| * 91bc32 Add payment API
| * 22ab31 Add validation
|/
* 82ac91 Update dependencies
* 71bb20 Initial commit
```

---

## Show specific commit

```bash
git show abc123
```

---

## Search commit messages

```bash
git log --grep="payment"
```

---

## Search code changes

```bash
git log -S "paymentStatus"
```

This is extremely useful when investigating when a piece of code was introduced.

---

## Find who changed a line

```bash
git blame app.ts
```

Specific lines:

```bash
git blame -L 20,40 app.ts
```

Very useful during production debugging.

---

# 18. Advanced — Remote Branch Tracking

Create local branch tracking remote:

```bash
git switch -c dev --track origin/dev
```

Your older command:

```bash
git branch --set-upstream-to=origin/dev dev
```

is also valid.

Check tracking:

```bash
git branch -vv
```

Example:

```text
* dev      abc123 [origin/dev] Add payment API
  main     def456 [origin/main] Release v1.0
```

---

# 19. Advanced — Rebase

Rebase is important for senior engineers.

Example:

```bash
git switch feature/payment
git fetch origin
git rebase origin/main
```

Instead of:

```bash
git merge origin/main
```

Conceptually:

### Before

```text
A---B---C---D main
     \
      E---F feature
```

After rebase:

```text
A---B---C---D main
             \
              E'---F' feature
```

The commits are recreated on top of the latest `main`.

---

## Interactive Rebase

```bash
git rebase -i HEAD~3
```

Allows you to:

```text
pick
reword
edit
squash
fixup
drop
```

For example:

```text
pick 123 Add API
squash 456 Fix typo
squash 789 Fix validation
```

can become one clean commit.

Very useful before creating a PR.

---

# 20. Advanced — Rebase Conflict

During rebase:

```bash
git status
```

Fix conflict.

```bash
git add .
git rebase --continue
```

Abort:

```bash
git rebase --abort
```

Skip problematic commit:

```bash
git rebase --skip
```

---

# 21. Force Push — Senior-Level Knowledge

Never casually use:

```bash
git push --force
```

Prefer:

```bash
git push --force-with-lease
```

Why?

Suppose:

```text
Your local branch
      ↓
A---B---C

Remote
      ↓
A---B---C---D
```

Someone else pushed `D`.

A blind:

```bash
git push --force
```

can overwrite their work.

`--force-with-lease` provides a safety check.

### Rule

Prefer:

```bash
git push --force-with-lease
```

over:

```bash
git push --force
```

Especially after:

```bash
git rebase
```

---

# 22. Recover Deleted/Bad Commits — Reflog

One of the most important advanced Git commands:

```bash
git reflog
```

Git records where your HEAD and branch references have been.

Example:

```text
abc123 HEAD@{0}: commit: Add payment
def456 HEAD@{1}: reset: moving to HEAD~1
abc123 HEAD@{2}: commit: Add payment
```

You can recover:

```bash
git reset --hard abc123
```

This is why a "deleted" commit is often recoverable.

---

# 23. Recover Lost Git Objects

Your command:

```bash
git fsck --unreachable
```

is an advanced recovery technique.

Example:

```bash
git fsck --full --no-reflogs
```

Then inspect an object:

```bash
git show <hash>
```

But **learn `git reflog` first**. It is usually much easier.

---

# 24. Reset Remote Branch

Your document has:

```bash
git checkout master
git reset --hard HEAD^
git push -f
```

This works technically, but it is risky.

Modern safer approach:

```bash
git switch main
git reset --hard HEAD~1
git push --force-with-lease origin main
```

But before doing this on a shared branch, ask:

> Should I rewrite history, or should I create a revert commit?

Usually for `main`, `master`, `production`, etc.:

```bash
git revert <commit>
```

is safer.

---

# 25. Replace Local Branch With Remote

Very useful DevOps recovery operation.

Suppose your local branch is messed up and you want it to exactly match remote:

```bash
git fetch origin
git reset --hard origin/main
```

Also remove untracked files if required:

```bash
git clean -fd
```

Now local `main` matches:

```text
origin/main
```

### ⚠️ Warning

This destroys local uncommitted work.

---

# 26. Find Differences Between Local and Remote

```bash
git fetch origin
```

Commits local has but remote doesn't:

```bash
git log origin/main..main
```

Commits remote has but local doesn't:

```bash
git log main..origin/main
```

Very useful when diagnosing:

> "Why is my branch ahead/behind?"

---

# 27. Git Bisect — Advanced Debugging

This is a **very valuable senior-engineer skill**.

Suppose:

```text
v1.0 worked
v1.1 worked
v1.2 worked
v1.3 broken
v1.4 broken
```

You don't know which commit introduced the bug.

Start:

```bash
git bisect start
```

Mark current bad commit:

```bash
git bisect bad
```

Mark known good:

```bash
git bisect good v1.2
```

Git checks out a middle commit.

Test it.

If working:

```bash
git bisect good
```

If broken:

```bash
git bisect bad
```

Eventually Git identifies the offending commit.

Finish:

```bash
git bisect reset
```

This is excellent for production regression investigation.

---

# 28. Cherry Pick — Very Important for DevOps

Suppose a production hotfix exists in another branch:

```text
hotfix
  ↓
ABC123 Fix payment timeout
```

You want only that commit in `main`.

```bash
git switch main
git cherry-pick ABC123
```

This copies the change into the current branch.

Typical scenario:

```text
feature branch
       ↓
hotfix commit
       ↓
cherry-pick
       ↓
production/main
```

Very useful for urgent production fixes.

---

# 29. Git Worktree — Advanced

You can work on multiple branches simultaneously without repeatedly switching.

```bash
git worktree add ../payment-hotfix hotfix/payment
```

Now:

```text
project/
payment-hotfix/
```

You can have:

```text
Directory 1 → feature/payment
Directory 2 → hotfix/production
```

Useful for DevOps engineers handling an urgent production incident while keeping current development untouched.

---

# 30. Git Archive

Create a source archive:

```bash
git archive --format=tar.gz HEAD > source.tar.gz
```

Useful for packaging source code without the `.git` directory.

---

# 31. Git Clean vs Reset vs Restore

This is worth memorizing.

| Situation                          | Command                     |
| ---------------------------------- | --------------------------- |
| Remove file changes                | `git restore file`          |
| Remove all working changes         | `git restore .`             |
| Unstage file                       | `git restore --staged file` |
| Undo last local commit             | `git reset HEAD~1`          |
| Undo commit + keep staged          | `git reset --soft HEAD~1`   |
| Destroy commit + changes           | `git reset --hard HEAD~1`   |
| Remove untracked files             | `git clean -f`              |
| Remove untracked files/directories | `git clean -fd`             |
| Undo pushed commit safely          | `git revert <commit>`       |
| Recover lost HEAD                  | `git reflog`                |

---

# 32. Git Command Levels

If I were training a junior DevOps engineer, I'd divide Git knowledge like this.

## 🟢 Beginner — Must Know

Learn these first:

```bash
git clone
git init
git status
git add
git commit
git log
git diff
git branch
git switch
git merge
git fetch
git pull
git push
git remote
git restore
git stash
```

You should be able to use these without searching Google.

---

# 🟡 Intermediate — Professional Developer

Then learn:

```bash
git reset
git revert
git cherry-pick
git rebase
git rebase -i
git tag
git blame
git show
git log --graph
git branch -vv
git merge --abort
git rebase --abort
git stash list
git stash apply
git stash pop
git clean
```

You should understand **when** to use each one, not just memorize syntax.

---

# 🔴 Advanced — Senior DevOps / Senior Developer

Then master:

```bash
git reflog
git bisect
git worktree
git fsck
git cherry-pick
git rebase -i
git push --force-with-lease
git reset --hard
git replace
git filter-repo
```

And, more importantly, understand:

```text
Git object model
        ↓
commit
tree
blob
tag
        ↓
HEAD
        ↓
branch
        ↓
remote-tracking branch
        ↓
reflog
```

---

# 33. The 20 Git Commands I'd Expect a DevOps Engineer to Know

If you're preparing yourself for **DevOps/Senior DevOps interviews and real production work**, prioritize these:

|   # | Command                           | Importance |
| --: | --------------------------------- | ---------- |
|   1 | `git status`                      | ⭐⭐⭐⭐⭐ |
|   2 | `git log --oneline --graph --all` | ⭐⭐⭐⭐⭐ |
|   3 | `git diff`                        | ⭐⭐⭐⭐⭐ |
|   4 | `git add`                         | ⭐⭐⭐⭐⭐ |
|   5 | `git commit`                      | ⭐⭐⭐⭐⭐ |
|   6 | `git switch`                      | ⭐⭐⭐⭐⭐ |
|   7 | `git fetch`                       | ⭐⭐⭐⭐⭐ |
|   8 | `git pull`                        | ⭐⭐⭐⭐⭐ |
|   9 | `git push`                        | ⭐⭐⭐⭐⭐ |
|  10 | `git merge`                       | ⭐⭐⭐⭐⭐ |
|  11 | `git rebase`                      | ⭐⭐⭐⭐⭐ |
|  12 | `git stash`                       | ⭐⭐⭐⭐   |
|  13 | `git reset`                       | ⭐⭐⭐⭐   |
|  14 | `git revert`                      | ⭐⭐⭐⭐⭐ |
|  15 | `git cherry-pick`                 | ⭐⭐⭐⭐   |
|  16 | `git reflog`                      | ⭐⭐⭐⭐⭐ |
|  17 | `git tag`                         | ⭐⭐⭐⭐   |
|  18 | `git blame`                       | ⭐⭐⭐     |
|  19 | `git bisect`                      | ⭐⭐⭐⭐   |
|  20 | `git push --force-with-lease`     | ⭐⭐⭐⭐⭐ |

---

# 34. Your Existing Document — Commands I'd Change

A few important corrections:

### ❌ Old

```bash
git checkout .
```

### ✅ Prefer

```bash
git restore .
```

---

### ❌ Your command

```bash
git pull origin/master
```

### ✅ Correct

```bash
git pull origin master
```

---

### ❌ Dangerous

```bash
git push -f
```

### ✅ Prefer

```bash
git push --force-with-lease
```

---

### ❌ Don't use `merge --abort` to undo a completed merge

```bash
git merge --abort
```

is only for an **ongoing merge conflict**.

To undo an already-created merge commit, normally:

```bash
git revert -m 1 <merge-commit>
```

---

### ❌ Don't use this casually

```bash
git reset --hard
```

Understand exactly what it will destroy first.

---

### ❌ Avoid storing credentials in remote URLs

Your document has:

```bash
git config remote.origin.url https://USERNAME:PASSWORD@github.com/...
```

**Do not use this pattern.**

Passwords/tokens can leak through shell history, configuration, logs, CI output, etc.

Prefer:

```bash
git@github.com:USERNAME/REPOSITORY.git
```

with SSH authentication, or HTTPS with an appropriate credential manager/token mechanism.

---

# 35. DevOps Git Workflow You Should Practice

For your DevOps learning, I recommend practicing this complete workflow locally:

```bash
# Clone
git clone <repository>

# Enter project
cd project

# Check
git status

# Update remote information
git fetch origin

# Create feature branch
git switch -c feature/payment

# Make changes
vim app.js

# Review
git diff

# Stage
git add app.js

# Review staged changes
git diff --staged

# Commit
git commit -m "Add payment API"

# Push
git push -u origin feature/payment
```

Then simulate a PR workflow:

```text
feature/payment
       ↓
     PR
       ↓
     CI
       ↓
 code review
       ↓
    merge
       ↓
     main
       ↓
     tag
       ↓
     CD
       ↓
 production
```

That is the Git knowledge that starts connecting **Git → CI/CD → DevOps**.

---

## 🔥 One-page mental model

Don't memorize 100 commands randomly. Understand this:

```text
                    REMOTE
                      │
                 git fetch
                      ↓
              origin/main
                      │
                 git merge
                      ↓
                    main
                      │
                 git switch
                      ↓
              feature/payment
                      │
                  git add
                      ↓
                  STAGING
                      │
                git commit
                      ↓
                   LOCAL
                      │
                  git push
                      ↓
                    REMOTE
```

And for recovery:

```text
Uncommitted change
       │
       ├── git restore       → discard
       │
       ├── git stash         → temporarily save
       │
       └── git add           → staging
                              │
                              ↓
                           commit
                              │
                    ┌─────────┴─────────┐
                    ↓                   ↓
               git reset           git revert
             local history       shared history
                    │
                    ↓
                git reflog
                 recovery
```

**For a DevOps engineer, I would especially focus next on `fetch vs pull`, `merge vs rebase`, `reset vs revert`, `stash`, `cherry-pick`, `reflog`, `force-with-lease`, and `bisect`.** Those are the areas where Git knowledge moves from "I can use Git" to "I can safely operate Git in production."
