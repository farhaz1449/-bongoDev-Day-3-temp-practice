# The Definitive Git & GitHub Handbook

A comprehensive, production-ready guide to version control with **Git** and collaboration on **GitHub**, covering essential workflows, advanced commands, branching strategies, conflict resolution, and best practices.

---

## Table of Contents

1. [Understanding Git vs. GitHub](#1-understanding-git-vs-github)
2. [Git Core Architecture](#2-git-core-architecture)
3. [Initial Setup & Configuration](#3-initial-setup--configuration)
4. [Basic Workflow & Essential Commands](#4-basic-workflow--essential-commands)
5. [Branching & Merging Strategies](#5-branching--merging-strategies)
6. [Working with Remote Repositories (GitHub)](#6-working-with-remote-repositories-github)
7. [GitHub Collaboration & Workflows](#7-github-collaboration--workflows)
8. [Advanced Git Techniques & Recovery](#8-advanced-git-techniques--recovery)
9. [Git Best Practices & Conventional Commits](#9-git-best-practices--conventional-commits)
10. [Quick Reference Cheat Sheet](#10-quick-reference-cheat-sheet)

---

## 1. Understanding Git vs. GitHub

| Feature | Git | GitHub |
| :--- | :--- | :--- |
| **Type** | Distributed Version Control System (VCS) | Cloud-based hosting platform for Git repositories |
| **Location** | Runs locally on your machine | Hosted on remote cloud servers |
| **Internet Requirement** | Fully functional offline | Requires network connection |
| **Primary Function** | Track file history, manage branches, merge code | Remote backup, code review, CI/CD, issue tracking, collaboration |
| **Interface** | Command Line Interface (CLI) & GUI clients | Web UI, GitHub Desktop, GitHub CLI (`gh`), REST/GraphQL APIs |

---

## 2. Git Core Architecture

Git operates across **four distinct zones**:

```
 [ Working Directory ] ──(git add)──> [ Staging Area (Index) ]
          │                                      │
     (git checkout /                             │ (git commit)
      git restore)                               ▼
          │                             [ Local Repository (.git) ]
          │                                      │
          └───────────(git push / pull)──────────▼
                                        [ Remote Repository (GitHub) ]
```

1. **Working Directory (Workspace):** Local sandbox where you edit, add, and delete files.
2. **Staging Area (Index):** Intermediate holding area containing snapshots of changes slated for the next commit.
3. **Local Repository:** The `.git` directory storing committed history and object database (blobs, trees, commits, tags).
4. **Remote Repository:** Centralized or distributed server copy (e.g., hosted on GitHub) used for team synchronization.

---

## 3. Initial Setup & Configuration

### Global User Identity
```bash
# Set your commit username and email (must match your GitHub email)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name to 'main'
git config --global init.defaultBranch main

# Set default text editor (e.g., VS Code, nano, vim)
git config --global core.editor "code --wait"

# Verify configuration
git config --list --show-origin
```

### SSH Key Setup for GitHub
Authenticating via SSH avoids typing passwords and tokens on every push.

```bash
# 1. Generate an Ed25519 SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# 2. Start ssh-agent in background
eval "$(ssh-agent -s)"

# 3. Add private key to agent
ssh-add ~/.ssh/id_ed25519

# 4. Copy public key to clipboard (Linux: xclip, Mac: pbcopy, Windows: clip)
cat ~/.ssh/id_ed25519.pub
```
*Go to **GitHub Settings → SSH and GPG keys → New SSH key**, and paste your public key.*

---

## 4. Basic Workflow & Essential Commands

### Initializing and Cloning
```bash
# Initialize a new local Git repository
git init my-project
cd my-project

# Clone an existing remote repository
git clone git@github.com:username/repository.git
```

### Inspecting and Staging Changes
```bash
# Check status of modified, staged, and untracked files
git status

# View unstaged line-by-line differences
git diff

# View differences in staged files (against last commit)
git diff --staged

# Stage specific files
git add path/to/file.py

# Stage all tracked and untracked modifications
git add .
```

### Committing Changes
```bash
# Commit staged changes with an inline message
git commit -m "feat: implement user authentication flow"

# Stage all tracked modified files and commit in one step
git commit -am "fix: resolve token expiration race condition"

# Amend the previous commit (message or newly staged files)
git commit --amend --no-edit
```

### Viewing History
```bash
# Standard commit history
git log

# Compact one-line graphical commit tree
git log --oneline --graph --decorate --all

# View commits modifying a specific file
git log -p path/to/file.py
```

---

## 5. Branching & Merging Strategies

### Branch Management
```bash
# List all local branches
git branch

# List local and remote tracking branches
git branch -a

# Create a new branch
git branch feature/payment-gateway

# Switch to a branch (Modern syntax)
git switch feature/payment-gateway

# Create and switch to a branch simultaneously
git switch -c feature/payment-gateway
# Legacy equivalent: git checkout -b feature/payment-gateway

# Rename current branch
git branch -m feature/billing-v2

# Delete merged branch
git branch -d feature/old-branch

# Force delete unmerged branch
git branch -D feature/abandoned-branch
```

### Merging vs. Rebasing

#### 1. Standard Merge (Creates a Merge Commit)
Preserves complete historical context of branch lifetimes.
```bash
git switch main
git merge feature/payment-gateway
```

#### 2. Fast-Forward Merge
Occurs automatically when the target branch has no diverging commits.
```bash
git merge --ff-only feature/payment-gateway
```

#### 3. Rebase (Linear History)
Replays commits from the current branch onto the tip of the target branch.
```bash
git switch feature/payment-gateway
git rebase main
```

#### Merge Conflict Resolution Workflow:
1. Identify conflicted files using `git status`.
2. Open files and resolve conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
3. Stage the resolved files: `git add <file>`.
4. Finalize:
   - For merge: `git commit -m "merge: resolve conflicts with main"`
   - For rebase: `git rebase --continue` (or abort anytime via `git rebase --abort`).

---

## 6. Working with Remote Repositories (GitHub)

```bash
# View configured remote names and URLs
git remote -v

# Link a local repository to a new GitHub remote
git remote add origin git@github.com:username/repository.git

# Push local branch and set upstream tracking
git push -u origin main

# Push subsequent commits on tracking branch
git push

# Fetch latest refs and objects without modifying working tree
git fetch origin

# Fetch and merge changes from remote into current branch
git pull origin main

# Pull with rebase instead of merge (avoids unnecessary merge commits)
git pull --rebase origin main
```

---

## 7. GitHub Collaboration & Workflows

### Standard Fork & Pull Request (PR) Workflow
1. **Fork** the target repository on GitHub to your account.
2. **Clone** your fork locally:
   ```bash
   git clone git@github.com:your-user/project.git
   ```
3. **Add Upstream Remote** to sync with original project:
   ```bash
   git remote add upstream git@github.com:original-owner/project.git
   ```
4. **Create Feature Branch**:
   ```bash
   git switch -c feat/add-search-indexer
   ```
5. **Develop, Commit, and Push**:
   ```bash
   git add .
   git commit -m "feat(search): add elasticsearch client"
   git push -u origin feat/add-search-indexer
   ```
6. **Open PR** on GitHub interface against `upstream:main`.
7. **Keep Fork Updated**:
   ```bash
   git fetch upstream
   git switch main
   git merge upstream/main
   ```

### Ignoring Files (`.gitignore`)
Place a `.gitignore` file in your repository root to prevent checking in unwanted files:

```gitignore
# Operating System files
.DS_Store
Thumbs.db

# Dependencies
node_modules/
vendor/
venv/
__pycache__/

# Environment & Secrets
.env
.env.local
*.pem
*.key

# Build outputs
dist/
build/
*.log
```

---

## 8. Advanced Git Techniques & Recovery

### 1. Stashing Temporary Work
```bash
# Save uncommitted changes to stash stack
git stash push -m "wip: halfway through refactoring"

# List stashes
git stash list

# Re-apply the latest stash and remove it from stack
git stash pop

# Apply stash without removing from stack
git stash apply stash@{0}

# Clear all stashes
git stash clear
```

### 2. Undoing & Reverting Changes
```bash
# Unstage a file keeping local edits
git restore --staged path/to/file.py

# Discard local unstaged changes to a file
git restore path/to/file.py

# Create a new commit that safely inverts a previous commit
git revert <commit_hash>

# Soft Reset: Move HEAD back, keep changes staged
git reset --soft HEAD~1

# Mixed Reset (Default): Move HEAD back, keep changes unstaged
git reset HEAD~1

# Hard Reset: DESTROY uncommitted changes and revert to state of commit
git reset --hard HEAD~1
```

### 3. Emergency Recovery (`git reflog`)
Git records every movement of `HEAD` in the reference log. Even after accidental `git reset --hard` or deleted branches, commits can be recovered.

```bash
# Inspect all recent HEAD movements
git reflog

# Restore branch back to a state prior to accidental reset
git reset --hard HEAD@{2}
```

### 4. Interactive Rebase (`git rebase -i`)
Clean up commit history before opening a Pull Request.

```bash
# Clean up the last 3 commits
git rebase -i HEAD~3
```
*Options in the interactive editor:*
- `pick` : Keep commit as is.
- `reword` : Change commit message.
- `edit` : Stop and amend commit contents.
- `squash` : Meld commit into previous commit.
- `drop` : Remove commit completely.

### 5. Cherry-Picking
```bash
# Apply a specific commit from another branch into current branch
git cherry-pick <commit_hash>
```

---

## 9. Git Best Practices & Conventional Commits

### Commit Message Standards
Follow the **Conventional Commits** specification:

```
<type>(<optional scope>): <subject>

[optional body]

[optional footer(s)]
```

#### Common Types:
- `feat`: A new feature for the user
- `fix`: A bug fix
- `docs`: Documentation changes only
- `style`: Formatting, missing semi-colons, white-space
- `refactor`: Code restructuring without changing behavior
- `perf`: Code change that improves performance
- `test`: Adding or correcting tests
- `chore`: Build process, dependencies, tooling updates

#### Example:
```text
feat(auth): add OAuth2 login via GitHub

- Implement authorization code flow with GitHub provider
- Store encrypted refresh tokens in database
- Add unit tests for auth callback handlers

Closes #142
```

---

## 10. Quick Reference Cheat Sheet

| Task | Command |
| :--- | :--- |
| **Check repository status** | `git status` |
| **Stage changes** | `git add <file>` or `git add .` |
| **Commit staged changes** | `git commit -m "msg"` |
| **Create & switch branch** | `git switch -c <branch-name>` |
| **Switch branch** | `git switch <branch-name>` |
| **Merge branch** | `git merge <branch-name>` |
| **Pull latest changes** | `git pull --rebase origin <branch>` |
| **Push to remote** | `git push -u origin <branch>` |
| **Save work temporarily** | `git stash` / `git stash pop` |
| **View graphical log** | `git log --oneline --graph --all` |
| **Unstage file** | `git restore --staged <file>` |
| **Safely undo commit** | `git revert <commit-hash>` |
| **Find lost commits** | `git reflog` |