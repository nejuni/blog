--- 
title: "git"
description: "readme"
date: 2025-10-28T14:11:52Z
lastmod: 2025-10-28T14:11:52Z
tags: ["Repo"]
draft: false
---

# Git CLI Cheat Sheet for Termux (2025 Edition)

**Version**: Git 2.47+ (Latest as of 2025)  
**Platform**: Termux (Android Terminal Emulator)  
**Official Resources**: 
- Website: https://git-scm.com
- Documentation: https://git-scm.com/docs
- GitHub Repository: https://github.com/git/git

---

## Table of Contents
1. [Initial Setup](#initial-setup)
2. [Repository Basics](#repository-basics)
3. [Staging & Committing](#staging--committing)
4. [Branching & Merging](#branching--merging)
5. [Remote Operations](#remote-operations)
6. [Viewing History](#viewing-history)
7. [Undoing Changes](#undoing-changes)
8. [Stashing](#stashing)
9. [Advanced Operations](#advanced-operations)
10. [Git Configuration](#git-configuration)
11. [Useful Aliases](#useful-aliases)

---

## Initial Setup

### Configure Git (Required First Time)
```bash
# Set your username
git config --global user.name "Your Name"

# Set your email
git config --global user.email "your.email@example.com"

# Set default branch name to main
git config --global init.defaultBranch main

# Set default editor (nano is easier in Termux)
git config --global core.editor nano

# Enable color output
git config --global color.ui auto
```

**Example:**
```bash
git config --global user.name "John Doe"
git config --global user.email "john.doe@gmail.com"
```

### Verify Configuration
```bash
# View all configurations
git config --list

# View specific config
git config user.name
git config user.email
```

---

## Repository Basics

### Creating Repositories

**Initialize a new repository:**
```bash
git init
```
**Example:**
```bash
cd ~/my-project
git init
# Output: Initialized empty Git repository in /data/data/com.termux/files/home/my-project/.git/
```

**Clone an existing repository:**
```bash
git clone <repository-url>
git clone <repository-url> <directory-name>
```
**Example:**
```bash
# Clone to default directory
git clone https://github.com/username/repo-name.git

# Clone to specific directory
git clone https://github.com/username/repo-name.git my-repo

# Clone with depth (shallow clone, faster)
git clone --depth 1 https://github.com/username/repo-name.git
```

### Repository Status

```bash
# Show working tree status
git status

# Short status
git status -s

# Show status with branch info
git status -sb
```

**Example:**
```bash
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
  new-file.txt
```

---

## Staging & Committing

### Adding Files to Staging Area

```bash
# Add specific file
git add <filename>

# Add all files in current directory
git add .

# Add all files in repository
git add -A

# Add files interactively
git add -i

# Add only modified and deleted files (not new files)
git add -u

# Add files with pattern
git add *.txt
```

**Example:**
```bash
# Add single file
git add README.md

# Add all Python files
git add *.py

# Add all files
git add .
```

### Committing Changes

```bash
# Commit with message
git commit -m "Commit message"

# Commit with detailed message (opens editor)
git commit

# Add and commit in one step (only tracked files)
git commit -am "Commit message"

# Amend last commit
git commit --amend

# Amend last commit message
git commit --amend -m "New commit message"
```

**Example:**
```bash
# Standard commit
git add .
git commit -m "Add user authentication feature"

# Quick commit for tracked files
git commit -am "Fix typo in documentation"

# Amend last commit (add forgotten files)
git add forgotten-file.txt
git commit --amend --no-edit
```

---

## Branching & Merging

### Branch Management

```bash
# List all local branches
git branch

# List all branches (local and remote)
git branch -a

# Create new branch
git branch <branch-name>

# Create and switch to new branch
git checkout -b <branch-name>
# OR (newer syntax)
git switch -c <branch-name>

# Switch to existing branch
git checkout <branch-name>
# OR
git switch <branch-name>

# Rename current branch
git branch -m <new-name>

# Delete local branch
git branch -d <branch-name>

# Force delete branch
git branch -D <branch-name>

# Delete remote branch
git push origin --delete <branch-name>
```

**Example:**
```bash
# Create feature branch
git checkout -b feature/user-login

# Work on feature...
git add .
git commit -m "Implement login form"

# Switch back to main
git checkout main

# Delete merged branch
git branch -d feature/user-login
```

### Merging

```bash
# Merge branch into current branch
git merge <branch-name>

# Merge with no fast-forward (creates merge commit)
git merge --no-ff <branch-name>

# Abort merge in case of conflicts
git merge --abort

# Continue merge after resolving conflicts
git merge --continue
```

**Example:**
```bash
# Switch to main branch
git checkout main

# Merge feature branch
git merge feature/user-login

# If conflicts occur:
# 1. Edit conflicted files
# 2. Mark as resolved:
git add conflicted-file.txt
# 3. Complete merge:
git commit -m "Merge feature/user-login"
```

### Rebasing

```bash
# Rebase current branch onto another
git rebase <branch-name>

# Interactive rebase (last 3 commits)
git rebase -i HEAD~3

# Abort rebase
git rebase --abort

# Continue rebase after resolving conflicts
git rebase --continue
```

**Example:**
```bash
# Update feature branch with latest main
git checkout feature/new-feature
git rebase main

# Interactive rebase to squash commits
git rebase -i HEAD~3
# In editor, change 'pick' to 'squash' for commits to combine
```

---

## Remote Operations

### Managing Remotes

```bash
# List remote repositories
git remote -v

# Add remote repository
git remote add <name> <url>

# Remove remote
git remote remove <name>

# Rename remote
git remote rename <old-name> <new-name>

# Show remote info
git remote show origin
```

**Example:**
```bash
# Add GitHub remote
git remote add origin https://github.com/username/repo.git

# Add multiple remotes
git remote add upstream https://github.com/original/repo.git

# View remotes
git remote -v
# Output:
# origin    https://github.com/username/repo.git (fetch)
# origin    https://github.com/username/repo.git (push)
```

### Fetching & Pulling

```bash
# Fetch changes from remote
git fetch origin

# Fetch all remotes
git fetch --all

# Pull changes (fetch + merge)
git pull

# Pull with rebase instead of merge
git pull --rebase

# Pull specific branch
git pull origin <branch-name>
```

**Example:**
```bash
# Update local repository
git pull origin main

# Get latest changes without merging
git fetch origin
git log origin/main
```

### Pushing

```bash
# Push to remote
git push origin <branch-name>

# Push current branch to origin
git push

# Push and set upstream
git push -u origin <branch-name>

# Force push (dangerous!)
git push --force

# Safer force push
git push --force-with-lease

# Push all branches
git push --all

# Push tags
git push --tags
```

**Example:**
```bash
# First time pushing new branch
git checkout -b feature/new-feature
git commit -m "Add new feature"
git push -u origin feature/new-feature

# Subsequent pushes
git push

# Push to different branch name
git push origin local-branch:remote-branch
```

---

## Viewing History

### Log Commands

```bash
# View commit history
git log

# One line per commit
git log --oneline

# Show last N commits
git log -n 5

# Show with graph
git log --graph --oneline --all

# Show with statistics
git log --stat

# Show commits by author
git log --author="John Doe"

# Show commits in date range
git log --since="2 weeks ago"
git log --after="2024-01-01" --before="2024-12-31"

# Show commits affecting specific file
git log -- <filename>

# Pretty format
git log --pretty=format:"%h - %an, %ar : %s"
```

**Example:**
```bash
# Beautiful graph log
git log --graph --oneline --decorate --all

# Output:
# * a1b2c3d (HEAD -> main) Fix bug in login
# * d4e5f6g Add user profile
# * h7i8j9k Initial commit

# Search commits
git log --grep="bug fix"

# See who changed a file
git log --follow -- README.md
```

### Diff Commands

```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
# OR
git diff --cached

# Compare branches
git diff <branch1> <branch2>

# Compare specific file
git diff <filename>

# Compare commits
git diff <commit1> <commit2>
```

**Example:**
```bash
# See what changed in a file
git diff README.md

# Compare current branch with main
git diff main

# See changes about to be committed
git diff --staged
```

### Showing Content

```bash
# Show commit details
git show <commit-hash>

# Show file from specific commit
git show <commit-hash>:<filename>

# Show file from another branch
git show <branch-name>:<filename>
```

**Example:**
```bash
# Show last commit
git show HEAD

# Show specific commit
git show a1b2c3d

# Show file from 2 commits ago
git show HEAD~2:README.md
```

---

## Undoing Changes

### Unstaging Files

```bash
# Unstage specific file
git reset HEAD <filename>

# Unstage all files
git reset HEAD

# Using restore (newer command)
git restore --staged <filename>
```

**Example:**
```bash
# Accidentally staged wrong file
git add secret.txt
git restore --staged secret.txt
```

### Discarding Changes

```bash
# Discard changes in working directory
git checkout -- <filename>

# Using restore (newer command)
git restore <filename>

# Discard all changes
git restore .

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Dry run (see what would be deleted)
git clean -n
```

**Example:**
```bash
# Undo changes to a file
git restore README.md

# Remove all untracked files
git clean -fd
```

### Resetting Commits

```bash
# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset HEAD~1
# OR
git reset --mixed HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Reset to specific commit
git reset --hard <commit-hash>
```

**Example:**
```bash
# Undo last commit but keep work
git reset --soft HEAD~1
# Now you can modify and recommit

# Undo last 3 commits completely
git reset --hard HEAD~3
```

### Reverting Commits

```bash
# Create new commit that undoes a commit
git revert <commit-hash>

# Revert without creating commit immediately
git revert -n <commit-hash>

# Revert merge commit
git revert -m 1 <merge-commit-hash>
```

**Example:**
```bash
# Safely undo a pushed commit
git revert a1b2c3d
# Creates new commit that reverses a1b2c3d
```

---

## Stashing

### Stash Commands

```bash
# Stash current changes
git stash

# Stash with message
git stash save "Work in progress on feature X"

# Stash including untracked files
git stash -u

# List all stashes
git stash list

# Apply most recent stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Apply and remove stash
git stash pop

# Remove specific stash
git stash drop stash@{0}

# Clear all stashes
git stash clear

# Show stash contents
git stash show

# Show stash diff
git stash show -p
```

**Example:**
```bash
# Save current work
git stash save "Half-done login feature"

# Switch to another branch
git checkout hotfix

# Fix bug and commit...

# Return to feature
git checkout feature/login
git stash pop

# Working with multiple stashes
git stash list
# Output:
# stash@{0}: On feature/login: Half-done login feature
# stash@{1}: On main: WIP on main

git stash apply stash@{1}
```

---

## Advanced Operations

### Cherry-Picking

```bash
# Apply specific commit to current branch
git cherry-pick <commit-hash>

# Cherry-pick without committing
git cherry-pick -n <commit-hash>

# Cherry-pick multiple commits
git cherry-pick <commit1> <commit2>
```

**Example:**
```bash
# Copy a bug fix from another branch
git checkout main
git cherry-pick a1b2c3d
```

### Tagging

```bash
# List tags
git tag

# Create lightweight tag
git tag v1.0.0

# Create annotated tag
git tag -a v1.0.0 -m "Version 1.0.0 release"

# Tag specific commit
git tag -a v1.0.0 <commit-hash> -m "Version 1.0.0"

# Push tag to remote
git push origin v1.0.0

# Push all tags
git push --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Checkout tag
git checkout v1.0.0
```

**Example:**
```bash
# Release new version
git tag -a v2.1.0 -m "Release version 2.1.0"
git push origin v2.1.0

# List tags matching pattern
git tag -l "v1.*"
```

### Searching

```bash
# Search in repository
git grep "search term"

# Search in specific branch
git grep "search term" <branch-name>

# Search with line numbers
git grep -n "search term"

# Search for regex
git grep -e "pattern"

# Find which commit introduced a string
git log -S "search term"
```

**Example:**
```bash
# Find all TODO comments
git grep -n "TODO"

# Find function definition
git grep "def login"
```

### Submodules

```bash
# Add submodule
git submodule add <repository-url> <path>

# Initialize submodules
git submodule init

# Update submodules
git submodule update

# Clone with submodules
git clone --recursive <repository-url>

# Update all submodules to latest
git submodule update --remote
```

**Example:**
```bash
# Add library as submodule
git submodule add https://github.com/user/library.git libs/library

# Clone project with submodules
git clone --recursive https://github.com/user/project.git
```

---

## Git Configuration

### Configuration Levels

```bash
# System level (all users)
git config --system

# Global level (current user)
git config --global

# Local level (current repository)
git config --local

# List all config with locations
git config --list --show-origin
```

### Useful Configurations

```bash
# Set default editor
git config --global core.editor nano

# Set credential helper (cache passwords)
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'

# Set line ending handling
git config --global core.autocrlf input

# Enable color
git config --global color.ui auto

# Set default merge strategy
git config --global pull.rebase false

# Set default push behavior
git config --global push.default current

# Enable case sensitivity
git config --global core.ignorecase false

# Set diff tool
git config --global diff.tool vimdiff

# Set merge tool
git config --global merge.tool vimdiff
```

**Example:**
```bash
# Cache GitHub credentials for 1 hour
git config --global credential.helper 'cache --timeout=3600'

# Always rebase when pulling
git config --global pull.rebase true
```

### .gitignore File

```bash
# Create .gitignore file
nano .gitignore
```

**Example .gitignore:**
```
# OS files
.DS_Store
Thumbs.db

# Editor files
*.swp
*.swo
*~
.vscode/
.idea/

# Logs
*.log
logs/

# Dependencies
node_modules/
vendor/
__pycache__/
*.pyc

# Build output
dist/
build/
*.exe

# Environment files
.env
.env.local

# Credentials
secrets.txt
*.key
*.pem
```

---

## Useful Aliases

Add these to your Git configuration for faster workflow:

```bash
# Add aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --graph --oneline --all'
git config --global alias.amend 'commit --amend --no-edit'
git config --global alias.undo 'reset --soft HEAD~1'
```

**Usage after setting aliases:**
```bash
# Instead of: git checkout main
git co main

# Instead of: git status
git st

# Instead of: git commit
git ci -m "message"

# Instead of: git log --graph --oneline --all
git visual
```

---

## Common Workflows

### Feature Branch Workflow

```bash
# 1. Update main branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/new-feature

# 3. Make changes and commit
git add .
git commit -m "Implement new feature"

# 4. Push to remote
git push -u origin feature/new-feature

# 5. After review, merge to main
git checkout main
git merge feature/new-feature

# 6. Push to remote
git push origin main

# 7. Delete feature branch
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### Hotfix Workflow

```bash
# 1. Create hotfix branch from main
git checkout main
git checkout -b hotfix/critical-bug

# 2. Fix bug and commit
git add .
git commit -m "Fix critical bug"

# 3. Merge to main
git checkout main
git merge hotfix/critical-bug

# 4. Tag release
git tag -a v1.0.1 -m "Hotfix release"

# 5. Push everything
git push origin main --tags

# 6. Delete hotfix branch
git branch -d hotfix/critical-bug
```

### Syncing Fork

```bash
# 1. Add upstream remote (once)
git remote add upstream https://github.com/original/repo.git

# 2. Fetch upstream changes
git fetch upstream

# 3. Merge upstream/main into your main
git checkout main
git merge upstream/main

# 4. Push to your fork
git push origin main
```

---

## Troubleshooting

### Common Issues

**Detached HEAD state:**
```bash
# Create branch from current position
git checkout -b new-branch-name

# Or go back to a branch
git checkout main
```

**Merge conflicts:**
```bash
# 1. Check conflicted files
git status

# 2. Edit files and resolve conflicts
# Look for markers: <<<<<<<, =======, >>>>>>>

# 3. Mark as resolved
git add conflicted-file.txt

# 4. Complete merge
git commit
```

**Accidentally committed to wrong branch:**
```bash
# 1. Soft reset (keeps changes)
git reset --soft HEAD~1

# 2. Stash changes
git stash

# 3. Switch to correct branch
git checkout correct-branch

# 4. Apply stashed changes
git stash pop

# 5. Commit
git commit -m "message"
```

**Pushed wrong commit:**
```bash
# If no one has pulled yet
git reset --hard HEAD~1
git push --force-with-lease

# If others have pulled (safer)
git revert <commit-hash>
git push
```

---

## Quick Reference Commands

### Most Used Daily Commands

```bash
git status              # Check status
git add .              # Stage all changes
git commit -m "msg"    # Commit changes
git push               # Push to remote
git pull               # Pull from remote
git checkout -b name   # Create new branch
git checkout name      # Switch branch
git merge name         # Merge branch
git log --oneline      # View history
git diff               # See changes
```

### Git Status Symbols

```
M  = Modified
A  = Added
D  = Deleted
R  = Renamed
C  = Copied
U  = Updated but unmerged
?? = Untracked
!! = Ignored
```

---

## Additional Resources

- **Official Git Documentation**: https://git-scm.com/doc
- **Git Reference Manual**: https://git-scm.com/docs
- **Pro Git Book** (Free): https://git-scm.com/book
- **Git GitHub Repository**: https://github.com/git/git
- **Git Cheat Sheet PDF**: https://training.github.com/downloads/github-git-cheat-sheet.pdf

---

## Version Check

```bash
# Check Git version
git --version

# Update Git in Termux
pkg update
pkg upgrade git
```

---

**Document Version**: 2025.1  
**Last Updated**: October 2025  
**Git Version Referenced**: 2.47+

---

# Detailed Explanation with Examples

Now let me explain the most important concepts with practical examples:

## 1. Understanding Git Basics

Git is a distributed version control system. Think of it as a time machine for your code that lets you:
- Track changes over time
- Collaborate with others
- Experiment safely with branches
- Recover from mistakes

### The Three States

Git has three main states for your files:

1. **Working Directory**: Your actual files you're editing
2. **Staging Area (Index)**: Files prepared for commit
3. **Repository (.git directory)**: Committed snapshots of your project

**Visual Flow:**
```
Working Directory → (git add) → Staging Area → (git commit) → Repository
```

## 2. Starting a New Project

**Scenario**: You want to create a new Android app project and track it with Git.

```bash
# Navigate to your projects folder
cd ~/storage/shared/Projects

# Create new directory
mkdir MyApp
cd MyApp

# Initialize Git repository
git init

# Output: Initialized empty Git repository in /data/data/com.termux/files/home/storage/shared/Projects/MyApp/.git/

# Configure for this project
git config user.name "John Doe"
git config user.email "john@example.com"

# Create initial files
echo "# MyApp" > README.md
echo "node_modules/" > .gitignore
echo "*.log" >> .gitignore

# Stage all files
git add .

# Check what's staged
git status
# Output:
# On branch main
# Changes to be committed:
#   new file:   .gitignore
#   new file:   README.md

# Make first commit
git commit -m "Initial commit with README and gitignore"

# Output:
# [main (root-commit) a1b2c3d] Initial commit with README and gitignore
#  2 files changed, 3 insertions(+)
```

## 3. Daily Development Workflow

**Scenario**: You're working on adding a login feature.

```bash
# Check current status
git status

# Create feature branch
git checkout -b feature/login
# Output: Switched to a new branch 'feature/login'

# Create login file
nano login.js
# (write your code)

# Check what changed
git status
# Output:
# On branch feature/login
# Untracked files:
#   login.js

# Stage the new file
git add login.js

# Commit with descriptive message
git commit -m "Add login form with email validation"

# Continue working...
nano login.js
# (make more changes)

# See what changed since last commit
git diff login.js

# Stage and commit
git add login.js
git commit -m "Add password strength indicator"

# View your commits
git log --oneline
# Output:
# b2c3d4e Add password strength indicator
# a1b2c3d Add login form with email validation
```

## 4. Working with GitHub

**Scenario**: You want to push your project to GitHub.

```bash
# First, create repository on GitHub website
# Then add it as remote

git remote add origin https://github.com/yourusername/MyApp.git

# Verify remote
git remote -v
# Output:
# origin  https://github.com/yourusername/MyApp.git (fetch)
# origin  https://github.com/yourusername/MyApp.git (push)

# Push main branch to GitHub
git push -u origin main

# Push feature branch
git checkout feature/login
git push -u origin feature/login

# Now others can see your code!
```

## 5. Collaborating with Others

**Scenario**: Your teammate made changes to the main branch.

```bash
# Get latest changes
git checkout main
git pull origin main

# Output:
# From https://github.com/yourusername/MyApp
#  * branch            main       -> FETCH_HEAD
# Updating a1b2c3d..e5f6g7h
# Fast-forward
#  app.js | 10 +++++++---
#  1 file changed, 7 insertions(+), 3 deletions(-)

# Update your feature branch with latest main
git checkout feature/login
git merge main

# If conflicts occur, resolve them:
nano conflicted-file.js
# (fix conflicts between <<<< ==== >>>> markers)

git add conflicted-file.js
git commit -m "Merge main into feature/login"
```

## 6. Merging Feature Branch

**Scenario**: Your login feature is complete and tested.

```bash
# Switch to main branch
git checkout main

# Make sure main is up to date
git pull origin main

# Merge feature branch
git merge feature/login

# Output:
# Updating e5f6g7h..j8k9l0m
# Fast-forward
#  login.js | 145 ++++++++++++++++++++++++++++++++++++++
#  1 file changed, 145 insertions(+)

# Push to GitHub
git push origin main

# Delete feature branch locally
git branch -d feature/login

# Delete from GitHub
git push origin --delete feature/login
```

## 7. Fixing Mistakes

### Mistake 1: Wrong commit message

```bash
# Last commit had typo in message
git commit --amend -m "Add login feature with validation (fixed typo)"
```

### Mistake 2: Forgot to add file

```bash
# Made commit but forgot to add a file
git add forgotten-file.js
git commit --amend --no-edit
```

### Mistake 3: Want to undo last commit

```bash
# Keep changes but undo commit
git reset --soft HEAD~1

# Now you can modify and recommit
git add .
git commit -m "Better commit message"
```

### Mistake 4: Committed to wrong branch

```bash
# Oh no! Committed to main instead of feature branch
# 1. Create new branch (keeps the commit)
git branch feature/new-feature

# 2. Reset main to previous state
git reset --hard HEAD~1

# 3. Switch to new branch
git checkout feature/new-feature

# Now the commit is on the correct branch!
```

## 8. Using Stash

**Scenario**: You're working on a feature but need to quickly fix a bug on main.

```bash
# Currently on feature/dashboard with uncommitted changes
git status
# Output: Modified: dashboard.js

# Stash your work
git stash save "Dashboard work in progress"

# Switch to main
git checkout main

# Fix bug
nano bugfix.js
git add bugfix.js
git commit -m "Fix critical bug in authentication"

# Push fix
git push origin main

# Go back to feature
git checkout feature/dashboard

# Restore your work
git stash pop

# Continue working...
```

## 9. Viewing History

**Scenario**: You need to find when a bug was introduced.

```bash
# View recent commits
git log --oneline -10

# Search commits
git log --grep="authentication"

# See what changed in a commit
git show a1b2c3d

# Find who changed a line
git blame login.js

# See file history
git log --follow -- login.js

# Compare two commits
git diff a1b2c3d..e5f6g7h

# Beautiful graph of branches
git log --graph --oneline --all --decorate
```

## 10. Advanced: Interactive Rebase

**Scenario**: You made 5 small commits but want to combine them into 1 before merging.

```bash
# View last 5 commits
git log --oneline -5
# Output:
# e5e5e5e Fix typo
# d4d4d4d Add comments
# c3c3c3c Update logic
# b2b2b2b Add feature
# a1a1a1a Initial implementation

# Start interactive rebase
git rebase -i HEAD~5

# Editor opens with:
# pick a1a1a1a Initial implementation
# pick b2b2b2b Add feature
# pick c3c3c3c Update logic
# pick d4d4d4d Add comments
# pick e5e5e5e Fix typo

# Change to:
# pick a1a1a1a Initial implementation
# squash b2b2b2b Add feature
# squash c3c3c3c Update logic
# squash d4d4d4d Add comments
# squash e5e5e5e Fix typo

# Save and close editor
# New editor opens for combined commit message
# Write: "Implement complete login feature"

# Now you have 1 clean commit instead of 5!
```

---

## Termux-Specific Tips

1. **Storage Access**: If working with files in phone storage:
```bash
termux-setup-storage
cd ~/storage/shared
```

2. **SSH Keys for GitHub** (avoid password every time):
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings → SSH and GPG keys → New SSH key
# Then use SSH URLs:
git remote set-url origin git@github.com:username/repo.git
```

3. **Credential Helper** (cache HTTPS passwords):
```bash
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=7200'
```

---

## Best Practices

1. **Commit Often**: Small, focused commits are better than large ones
2. **Write Good Messages**: Explain why, not just what
3. **Pull Before Push**: Always get latest changes first
4. **Branch for Features**: Keep main branch stable
5. **Review Before Commit**: Use `git diff` and `git status`
6. **Don't Commit Secrets**: Use `.gitignore` for API keys, passwords
7. **Tag Releases**: Use semantic versioning (v1.0.0, v1.1.0, etc.)

---

## Quick Troubleshooting

**"fatal: not a git repository"**
```bash
# You're not in a Git repository
# Either initialize: git init
# Or navigate to your project: cd ~/myproject
```

**"fatal: refusing to merge unrelated histories"**
```bash
# When pulling from a remote for first time:
git pull origin main --allow-unrelated-histories
```

**"Your branch is ahead of 'origin/main' by X commits"**
```bash
# You have unpushed commits:
git push origin main
```

**"Your branch is behind 'origin/main' by X commits"**
```bash
# Remote has newer commits:
git pull origin main
```

---

This comprehensive guide covers everything you need for daily Git usage in Termux. Bookmark it and refer back as needed!

**Sources:**
- Git Official Documentation: https://git-scm.com/docs
- Git GitHub Repository: https://github.com/git/git
- Git Reference Manual: https://git-scm.com/docs/git