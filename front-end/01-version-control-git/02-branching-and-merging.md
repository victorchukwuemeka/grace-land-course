# Module 01: Branching, Merging, Rebasing & Resolving Conflicts

Branching is Git's superpower. Because branches in Git are merely lightweight, movable pointers to 40-character SHA-1 commit hashes (only 41 bytes on disk), creating and switching branches is virtually instantaneous.

---

## 1. What is a Branch?

A branch represents an independent line of development.

```
       (commit A) ───► (commit B) ───► (commit C)  <── main (HEAD)
```

- `HEAD` is a special pointer that points to the branch you currently have checked out in your working directory.
- When you create a branch named `feature/login`:
```
       (commit A) ───► (commit B) ───► (commit C)  <── main
                                          ▲
                                          └── feature/login (HEAD)
```

---

## 2. Modern Branching Commands (`git switch` vs `git checkout`)

> [!NOTE]
> In Git version 2.23+, `git switch` and `git restore` were introduced to split the overloaded responsibilities of the legacy `git checkout` command.

```bash
# List all local branches (* marks the current branch)
git branch

# List all local and remote-tracking branches
git branch -a

# Create and switch to a new branch in one step
git switch -c feature/dark-mode

# Switch to an existing branch
git switch main

# Delete a branch safely (only if fully merged)
git branch -d feature/dark-mode

# Force delete a branch (even if unmerged changes exist)
git branch -D feature/experimental
```

### Industry Branch Naming Conventions:
- `feature/<ticket-or-description>` (e.g., `feature/user-authentication`)
- `bugfix/<issue-id>` or `fix/<description>` (e.g., `fix/navbar-mobile-overflow`)
- `hotfix/<critical-patch>` (e.g., `hotfix/checkout-crash`)
- `chore/<task>` (e.g., `chore/upgrade-tailwind`)

---

## 3. Merging: Fast-Forward vs 3-Way Merge Commits

When your feature is complete and tested, you merge it back into your primary branch (e.g., `main`).

### 1. Fast-Forward Merge
If the target branch (`main`) has had **no new commits** since the feature branch was branched off:

```
Before Merge:
main:            (C1) ──► (C2)
                            \
feature/hero:                (C3) ──► (C4)

After Fast-Forward Merge (`git merge feature/hero` while on main):
main & feature:  (C1) ──► (C2) ──► (C3) ──► (C4)
```
Git simply moves the `main` pointer forward. No new merge commit is created.

### 2. Three-Way Merge Commit
If `main` has moved forward with new commits while you were working on your feature:

```
Before Merge:
main:            (C1) ──► (C2) ────────► (C5)
                            \
feature/hero:                (C3) ──► (C4)

After 3-Way Merge:
main:            (C1) ──► (C2) ────────► (C5) ────► (M1: Merge commit)
                            \                      ▲
feature/hero:                (C3) ──► (C4) ────────┘
```
Git creates a new special commit (`M1`) that has **two parent commits** (`C5` and `C4`).

---

## 4. Rebasing: Creating a Clean, Linear History

Rebasing is an alternative to merging. Instead of a merge commit, Git takes the commits from your feature branch, rewinds them, and **replays** them one by one on top of the latest commit of the base branch.

```bash
# While on your feature branch:
git switch feature/hero
git rebase main
```

```
Before Rebase:
main:            (C1) ──► (C2) ──► (C5)
                            \
feature/hero:                (C3) ──► (C4)

After Rebase:
main:            (C1) ──► (C2) ──► (C5)
                                    \
feature/hero:                        (C3') ──► (C4')  <-- Brand new commit hashes!
```

> [!WARNING]
> **The Golden Rule of Rebasing**:
> **NEVER rebase commits that have been pushed to a shared public branch.**
> Rebasing rewrites commit hashes. If other developers have based work on those commits, rebasing creates duplicate commits and causes severe confusion. Only rebase local, unshared feature branches!

---

## 5. Mastering Merge Conflicts

A merge conflict occurs when two branches have modified the **exact same line** in the same file differently, and Git cannot automatically decide which version is correct.

### Step 1: Triggering a Conflict
When you run `git merge` or `git rebase`, Git halts and outputs:
```text
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### Step 2: Understanding Conflict Markers
Open the conflicted file in VS Code. Git will mark the conflict like this:

```html
<<<<<<< HEAD (Current Change - usually main)
<h1 class="title">Welcome to Grace Frontend Academy</h1>
=======
<h1 class="hero-heading">Master Modern Frontend Engineering</h1>
>>>>>>> feature/hero (Incoming Change)
```

- Everything between `<<<<<<< HEAD` and `=======` is what existed on the branch you are merging into.
- Everything between `=======` and `>>>>>>> feature/hero` is the incoming code from the feature branch.

### Step 3: Resolving the Conflict
1. Decide which version to keep, or manually blend both into a single correct version.
2. Delete all marker lines (`<<<<<<<`, `=======`, `>>>>>>>`).
3. Final clean code:
```html
<h1 class="hero-heading">Welcome to Grace Frontend Academy - Master Modern Frontend</h1>
```

### Step 4: Staging and Completing the Merge
```bash
# 1. Stage the resolved file
git add index.html

# 2. Check status to ensure all conflicts are resolved
git status

# 3. Finalize the commit
git commit -m "Merge branch 'feature/hero' and resolve heading conflict"
```
*(If you were in the middle of a rebase, run `git rebase --continue` instead of committing).*
