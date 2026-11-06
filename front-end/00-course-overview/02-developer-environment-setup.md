# Module 00: Developer Environment & Tooling Setup

A professional development environment reduces friction, catches errors early, and accelerates your workflow. Below is the exact setup used by industry professionals.

---

## 1. Operating System & Terminal Essentials

### Recommended Terminals:
- **macOS**: Built-in Terminal or [iTerm2](https://iterm2.com/) with Zsh.
- **Linux**: Standard bash or zsh (Ubuntu/Debian/Fedora).
- **Windows**: [Windows Terminal](https://aka.ms/terminal) running **WSL 2 (Windows Subsystem for Linux - Ubuntu)**. *Avoid Git Bash or Command Prompt for serious engineering workflows.*

### Fundamental Terminal Commands You Must Know:
```bash
# Print working directory
pwd

# List files (all files including hidden, with details)
ls -la

# Change directory
cd path/to/folder
cd ..        # Move up one directory level
cd ~         # Jump to user home directory

# Create new directory (use -p to create parent paths)
mkdir -p my-project/src

# Create empty file
touch index.html style.css app.js

# Remove files/folders (CAUTION: permanent delete)
rm filename.txt
rm -rf folder_name/

# View file contents in terminal
cat README.md
head -n 20 README.md
```

---

## 2. Code Editor: Visual Studio Code

Download and install [Visual Studio Code](https://code.visualstudio.com/).

### Essential VS Code Extensions:
1. **Live Server** (`ritwickdey.LiveServer`): Launches a local development server with live reload feature for static pages.
2. **Prettier - Code Formatter** (`esbenp.prettier-vscode`): Enforces consistent formatting across HTML, CSS, and JS.
3. **ESLint** (`dbaeumer.vscode-eslint`): Integrates static JavaScript linting to catch syntax errors and bad practices.
4. **GitLens** (`eamodio.gitlens`): Supercharges Git inside VS Code, showing inline blame annotations, commit history, and branch visualizers.
5. **Auto Rename Tag** (`formulahendry.auto-rename-tag`): Automatically renames paired HTML/XML tags.
6. **Path Intellisense** (`christian-kohler.path-intellisense`): Autocompletes filenames in imports and asset links.

### Recommended VS Code Settings (`settings.json`):
Open your command palette (`Ctrl+Shift+P` / `Cmd+Shift+P`), type `Open User Settings (JSON)`, and add:

```json
{
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.wordWrap": "on",
  "files.autoSave": "onFocusChange",
  "emmet.includeLanguages": {
    "javascript": "javascriptreact"
  },
  "editor.linkedEditing": true,
  "terminal.integrated.defaultProfile.linux": "bash"
}
```

---

## 3. Web Browser & DevTools Setup

Install **Google Chrome** or **Firefox Developer Edition**.

### Key DevTools Shortcuts:
- **Inspect Element**: `Ctrl+Shift+C` (Linux/Win) or `Cmd+Shift+C` (Mac).
- **Open DevTools**: `F12` or `Ctrl+Shift+I` / `Cmd+Option+I`.
- **Toggle Device Toolbar (Responsive View)**: `Ctrl+Shift+M` / `Cmd+Shift+M`.
- **Hard Refresh (bypassing cache)**: `Ctrl+F5` or `Ctrl+Shift+R` / `Cmd+Shift+R`.

### DevTools Tabs to Master:
- **Elements / Inspector**: Inspect the live DOM tree, manipulate CSS styles dynamically, test box model margins/paddings, and simulate hover/focus states.
- **Console**: Log errors, run arbitrary JavaScript, test expressions, view warnings.
- **Network**: Monitor all HTTP requests, inspect file size, check response headers, simulate slow 3G/4G network throttling.
- **Sources / Debugger**: Set breakpoints in JavaScript, step through code line-by-line, and inspect variable values in real-time.
- **Application / Storage**: Inspect LocalStorage, SessionStorage, Cookies, and IndexedDB.

---

## 4. Node.js & Package Managers (For Local Tooling)

Even though this is a vanilla frontend course, modern developers use Node.js to run local dev servers, linters, and formatters:
```bash
# Verify installation
node -v
npm -v
```
*(If not installed, install the LTS release from [nodejs.org](https://nodejs.org/) or via your system's package manager).*
