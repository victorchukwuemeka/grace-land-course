# 🧪 Git Hands-on Lab: Real-World Simulation Exercise

Practice makes permanent. Follow this step-by-step terminal lab to practice the exact workflow used on professional engineering teams.

---

## 🎯 Lab Objectives:
1. Initialize a new repository and configure `.gitignore`.
2. Commit initial files using Conventional Commits.
3. Create a feature branch and work on a new component.
4. Simulate a conflicting commit on `main`.
5. Perform a merge, resolve the conflict manually, and verify the commit graph.

---

## 📝 Step-by-Step Instructions

### Step 1: Create a Sandbox Directory
Open your terminal and run:
```bash
# Navigate to a temporary practice area
cd /tmp
mkdir git-practice-lab
cd git-practice-lab

# Initialize Git
git init
```

### Step 2: Create Project Files and `.gitignore`
```bash
# Create .gitignore
cat << 'EOF' > .gitignore
node_modules/
.DS_Store
*.log
EOF

# Create initial HTML file
cat << 'EOF' > index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Practice Lab</title>
</head>
<body>
  <h1>Welcome to Our Store</h1>
  <p>Coming soon!</p>
</body>
</html>
EOF

# Check status
git status
```

### Step 3: Stage and Commit Initial Code
```bash
git add .
git commit -m "feat: initial project setup with homepage"
```

### Step 4: Create and Switch to a Feature Branch
```bash
git switch -c feature/header-update

# Update index.html heading
sed -i 's/<h1>Welcome to Our Store<\/h1>/<h1>Welcome to the Ultimate Digital Store<\/h1>/' index.html

# Stage and commit
git add index.html
git commit -m "feat(header): update store headline for branding"
```

### Step 5: Make a Conflicting Commit on `main`
```bash
# Switch back to main
git switch main

# Change the EXACT same line differently on main
sed -i 's/<h1>Welcome to Our Store<\/h1>/<h1>Welcome to Tech Superstore 2026<\/h1>/' index.html

# Commit on main
git add index.html
git commit -m "feat(header): update headline to 2026 edition"
```

### Step 6: Trigger the Merge Conflict
```bash
git merge feature/header-update
```
Notice Git outputs:
```text
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### Step 7: Resolve the Conflict
Open `index.html` in your editor (`cat index.html` to view). You will see:
```html
<<<<<<< HEAD
  <h1>Welcome to Tech Superstore 2026</h1>
=======
  <h1>Welcome to the Ultimate Digital Store</h1>
>>>>>>> feature/header-update
```

Edit `index.html` to combine both intentions:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Practice Lab</title>
</head>
<body>
  <h1>Welcome to the Ultimate Tech Superstore 2026</h1>
  <p>Coming soon!</p>
</body>
</html>
```

### Step 8: Stage and Complete the Merge
```bash
git add index.html
git commit -m "fix(merge): resolve header branding conflict between main and feature branch"
```

### Step 9: Inspect the Resulting Commit Tree
```bash
git log --oneline --graph --all
```
You should see a beautiful branched graph where the feature branch diverged and cleanly merged back into `main`!

### Step 10: Clean Up
```bash
# Delete the merged feature branch
git branch -d feature/header-update

# Leave the temp directory
cd ..
rm -rf git-practice-lab
```
Congratulations! You just experienced and mastered the fundamental collaborative Git lifecycle!
