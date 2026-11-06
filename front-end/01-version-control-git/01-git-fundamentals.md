# Module 01: Git Fundamentals & The Core Architecture

Version control is the bedrock of modern software engineering. It allows teams to track changes, collaborate without overwriting each other's code, safely experiment with new features, and revert bugs in production.

---

## 1. What is Git? (Centralized vs Distributed)

Earlier version control systems (like SVN or CVS) were **centralized**: a single central server held all code history. If the server crashed or you lost network access, you could not commit changes or inspect logs.

**Git is a Distributed Version Control System (DVCS)** created by Linus Torvalds in 2005.
- Every developer clones a **full copy** of the repository, including its entire historical commit graph, branches, and tags.
- Most operations are instantaneous and run locally offline.
- Cryptographic hashing (SHA-1 / SHA-256) guarantees integrity: not a single byte or commit can be altered without altering the entire downstream history.

---

## 2. Git's Three States & Mental Model

Understanding Git requires understanding its **three zones**:

```
 ┌──────────────────────┐
 │  Working Directory   │  (The files you see, create, and edit on your disk)
 └──────────┬───────────┘
            │
            │  git add <file>
            ▼
 ┌──────────────────────┐
 │ Staging Area (Index) │  (The draft snapshot ready for the next commit)
 └──────────┬───────────┘
            │
            │  git commit -m "message"
            ▼
 ┌──────────────────────┐
 │ Git Directory (.git) │  (The permanent database of committed snapshots)
 └──────────────────────┘
```

### The Three States:
1. **Modified**: You changed a file in your working directory, but have not marked it for storage.
2. **Staged**: You marked a modified file in its current version to go into your next commit snapshot (`git add`).
3. **Committed**: The snapshot of files is safely recorded into the local `.git` repository database.

---

## 3. Initial Configuration

Before making your first commit, configure your identity. Git attaches this information to every commit you make:

```bash
# 1. Set your name and email
git config --global user.name "Jane Developer"
git config --global user.email "jane@example.com"

# 2. Set default initial branch name to main
git config --global init.defaultBranch main

# 3. Configure line ending handling
# Linux/macOS:
git config --global core.autocrlf input
# Windows:
# git config --global core.autocrlf true

# 4. Set default editor (VS Code)
git config --global core.editor "code --wait"

# 5. Verify your configuration
git config --list --show-origin
```

---

## 4. Initializing a Repository

To place a project under Git version control:

```bash
# Navigate to your project folder
cd /path/to/my-frontend-project

# Initialize a new Git repository
git init
```
This creates a hidden directory named `.git/`. This folder contains Git's internal object database, commit history, configuration, and references (`HEAD`, branches). **Never manually delete or alter files inside `.git/` unless you know what you are doing.**

---

## 5. Tracking Files & Staging Changes

### Checking Status
The most frequent command you will run:
```bash
git status
```
It tells you:
- Which branch you are on.
- Which files are untracked.
- Which files are modified but unstaged.
- Which files are staged and ready to commit.

### Adding Files to the Staging Area
```bash
# Stage a specific file
git add index.html

# Stage multiple specific files
git add style.css app.js

# Stage all modified and new files in the current directory and subdirectories
git add .
```

### Unstaging a File
If you staged a file by mistake:
```bash
# Unstage a file while keeping your changes in the working directory
git restore --staged index.html
```

---

## 6. Making Commits & Commit Message Discipline

A commit is an immutable snapshot of your staged files at a specific moment in time.

```bash
git commit -m "Add accessible navigation bar and responsive header styles"
```

### The Conventional Commits Standard
Professional engineering teams use structured commit messages following the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<optional scope>): <short description in present tense>

[optional body explaining WHY the change was made]

[optional footer, e.g. Closes #142]
```

#### Common Types:
- `feat`: A new feature for the user (e.g., `feat(auth): add google sign-in button`).
- `fix`: A bug fix (e.g., `fix(nav): correct mobile drawer z-index overlap`).
- `docs`: Documentation-only changes (e.g., `docs(readme): add installation guide`).
- `style`: Formatting, semicolons, whitespace (no functional logic changes).
- `refactor`: Code restructuring without bug fixes or new features.
- `perf`: Performance improvement.
- `test`: Adding or correcting tests.
- `chore`: Updating build tools, dependencies, or `.gitignore`.

---

## 7. Inspecting History & Changes

```bash
# View full commit history
git log

# View compact one-line history with graph visualization
git log --oneline --graph --decorate --all

# View changes made in working directory (unstaged changes)
git diff

# View changes that are staged for the next commit
git diff --staged

# View details and diff of a specific commit
git show <commit-hash>
```

---

## 8. Ignoring Files with `.gitignore`

Not every file belongs in version control! Never commit:
- Build artifacts or generated files (`dist/`, `build/`).
- Package dependencies (`node_modules/`).
- Operating system files (`.DS_Store`, `Thumbs.db`).
- Secret environment variables or API keys (`.env`, `.env.local`).

Create a file named `.gitignore` in your project root:

```gitignore
# Dependencies
node_modules/
.pnp
.pnp.js

# Environment and Secrets (CRITICAL: never commit credentials)
.env
.env.local
.env.*.local
*.pem

# Production build outputs
dist/
build/
out/

# Editor and OS junk
.DS_Store
Thumbs.db
.vscode/*
!.vscode/settings.json
!.vscode/extensions.json
.idea/

# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*
```
