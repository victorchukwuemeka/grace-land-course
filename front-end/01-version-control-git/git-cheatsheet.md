# 📋 Git Quick Reference Cheatsheet

Keep this cheatsheet handy for day-to-day frontend development.

---

## ⚙️ Configuration & Setup
```bash
git config --global user.name "Your Name"           # Set commit author name
git config --global user.email "you@example.com"    # Set commit author email
git config --global init.defaultBranch main        # Default new repos to 'main'
git config --list                                  # List all current config values
```

---

## 📁 Repository Initialization & Staging
```bash
git init                                           # Initialize local git repository
git clone <url>                                    # Clone remote repository
git status                                         # Check working directory and index
git add <file>                                     # Stage specific file
git add .                                          # Stage all modifications & new files
git restore --staged <file>                        # Unstage file (keep working edits)
git restore <file>                                 # Discard unstaged working tree changes
```

---

## 💾 Committing & History
```bash
git commit -m "feat(scope): concise description"   # Commit staged changes
git commit --amend -m "Updated commit message"     # Amend last commit message
git log --oneline --graph --decorate --all         # Visual one-line commit history
git diff                                           # View unstaged changes
git diff --staged                                  # View staged changes
git show <hash>                                    # View commit details and patch
```

---

## 🌿 Branching & Merging
```bash
git branch                                         # List local branches
git branch -a                                      # List local and remote branches
git switch -c <new-branch>                         # Create & switch to new branch
git switch <branch>                                # Switch to existing branch
git merge <feature-branch>                         # Merge feature branch into current
git merge --abort                                  # Abort a conflicted merge
git branch -d <branch>                             # Safely delete merged branch
git branch -D <branch>                             # Force delete branch
```

---

## 🌐 Remote Synchronization
```bash
git remote add origin <url>                        # Add remote tracking repository
git remote -v                                      # View configured remotes
git push -u origin <branch>                        # Push branch & set upstream
git push                                           # Push to upstream branch
git fetch origin                                   # Download remote commits without merge
git pull origin <branch>                           # Fetch and merge remote changes
```

---

## 🧰 Stashing & Undoing
```bash
git stash push -m "WIP message"                    # Shelve uncommitted changes
git stash list                                     # List saved stashes
git stash pop                                      # Apply latest stash and delete from stack
git stash drop                                     # Discard latest stash without applying
git reset --soft HEAD~1                            # Undo last commit; keep staged
git reset --mixed HEAD~1                           # Undo last commit; unstage files
git reset --hard HEAD~1                            # DESTRUCTIVE: Undo last commit & discard edits
git revert <hash>                                  # Create new commit reversing target commit
```
