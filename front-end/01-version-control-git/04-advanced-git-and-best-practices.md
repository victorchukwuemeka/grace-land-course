# Module 01: Advanced Git, Undoing Mistakes & Best Practices

Even senior engineers make mistakes in Git. Knowing how to safely recover from errors without losing work is what separates amateurs from professionals.

---

## 1. Undoing Mistakes at Every Stage

| Where is the change? | Command to undo | Effect |
| :--- | :--- | :--- |
| **Working Directory** (unstaged) | `git restore index.html` | Discards unstaged edits back to last commit. |
| **Staging Area** (after `git add`) | `git restore --staged index.html` | Unstages file; keeps changes in working directory. |
| **Last Commit** (need to edit message) | `git commit --amend -m "New message"` | Updates previous commit with new message or staged files. |
| **Committed locally** (not yet pushed) | `git reset --soft HEAD~1` | Undoes commit; keeps changes staged. |
| **Committed locally** (need to unstage) | `git reset --mixed HEAD~1` | Undoes commit; keeps changes unstaged in working tree. |
| **Committed locally** (delete completely) | `git reset --hard HEAD~1` | ⚠️ **DESTRUCTIVE**: Erases commit and working tree edits. |
| **Committed & pushed to remote** | `git revert <commit-hash>` | ✅ **SAFE**: Creates a *new* commit that undoes the target commit. |

> [!CAUTION]
> Never use `git reset --hard` on branches that other people share or if you have unsaved thoughts in your working directory. Use `git revert` when undoing pushed commits on shared branches.

---

## 2. Git Stash: Pausing Your Current Work

Imagine you are halfway through writing a complex CSS Grid layout on branch `feature/grid`, and your manager asks you to urgently fix a critical typo on `main`. You cannot commit broken code, but you cannot switch branches with dirty files that would conflict.

**Use `git stash` to shelve your uncommitted work temporarily:**

```bash
# 1. Stash dirty working directory and staged changes with a note
git stash push -m "WIP: grid layout half finished"

# 2. Check your working directory (it is now clean!)
git status

# 3. Switch branches and make the quick fix
git switch main
git switch -c hotfix/typo
# ... make fix, add, commit, push ...

# 4. Return to your feature branch
git switch feature/grid

# 5. List all stashed changes
git stash list
# stash@{0}: On feature/grid: WIP: grid layout half finished

# 6. Re-apply the stashed changes and remove from stash list
git stash pop
```

---

## 3. Interactive Rebase (`git rebase -i`): Cleaning Up History

Before opening a PR, clean up messy "fix typo", "oops", "wip" commits into clean, logical units:

```bash
# Interactively rebase the last 3 commits
git rebase -i HEAD~3
```

An editor opens with a list of commits:
```text
pick e4a1b2c feat(auth): add login form markup
pick a3f2c1d fix typo in placeholder
pick 9b1c7e4 add submit event handler
```

Commands you can replace `pick` with:
- `reword`: Change the commit message.
- `squash`: Meld commit into previous commit and combine messages.
- `fixup`: Meld commit into previous commit, discarding this commit's log message.
- `drop`: Remove the commit entirely.

Example:
```text
pick e4a1b2c feat(auth): add login form markup
fixup a3f2c1d fix typo in placeholder
pick 9b1c7e4 feat(auth): add submit event handler
```
Save and close the file. Git will automatically clean the history!

---

## 4. Git Cherry-Pick: Applying a Single Commit

When you need a single bugfix or commit from one branch without merging the entire branch:

```bash
# Switch to destination branch
git switch main

# Apply commit 7c9a2d1 from another branch
git cherry-pick 7c9a2d1
```

---

## 5. Security: What To Do If You Accidentally Commit Secrets / Keys

1. **Never just make a new commit deleting the secret!** The secret remains visible forever in Git history.
2. Invalidate / revoke the API key or token immediately in your cloud provider dashboard.
3. Remove the secret from Git history using tools like `git-filter-repo` or BFG Repo-Cleaner.
4. Add the secret pattern to `.gitignore`.
