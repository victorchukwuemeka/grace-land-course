# Module 01: GitHub, Remote Repositories & Team Collaboration

While Git manages your local repository, **GitHub** (or GitLab / Bitbucket) is a cloud-based hosting platform that enables code sharing, team peer reviews, automated CI/CD pipelines, and project tracking.

---

## 1. Setting Up SSH Authentication (Recommended)

SSH keys provide a secure, passwordless authentication method between your terminal and GitHub.

### Step 1: Generate an ED25519 SSH Key
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# Press Enter to accept the default file location (~/.ssh/id_ed25519)
# Optional: Enter a passphrase for extra security
```

### Step 2: Copy Your Public Key
```bash
cat ~/.ssh/id_ed25519.pub
```
Copy the full output string starting with `ssh-ed25519 ...`.

### Step 3: Add the Key to GitHub
1. Go to **GitHub $\rightarrow$ Settings $\rightarrow$ SSH and GPG keys**.
2. Click **New SSH Key**.
3. Set Title (e.g. `MacBook Pro Linux`) and paste the key.
4. Verify connection:
```bash
ssh -T git@github.com
# Should output: Hi username! You've successfully authenticated...
```

---

## 2. Connecting a Local Repo to GitHub

```bash
# Add a remote named 'origin'
git remote add origin git@github.com:yourusername/frontend-course.git

# Verify remote configuration
git remote -v

# Push your main branch and set upstream tracking (-u)
git push -u origin main
```
- `-u` (or `--set-upstream`) links your local `main` branch to `origin/main`. Future pushes and pulls can simply be `git push` or `git pull`.

---

## 3. Remote Synchronization: `git fetch` vs `git pull`

It is essential to understand the difference between fetching and pulling:

```
                  ┌──────────────┐
                  │ Remote Repo  │
                  │ (GitHub)     │
                  └──────┬───────┘
                         │
        git fetch        │   git pull (fetch + merge)
  (downloads new commits │  (downloads AND merges into
   into remote-tracking) │   your working branch)
                         ▼
                  ┌──────────────┐
                  │ Local Repo   │
                  │ (origin/main)│
                  └──────┬───────┘
                         │
                         │   git merge origin/main
                         ▼
                  ┌──────────────┐
                  │ Working Copy │
                  │    (main)    │
                  └──────────────┘
```

- **`git fetch origin`**: Safely downloads all commits, branches, and tags from the remote. It does **not** alter your working directory or local branches.
- **`git pull origin main`**: Equivalent to running `git fetch origin` followed immediately by `git merge origin/main`.

---

## 4. The Pull Request (PR) Workflow

In professional engineering environments, developers **never push directly to `main`**. All work is submitted via Pull Requests.

```
1. Clone / Pull Latest Main
        │
2. git switch -c feature/newsletter-signup
        │
3. Write Code + Tests + Commits
        │
4. git push -u origin feature/newsletter-signup
        │
5. Open PR on GitHub -> Peer Review -> CI Checks
        │
6. Merge PR into main -> Deploy to Staging/Prod
```

### Anatomy of a Great Pull Request:
1. **Clear Title**: e.g. `feat(newsletter): add email validation and submit feedback`
2. **Context / Problem Description**: Why is this change necessary?
3. **Screenshots / GIFs**: Before and after visuals for UI changes.
4. **Testing Instructions**: Step-by-step instructions for the reviewer to test the behavior locally.

### PR Merge Strategies:
- **Create a merge commit**: Retains all individual commits and creates an explicit merge commit on `main`.
- **Squash and merge**: Condenses all 10 feature commits into a single clean commit on `main`. (Most common in modern agile teams).
- **Rebase and merge**: Replays individual commits linearly onto `main` without a merge commit.

---

## 5. Forking vs Cloning

- **Clone**: Downloading a copy of a repository you have direct push rights to.
- **Fork**: Creating a server-side copy of an open-source repository under your own personal GitHub account. You push to your fork, then submit a cross-repository Pull Request back to the original ("upstream") repository.
