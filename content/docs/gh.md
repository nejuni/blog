--- 
title: "gh"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:10:52Z
tags: ["Repo"]
draft: false
---

# GitHub CLI (gh) - Complete Guide for Daily Use
*Updated for 2025 | For Termux Users*

## Table of Contents
1. [Initial Setup](#initial-setup)
2. [Authentication](#authentication)
3. [Repository Commands](#repository-commands)
4. [Issue Management](#issue-management)
5. [Pull Requests](#pull-requests)
6. [GitHub Actions & Workflows](#github-actions--workflows)
7. [Gist Management](#gist-management)
8. [Release Management](#release-management)
9. [GitHub Copilot (CLI)](#github-copilot-cli)
10. [Advanced Tips & Tricks](#advanced-tips--tricks)

---

## Initial Setup

### Check Installation
```bash
gh --version
```

### View Help
```bash
gh help
gh <command> --help
gh <command> <subcommand> --help
```

---

## Authentication

### Login to GitHub
```bash
# Interactive login (recommended)
gh auth login

# Login with token
gh auth login --with-token < token.txt

# Login to GitHub Enterprise
gh auth login --hostname github.enterprise.com
```

### Check Authentication Status
```bash
gh auth status

# Check for specific hostname
gh auth status --hostname github.com
```

### Switch Accounts
```bash
gh auth switch

# Switch to specific account
gh auth switch --user username
```

### Logout
```bash
gh auth logout

# Logout from specific hostname
gh auth logout --hostname github.com
```

### Get Authentication Token
```bash
gh auth token
```

---

## Repository Commands

### Create Repository

#### Create a New Repository
```bash
# Create public repository
gh repo create my-project --public

# Create private repository
gh repo create my-project --private

# Create with description
gh repo create my-project --description "My awesome project" --public

# Create from current directory
gh repo create --source=. --private --push

# Create with specific gitignore and license
gh repo create my-project --gitignore Node --license MIT --public
```

#### Interactive Creation
```bash
gh repo create
# Follow the prompts
```

### Clone Repository
```bash
# Clone your repository
gh repo clone username/repo-name

# Clone to specific directory
gh repo clone username/repo-name target-directory

# Clone with all submodules
gh repo clone username/repo-name -- --recurse-submodules
```

### Fork Repository
```bash
# Fork a repository
gh repo fork owner/repo-name

# Fork and clone
gh repo fork owner/repo-name --clone

# Fork with custom name
gh repo fork owner/repo-name --fork-name my-fork
```

### View Repository
```bash
# View repository in browser
gh repo view

# View specific repository
gh repo view owner/repo-name

# View in terminal (README)
gh repo view owner/repo-name --web=false

# View in browser
gh repo view owner/repo-name --web
```

### Repository Information
```bash
# List your repositories
gh repo list

# List organization repositories
gh repo list organization-name

# List with filters
gh repo list --limit 50 --language python --public

# List archived repositories
gh repo list --archived
```

### Repository Settings
```bash
# Archive repository
gh repo archive owner/repo-name

# Unarchive repository
gh repo unarchive owner/repo-name

# Delete repository (careful!)
gh repo delete owner/repo-name --yes

# Rename repository
gh repo rename new-name

# Edit repository settings
gh repo edit --description "New description" --homepage "https://example.com"

# Enable/disable features
gh repo edit --enable-wiki
gh repo edit --disable-issues
gh repo edit --enable-projects
```

### Sync Fork
```bash
# Sync your fork with upstream
gh repo sync owner/repo-name

# Sync current repository
gh repo sync
```

---

## Issue Management

### Create Issues
```bash
# Interactive issue creation
gh issue create

# Create with title and body
gh issue create --title "Bug: Something broke" --body "Description here"

# Create from file
gh issue create --title "Feature Request" --body-file issue.md

# Assign issue to yourself
gh issue create --title "New task" --assignee @me

# Create with labels
gh issue create --title "Bug report" --label bug,urgent

# Create with milestone
gh issue create --title "Task" --milestone "v1.0"

# Assign to specific user
gh issue create --title "Task" --assignee username

# Create with project
gh issue create --title "Feature" --project "Roadmap"
```

### List Issues
```bash
# List open issues
gh issue list

# List all issues (including closed)
gh issue list --state all

# List with filters
gh issue list --label bug --assignee @me
gh issue list --author username
gh issue list --mention @me

# List with limit
gh issue list --limit 50

# Search issues
gh issue list --search "bug in:title"

# List by state
gh issue list --state closed
```

### View Issue
```bash
# View issue in browser
gh issue view 123

# View in terminal
gh issue view 123 --web=false

# View with comments
gh issue view 123 --comments
```

### Edit Issue
```bash
# Edit issue interactively
gh issue edit 123

# Update title
gh issue edit 123 --title "New title"

# Add/remove labels
gh issue edit 123 --add-label "bug,urgent"
gh issue edit 123 --remove-label "wontfix"

# Change assignee
gh issue edit 123 --add-assignee username
gh issue edit 123 --remove-assignee username

# Update milestone
gh issue edit 123 --milestone "v2.0"

# Update body
gh issue edit 123 --body "Updated description"
```

### Comment on Issues
```bash
# Add comment
gh issue comment 123 --body "This is a comment"

# Add comment from file
gh issue comment 123 --body-file comment.md

# Edit your last comment
gh issue comment 123 --edit-last
```

### Close/Reopen Issues
```bash
# Close issue
gh issue close 123

# Close with comment
gh issue close 123 --comment "Fixed in #124"

# Close as completed
gh issue close 123 --reason completed

# Close as not planned
gh issue close 123 --reason "not planned"

# Reopen issue
gh issue reopen 123

# Reopen with comment
gh issue reopen 123 --comment "Need to revisit this"
```

### Pin/Unpin Issues
```bash
# Pin issue
gh issue pin 123

# Unpin issue
gh issue unpin 123
```

### Transfer Issue
```bash
# Transfer to another repository
gh issue transfer 123 owner/destination-repo
```

### Issue Status
```bash
# Check issue status
gh issue status

# View issues assigned to you
gh issue status --assignee @me
```

---

## Pull Requests

### Create Pull Request
```bash
# Interactive PR creation
gh pr create

# Create with title and body
gh pr create --title "Add new feature" --body "Description"

# Create from file
gh pr create --title "Feature" --body-file pr.md

# Create draft PR
gh pr create --draft --title "WIP: Feature"

# Create with reviewers
gh pr create --title "Feature" --reviewer user1,user2

# Create with assignees
gh pr create --title "Feature" --assignee @me

# Create with labels
gh pr create --title "Feature" --label enhancement,documentation

# Create with milestone
gh pr create --title "Feature" --milestone "v1.0"

# Create to specific branch
gh pr create --base develop --head feature-branch

# Fill with commits
gh pr create --fill

# Auto-fill from commits and title from branch
gh pr create --fill-first

# Create and open in browser
gh pr create --web
```

### List Pull Requests
```bash
# List open PRs
gh pr list

# List all PRs
gh pr list --state all

# List by author
gh pr list --author username
gh pr list --author @me

# List with filters
gh pr list --label bug --assignee @me

# List by base branch
gh pr list --base main

# Search PRs
gh pr list --search "fix in:title"

# List draft PRs
gh pr list --draft

# List ready for review
gh pr list --search "is:pr is:open review:required"
```

### View Pull Request
```bash
# View PR in browser
gh pr view 123

# View in terminal
gh pr view 123 --web=false

# View with comments
gh pr view 123 --comments

# View diff
gh pr diff 123

# View with JSON output
gh pr view 123 --json title,body,state
```

### Checkout Pull Request
```bash
# Checkout PR branch locally
gh pr checkout 123

# Checkout by branch name
gh pr checkout feature-branch

# Force checkout (discard local changes)
gh pr checkout 123 --force
```

### Review Pull Request
```bash
# Start interactive review
gh pr review 123

# Approve PR
gh pr review 123 --approve

# Request changes
gh pr review 123 --request-changes --body "Please fix the typo"

# Comment on PR
gh pr review 123 --comment --body "Looks good overall"

# Approve with comment
gh pr review 123 --approve --body "LGTM!"
```

### Comment on Pull Request
```bash
# Add comment
gh pr comment 123 --body "Great work!"

# Add comment from file
gh pr comment 123 --body-file comment.md
```

### Edit Pull Request
```bash
# Edit PR interactively
gh pr edit 123

# Update title
gh pr edit 123 --title "New title"

# Update body
gh pr edit 123 --body "Updated description"

# Add/remove labels
gh pr edit 123 --add-label "ready-to-merge"
gh pr edit 123 --remove-label "wip"

# Add/remove reviewers
gh pr edit 123 --add-reviewer user1,user2
gh pr edit 123 --remove-reviewer user3

# Add/remove assignees
gh pr edit 123 --add-assignee @me
gh pr edit 123 --remove-assignee username

# Update milestone
gh pr edit 123 --milestone "v2.0"

# Update base branch
gh pr edit 123 --base develop

# Convert to draft
gh pr ready 123 --undo

# Mark as ready for review
gh pr ready 123
```

### Merge Pull Request
```bash
# Merge PR (default merge commit)
gh pr merge 123

# Merge with squash
gh pr merge 123 --squash

# Merge with rebase
gh pr merge 123 --rebase

# Merge and delete branch
gh pr merge 123 --delete-branch

# Auto-merge when checks pass
gh pr merge 123 --auto --squash

# Merge with custom commit message
gh pr merge 123 --subject "Custom message" --body "Details"

# Disable auto-merge
gh pr merge 123 --disable-auto
```

### Close/Reopen Pull Request
```bash
# Close PR without merging
gh pr close 123

# Close with comment
gh pr close 123 --comment "Closing due to..."

# Delete branch when closing
gh pr close 123 --delete-branch

# Reopen PR
gh pr reopen 123

# Reopen with comment
gh pr reopen 123 --comment "Reopening because..."
```

### Check PR Status
```bash
# Check status of your PRs
gh pr status

# View checks for a PR
gh pr checks 123

# Watch checks in real-time
gh pr checks 123 --watch

# View specific check details
gh pr checks 123 --required
```

---

## GitHub Actions & Workflows

### List Workflows
```bash
# List all workflows
gh workflow list

# List with status
gh workflow list --all
```

### View Workflow
```bash
# View workflow details
gh workflow view workflow-name

# View workflow in browser
gh workflow view workflow-name --web

# View workflow YAML
gh workflow view workflow-name --yaml
```

### Run Workflow
```bash
# Run workflow interactively
gh workflow run

# Run specific workflow
gh workflow run workflow-name

# Run with inputs
gh workflow run workflow-name --field key=value

# Run on specific branch
gh workflow run workflow-name --ref branch-name

# Run with raw parameters
gh workflow run workflow-name --raw-field key=value
```

### Enable/Disable Workflow
```bash
# Enable workflow
gh workflow enable workflow-name

# Disable workflow
gh workflow disable workflow-name
```

### List Workflow Runs
```bash
# List all runs
gh run list

# List for specific workflow
gh run list --workflow workflow-name

# List with filters
gh run list --branch main --status success
gh run list --event push
gh run list --limit 50

# List all statuses
gh run list --status completed
gh run list --status failure
gh run list --status in_progress
```

### View Run Details
```bash
# View run in browser
gh run view 123456

# View in terminal
gh run view 123456 --web=false

# View logs
gh run view 123456 --log

# View specific job logs
gh run view 123456 --job=job-id --log

# Exit with non-zero if run failed
gh run view 123456 --exit-status
```

### Watch Run
```bash
# Watch run progress
gh run watch 123456

# Watch with exit status
gh run watch 123456 --exit-status
```

### Rerun Workflow
```bash
# Rerun entire workflow
gh run rerun 123456

# Rerun only failed jobs
gh run rerun 123456 --failed

# Rerun with debug
gh run rerun 123456 --debug
```

### Cancel Run
```bash
# Cancel workflow run
gh run cancel 123456
```

### Download Run Artifacts
```bash
# Download all artifacts
gh run download 123456

# Download specific artifact
gh run download 123456 --name artifact-name

# Download to specific directory
gh run download 123456 --dir ./downloads
```

### Delete Run
```bash
# Delete workflow run
gh run delete 123456
```

### View Run Logs
```bash
# View logs for latest run
gh run view --log

# View logs for specific run
gh run view 123456 --log

# View logs for specific job
gh run view 123456 --log --job=job-name
```

---

## Gist Management

### Create Gist
```bash
# Create gist from file
gh gist create file.txt

# Create multiple files
gh gist create file1.txt file2.js

# Create from stdin
echo "Hello World" | gh gist create -

# Create with description
gh gist create file.txt --desc "My awesome gist"

# Create public gist
gh gist create file.txt --public

# Create with filename
gh gist create - --filename "hello.txt" < file.txt

# Create and open in browser
gh gist create file.txt --web
```

### List Gists
```bash
# List your gists
gh gist list

# List with limit
gh gist list --limit 50

# List public only
gh gist list --public

# List secret only
gh gist list --secret
```

### View Gist
```bash
# View gist in browser
gh gist view gist-id

# View in terminal
gh gist view gist-id --raw

# View specific file
gh gist view gist-id --filename file.txt

# View files list
gh gist view gist-id --files
```

### Edit Gist
```bash
# Edit gist file
gh gist edit gist-id --filename file.txt

# Add file to gist
gh gist edit gist-id --add new-file.txt

# Remove file from gist
gh gist edit gist-id --remove old-file.txt

# Update description
gh gist edit gist-id --desc "Updated description"
```

### Clone Gist
```bash
# Clone gist
gh gist clone gist-id

# Clone to specific directory
gh gist clone gist-id target-dir
```

### Delete Gist
```bash
# Delete gist
gh gist delete gist-id
```

---

## Release Management

### Create Release
```bash
# Interactive release creation
gh release create

# Create with tag
gh release create v1.0.0

# Create with title and notes
gh release create v1.0.0 --title "Version 1.0.0" --notes "Release notes"

# Create from notes file
gh release create v1.0.0 --notes-file CHANGELOG.md

# Create draft release
gh release create v1.0.0 --draft

# Create pre-release
gh release create v1.0.0 --prerelease

# Create with assets
gh release create v1.0.0 ./dist/*.zip

# Generate notes automatically
gh release create v1.0.0 --generate-notes

# Create with specific target
gh release create v1.0.0 --target main

# Verify tag signature
gh release create v1.0.0 --verify-tag
```

### List Releases
```bash
# List all releases
gh release list

# List with limit
gh release list --limit 20

# Exclude drafts
gh release list --exclude-drafts

# Exclude pre-releases
gh release list --exclude-pre-releases
```

### View Release
```bash
# View latest release
gh release view

# View specific release
gh release view v1.0.0

# View in browser
gh release view v1.0.0 --web

# View with JSON output
gh release view v1.0.0 --json tagName,name,assets
```

### Download Release Assets
```bash
# Download all assets from latest release
gh release download

# Download from specific release
gh release download v1.0.0

# Download specific asset
gh release download v1.0.0 --pattern "*.zip"

# Download to specific directory
gh release download v1.0.0 --dir ./downloads

# Skip existing files
gh release download v1.0.0 --skip-existing

# Download archive
gh release download v1.0.0 --archive zip
```

### Edit Release
```bash
# Edit release interactively
gh release edit v1.0.0

# Update title
gh release edit v1.0.0 --title "New Title"

# Update notes
gh release edit v1.0.0 --notes "Updated notes"

# Mark as latest
gh release edit v1.0.0 --latest

# Mark as pre-release
gh release edit v1.0.0 --prerelease

# Convert draft to published
gh release edit v1.0.0 --draft=false

# Update tag
gh release edit v1.0.0 --tag v1.0.1
```

### Upload Release Assets
```bash
# Upload assets to release
gh release upload v1.0.0 ./dist/*.zip

# Upload and overwrite existing
gh release upload v1.0.0 app.zip --clobber
```

### Delete Release
```bash
# Delete release
gh release delete v1.0.0

# Delete with confirmation
gh release delete v1.0.0 --yes

# Delete and cleanup tag
gh release delete v1.0.0 --cleanup-tag
```

---

## GitHub Copilot (CLI)

### Copilot Explain
```bash
# Explain a command
gh copilot explain "git rebase -i HEAD~3"

# Explain in interactive mode
gh copilot explain
```

### Copilot Suggest
```bash
# Get command suggestions
gh copilot suggest "list all running docker containers"

# Suggest git command
gh copilot suggest --target git "undo last commit"

# Suggest GitHub CLI command
gh copilot suggest --target gh "create a pull request"

# Suggest shell command
gh copilot suggest --target shell "find large files"
```

---

## Advanced Tips & Tricks

### Configuration

#### View Configuration
```bash
# View all configuration
gh config list

# Get specific value
gh config get editor
gh config get git_protocol
```

#### Set Configuration
```bash
# Set default editor
gh config set editor vim
gh config set editor "code --wait"

# Set git protocol
gh config set git_protocol ssh
gh config set git_protocol https

# Set browser
gh config set browser firefox

# Set default prompt behavior
gh config set prompt enabled

# Set preferred pager
gh config set pager less
```

### Aliases

#### List Aliases
```bash
gh alias list
```

#### Create Aliases
```bash
# Simple alias
gh alias set pv 'pr view'

# Alias with parameters
gh alias set bugs 'issue list --label=bug'

# Shell alias
gh alias set --shell igrep 'gh issue list --label="$1" | grep "$2"'

# Complex aliases
gh alias set prs 'pr list --state open --author @me'
gh alias set co 'pr checkout'
gh alias set merge 'pr merge --squash --delete-branch'
```

#### Delete Alias
```bash
gh alias delete pv
```

### Search

#### Search Repositories
```bash
# Search repositories
gh search repos "machine learning" --language python

# Search with stars filter
gh search repos "web framework" --stars ">1000"

# Search in specific organization
gh search repos "react" --owner facebook

# Limit results
gh search repos "cli tool" --limit 10

# Search archived repos
gh search repos "archived:true"

# Search by topic
gh search repos "topic:nodejs"
```

#### Search Code
```bash
# Search code
gh search code "func main" --language go

# Search in specific repository
gh search code "authentication" --repo owner/repo

# Search by filename
gh search code --filename ".github/workflows/*.yml"

# Search in path
gh search code "import" --path "src/"
```

#### Search Issues & PRs
```bash
# Search issues
gh search issues "bug" --repo owner/repo

# Search PRs
gh search prs "fix" --state open

# Search by label
gh search issues "label:bug"

# Search by author
gh search issues "author:username"

# Search by assignee
gh search issues "assignee:@me"

# Complex search
gh search issues "is:open label:bug created:>2024-01-01"
```

#### Search Commits
```bash
# Search commits
gh search commits "fix bug"

# Search in repository
gh search commits "security" --repo owner/repo

# Search by author
gh search commits "author:username"

# Search by committer
gh search commits "committer:username"

# Search with date range
gh search commits "created:2024-01-01..2024-12-31"
```

### API Access

#### Make API Calls
```bash
# GET request
gh api repos/:owner/:repo

# GET with parameters
gh api repos/:owner/:repo/issues --field state=open

# POST request
gh api repos/:owner/:repo/issues --method POST --field title="Bug report"

# Paginate results
gh api repos/:owner/:repo/issues --paginate

# Use custom headers
gh api repos/:owner/:repo --header "Accept: application/vnd.github.v3+json"

# GraphQL query
gh api graphql -f query='query { viewer { login } }'

# Save response to file
gh api repos/:owner/:repo > repo.json

# Use JQ to parse
gh api repos/:owner/:repo | jq '.stargazers_count'
```

### Extensions

#### List Extensions
```bash
gh extension list
```

#### Install Extension
```bash
# Install from repository
gh extension install owner/gh-extension-name

# Install from local path
gh extension install .
```

#### Upgrade Extensions
```bash
# Upgrade all extensions
gh extension upgrade --all

# Upgrade specific extension
gh extension upgrade extension-name
```

#### Remove Extension
```bash
gh extension remove extension-name
```

#### Browse Extensions
```bash
gh extension browse
```

### SSH Keys

#### List SSH Keys
```bash
gh ssh-key list
```

#### Add SSH Key
```bash
# Add from file
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My Laptop"

# Add with stdin
cat ~/.ssh/id_rsa.pub | gh ssh-key add --title "Work Desktop"
```

#### Delete SSH Key
```bash
gh ssh-key delete key-id
```

### GPG Keys

#### List GPG Keys
```bash
gh gpg-key list
```

#### Add GPG Key
```bash
# Add from file
gh gpg-key add key.asc

# Add with stdin
gpg --armor --export key-id | gh gpg-key add
```

#### Delete GPG Key
```bash
gh gpg-key delete key-id
```

### Notifications

#### List Notifications
```bash
# List all notifications
gh api notifications

# List unread notifications
gh api notifications --field all=false

# Mark as read
gh api notifications --method PUT
```

### Environment Variables

Important environment variables for `gh`:

```bash
# GitHub token
export GITHUB_TOKEN=your_token_here

# GitHub Enterprise hostname
export GH_ENTERPRISE_TOKEN=token
export GH_HOST=github.enterprise.com

# Default editor
export EDITOR=vim

# Pager
export PAGER=less

# Browser
export BROWSER=firefox

# Force terminal output
export GH_FORCE_TTY=true

# Disable color output
export NO_COLOR=1

# Debug mode
export GH_DEBUG=1

# API debug mode
export GH_DEBUG=api
```

### Output Formats

#### JSON Output
```bash
# Output as JSON
gh repo view --json name,description,url

# Specific fields
gh pr list --json number,title,author

# Pretty print JSON
gh repo view --json name | jq .
```

#### Template Output
```bash
# Use Go templates
gh pr list --template '{{range .}}{{.number}} - {{.title}}{{"\n"}}{{end}}'

# Table format
gh issue list --template '{{tablerow "NUMBER" "TITLE" "LABELS"}}{{range .}}{{tablerow .number .title .labels}}{{end}}'
```

### Scripting Examples

#### Daily Workflow Script
```bash
#!/bin/bash

# Check auth status
gh auth status || gh auth login

# View your PRs and issues
echo "=== Your Pull Requests ==="
gh pr status

echo -e "\n=== Your Issues ==="
gh issue status

echo -e "\n=== Recent Activity ==="
gh api user/events --paginate --jq '.[0:5] | .[] | "\(.type) - \(.repo.name)"'
```

#### Batch PR Checkout and Test
```bash
#!/bin/bash

# Get all open PRs
for pr in $(gh pr list --json number -q '.[].number'); do
    echo "Testing PR #$pr"
    gh pr checkout $pr
    npm test
    if [ $? -eq 0 ]; then
        gh pr review $pr --approve --body "Tests passed ✅"
    fi
done
```

#### Auto-merge Dependabot PRs
```bash
#!/bin/bash

gh pr list --author "app/dependabot" --json number,title | \
jq -r '.[] | select(.title | startswith("Bump")) | .number' | \
while read pr; do
    gh pr review "$pr" --approve
    gh pr merge "$pr" --auto --squash
done
```

#### Create Release with Changelog
```bash
#!/bin/bash

VERSION=$1
PREVIOUS_TAG=$(gh release list --limit 1 --json tagName -q '.[0].tagName')

# Generate changelog
gh api repos/:owner/:repo/compare/$PREVIOUS_TAG...HEAD \
    --jq '.commits | map("- " + .commit.message) | join("\n")' > CHANGELOG.txt

# Create release
gh release create "$VERSION" --title "Release $VERSION" --notes-file CHANGELOG.txt
```

### Useful One-Liners

```bash
# Clone all repos from an organization
gh repo list ORG --limit 1000 --json name -q '.[].name' | xargs -I {} gh repo clone ORG/{}

# List all your starred repositories
gh api user/starred --paginate --jq '.[] | .full_name'

# Find PRs that mention you
gh search prs "mentions:@me is:open"

# Get repository statistics
gh api repos/:owner/:repo --jq '{stars: .stargazers_count, forks: .forks_count, watchers: .watchers_count}'

# List all workflow runs for today
gh run list --created $(date +%Y-%m-%d)

# Watch CI status for current branch
watch -n 5 gh pr checks

# Create issue from commit message
git log -1 --pretty=%B | gh issue create --title "$(git log -1 --pretty=%s)" --body-file -

# Bulk close stale issues
gh issue list --label stale --json number -q '.[].number' | xargs -I {} gh issue close {}

# List contributors
gh api repos/:owner/:repo/contributors --paginate --jq '.[] | .login' | sort -u

# Compare branches
gh api repos/:owner/:repo/compare/main...develop --jq '.commits | length'
```

---

## Quick Reference Card

### Most Common Daily Commands

```bash
# Authentication
gh auth login
gh auth status

# Repository
gh repo create <name> --private
gh repo clone <owner>/<repo>
gh repo view --web

# Issues
gh issue create
gh issue list
gh issue view <number>
gh issue close <number>

# Pull Requests
gh pr create
gh pr list
gh pr checkout <number>
gh pr view <number>
gh pr merge <number> --squash
gh pr status

# Workflows
gh workflow run <workflow>
gh run list
gh run view <run-id> --log
gh run watch <run-id>

# Releases
gh release create <tag>
gh release download <tag>
```

### Keyboard Shortcuts in Interactive Mode

- `Ctrl+C` - Cancel/Exit
- `Tab` - Auto-complete
- `↑/↓` - Navigate options
- `Enter` - Select/Confirm
- `Space` - Toggle selection (multi-select)
- `Ctrl+D` - End input (EOF)

---

## Troubleshooting

### Common Issues

#### Authentication Failed
```bash
# Clear credentials and re-login
gh auth logout
gh auth login
```

#### Token Permissions
```bash
# Check current token scopes
gh auth status

# Generate new token with required scopes
gh auth refresh -h github.com -s admin:public_key,repo,workflow
```

#### Rate Limit Exceeded
```bash
# Check rate limit status
gh api rate_limit
```

#### Network Issues
```bash
# Use HTTPS instead of SSH
gh config set git_protocol https

# Or use SSH
gh config set git_protocol ssh
```

### Debug Mode

```bash
# Enable debug output
export GH_DEBUG=1
gh <command>

# API debug mode
export GH_DEBUG=api
gh api <endpoint>
```

---

## Resources

- **Official Documentation**: https://cli.github.com/manual/
- **GitHub Repository**: https://github.com/cli/cli
- **Release Notes**: https://github.com/cli/cli/releases
- **Extensions**: https://github.com/topics/gh-extension
- **Community Forum**: https://github.com/cli/cli/discussions

---

## Notes for Termux Users

### Specific Considerations

1. **Storage Access**: Grant storage permissions to Termux
   ```bash
   termux-setup-storage
   ```

2. **Text Editor**: Use Termux-compatible editors
   ```bash
   gh config set editor nano
   # or
   gh config set editor vim
   ```

3. **Browser**: Open URLs with Termux-URL-Opener
   ```bash
   gh pr view 123 --web
   # Will use termux-open-url
   ```

4. **Git Configuration**: Ensure git is properly configured
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your@email.com"
   ```

5. **SSH Keys**: Generate and add SSH keys for better performance
   ```bash
   ssh-keygen -t ed25519 -C "your@email.com"
   gh ssh-key add ~/.ssh/id_ed25519.pub --title "Termux"
   ```

---

## Version Information

This guide is updated for:
- **GitHub CLI**: v2.60+ (2025)
- **Termux**: Current stable version
- **Last Updated**: October 2025

---

**Happy GitHubbing from Termux! 🚀**
```

---

## Explanation and Usage Guide

### How to Use This Guide

#### 1. **Save the Markdown File**
```bash
# In Termux, create the file
nano gh-cli-guide.md
# Paste the content and save (Ctrl+X, Y, Enter)
```

#### 2. **Quick Search**
Use `grep` to find specific commands:
```bash
grep -i "pull request" gh-cli-guide.md
grep -A 5 "gh pr create" gh-cli-guide.md
```

#### 3. **View in Formatted Way**
```bash
# Install glow for beautiful markdown rendering
pkg install glow

# View the guide
glow gh-cli-guide.md

# Or use less
less gh-cli-guide.md
```

### Key Features Explained

#### **1. Authentication (Essential First Step)**
```bash
gh auth login
```
- Choose "GitHub.com"
- Select "HTTPS" protocol
- Choose "Login with a web browser" or "Paste an authentication token"
- Follow prompts to complete authentication

**Example:**
```bash
$ gh auth login
? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Login with a web browser
```

#### **2. Repository Management**
```bash
# Create a new repository from scratch
gh repo create my-awesome-project --public --description "My project" --gitignore Node --license MIT

# This creates a repository with:
# - Public visibility
# - Node.js .gitignore
# - MIT license
# - Custom description
```

**From existing directory:**
```bash
cd my-project
git init
gh repo create --source=. --private --push
# Automatically creates repo and pushes existing code
```

#### **3. Issues Workflow**

**Complete workflow example:**
```bash
# Create issue
gh issue create --title "Add dark mode" --body "Users requested dark mode" --label enhancement

# Assign to yourself
gh issue edit 42 --add-assignee @me

# Add comment while working
gh issue comment 42 --body "Working on this now"

# Close when done
gh issue close 42 --comment "Implemented in PR #43"
```

#### **4. Pull Request Workflow**

**Complete PR lifecycle:**
```bash
# Create feature branch
git checkout -b feature/dark-mode

# Make changes and commit
git add .
git commit -m "Add dark mode support"

# Push and create PR
git push -u origin feature/dark-mode
gh pr create --title "Add dark mode" --body "Implements #42" --reviewer teammate

# Review your own PR
gh pr view --web

# After review, merge
gh pr merge --squash --delete-branch
```

**Reviewing PRs:**
```bash
# Checkout PR locally to test
gh pr checkout 123

# Test the changes
npm test

# Approve if good
gh pr review 123 --approve --body "LGTM! Tests pass."

# Or request changes
gh pr review 123 --request-changes --body "Please fix the styling issues"
```

#### **5. GitHub Actions/CI**

**Monitoring workflows:**
```bash
# Check what's running
gh run list --status in_progress

# Watch a specific run
gh run watch 1234567

# Download logs if failed
gh run view 1234567 --log > workflow-logs.txt

# Rerun failed jobs
gh run rerun 1234567 --failed
```

**Manual workflow trigger:**
```bash
# Trigger deployment workflow
gh workflow run deploy.yml --ref main --field environment=production
```

#### **6. Release Management**

**Complete release process:**
```bash
# Tag your version
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin v1.0.0

# Create release with auto-generated notes
gh release create v1.0.0 --title "Release v1.0.0" --generate-notes

# Upload build artifacts
gh release upload v1.0.0 ./dist/app-linux-x64.tar.gz ./dist/app-win-x64.zip

# Users can download
gh release download v1.0.0 --pattern "*.tar.gz"
```

#### **7. Useful Aliases**

```bash
# Create helpful aliases
gh alias set co 'pr checkout'
gh alias set pv 'pr view'
gh alias set bugs 'issue list --label bug --state open'
gh alias set mine 'issue list --assignee @me'

# Now use them
gh bugs          # Lists all bugs
gh mine          # Lists your assigned issues
gh co 123        # Checkouts PR #123
gh pv --web      # Views PR in browser
```

#### **8. Advanced Scripting**

**Auto-update dependencies:**
```bash
#!/bin/bash
# update-deps.sh

gh pr list --author app/dependabot --state open --json number,title | \
jq -r '.[] | select(.title | contains("patch")) | .number' | \
while read pr; do
    echo "Auto-approving Dependabot PR #$pr"
    gh pr review "$pr" --approve --body "Auto-approved patch update"
    gh pr merge "$pr" --auto --squash
done
```

**Bulk operations:**
```bash
# Close all issues with specific label
gh issue list --label "wontfix" --state open --json number -q '.[].number' | \
while read issue; do
    gh issue close "$issue" --reason "not planned" --comment "Closing as won't fix"
done
```

### Best Practices

1. **Always authenticate first**: `gh auth login`
2. **Use `--help` liberally**: Every command has detailed help
3. **Start with interactive mode**: Many commands have interactive modes (e.g., `gh pr create`)
4. **Use aliases**: Create shortcuts for frequent operations
5. **Combine with git**: Use `gh` alongside regular git commands
6. **Enable auto-completion**: Improves productivity significantly
7. **Use JSON output for scripting**: `--json` flag for automation
8. **Watch instead of polling**: Use `gh run watch` instead of repeatedly checking

### Integration Examples

#### **With CI/CD:**
```bash
# In your CI script
gh release create $VERSION --generate-notes
gh release upload $VERSION ./artifacts/*
```

#### **With Issue Tracking:**
```bash
# Create issue from test failures
if ! npm test; then
    gh issue create --title "Tests failing on $(date)" --body "$(npm test 2>&1)" --label bug
fi
```

#### **With Git Hooks:**
```bash
# In .git/hooks/pre-push
#!/bin/bash
if gh pr view &>/dev/null; then
    echo "PR exists, running checks..."
    gh pr checks --watch
fi
```

---

## Sources

This guide was compiled from:
1. **Official GitHub CLI Documentation**: https://cli.github.com/manual/ [1]
2. **GitHub CLI Repository**: https://github.com/cli/cli [2]
3. **GitHub CLI Manual Pages**: Commands and options verified against official documentation [3]
4. **GitHub API Documentation**: For API-related commands [4]
5. **Community Best Practices**: Common usage patterns from GitHub Discussions [5]

The guide includes all features available in GitHub CLI v2.60+ (current as of 2025) and has been specifically adapted for Termux usage with relevant considerations for Android environment.