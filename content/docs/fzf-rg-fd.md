--- 
title: "fzf functions"
description: "readme"
date: 2025-10-28T14:09:52Z
lastmod: 2025-10-28T14:09:52Z
tags: ["Sort"]
draft: false
---

# 📁 **FZF_RG_FD_COMPLETE_GUIDE.md**

# 🚀 The Ultimate FZF + Ripgrep + fd Guide for Termux (2025)

> **The Trinity of Modern CLI Tools**  
> **Last Updated:** October 2025  
> **Optimized for:** Termux on Android

## 📦 Official Repositories
- **fzf:** https://github.com/junegunn/fzf (Fuzzy Finder)
- **ripgrep (rg):** https://github.com/BurntSushi/ripgrep (Fast grep alternative)
- **fd:** https://github.com/sharkdp/fd (Fast find alternative)

---

## 📋 Table of Contents
- [Installation](#installation)
- [Why This Trinity?](#why-this-trinity)
- [Quick Start](#quick-start)
- [FZF + FD Integration](#fzf--fd-integration)
- [FZF + Ripgrep Integration](#fzf--ripgrep-integration)
- [The Complete Trinity](#the-complete-trinity)
- [Real-World Workflows](#real-world-workflows)
- [Advanced Recipes](#advanced-recipes)
- [Custom Functions Library](#custom-functions-library)
- [Performance Optimization](#performance-optimization)
- [Termux-Specific Tips](#termux-specific-tips)

---

## 📥 Installation

### Install All Three Tools in Termux
```bash
# Update package lists
pkg update && pkg upgrade

# Install the trinity
pkg install fzf ripgrep fd

# Optional but highly recommended companions
pkg install bat exa git

# Verify installations
fzf --version
rg --version
fd --version
```

---

## 🎯 Why This Trinity?

### **The Problem with Traditional Tools:**
```bash
# Slow and verbose
find . -type f -name "*.js" 2>/dev/null

# Limited and outdated
grep -r "function" .

# Not interactive
locate file.txt
```

### **The Modern Solution:**

| Tool | Purpose | Speed | Features |
|------|---------|-------|----------|
| **fd** | Find files | 🚀 3-10x faster than `find` | Ignores .git, colors, smart case |
| **rg** | Search content | 🚀 5-50x faster than `grep` | Respects .gitignore, colors |
| **fzf** | Interactive filter | 🚀 Instant fuzzy search | Preview, multi-select, keybindings |

### **Combined Power:**
```bash
# Find files blazingly fast + interactive selection + live preview
fd --type f | fzf --preview 'bat --color=always {}'

# Search content everywhere + interactive results + jump to line
rg --line-number . | fzf --delimiter : --preview 'bat --color=always {1} --highlight-line {2}'
```

---

## ⚡ Quick Start

### Basic Commands

#### **fd (Fast Find)**
```bash
# Find all files
fd

# Find by name
fd config

# Find specific type
fd -e js          # JavaScript files
fd -e py          # Python files
fd -e md          # Markdown files

# Find in specific directory
fd config /etc

# Include hidden files
fd -H

# Search in depth
fd --max-depth 3
```

#### **rg (Ripgrep)**
```bash
# Search in current directory
rg "pattern"

# Case-insensitive search
rg -i "pattern"

# Show line numbers
rg -n "pattern"

# Search specific file types
rg -t js "function"
rg -t py "def "

# Show context (lines before/after)
rg -C 3 "pattern"

# Search and show file names only
rg -l "pattern"
```

#### **fzf (Fuzzy Finder)**
```bash
# Basic fuzzy find
fzf

# With preview
fzf --preview 'cat {}'

# Multi-select
fzf -m

# Custom height
fzf --height 40%
```

---

## 🔗 FZF + FD Integration

### 1. **Basic File Finder (fd + fzf)**

```bash
# Simple file search
fd --type f | fzf

# With preview
fd --type f | fzf --preview 'bat --color=always --style=numbers {}'

# Include hidden files
fd --type f --hidden | fzf --preview 'bat --color=always {}'

# Exclude patterns
fd --type f --exclude node_modules --exclude .git | fzf --preview 'bat {}'
```

### 2. **Directory Navigation (fd + fzf)**

```bash
# Jump to any directory
cd $(fd --type d | fzf)

# With preview of directory contents
cd $(fd --type d | fzf --preview 'ls -lah {}')

# With tree preview (if tree is installed)
cd $(fd --type d | fzf --preview 'tree -L 2 {}')

# With exa preview (modern ls)
cd $(fd --type d | fzf --preview 'exa -lah --icons {}')
```

### 3. **File Type Specific Searches**

```bash
# Find and edit JavaScript files
vim $(fd -e js | fzf --preview 'bat --color=always {}')

# Find Python files
fd -e py | fzf --preview 'bat -l python --color=always {}'

# Find config files
fd -e conf -e config -e yaml -e yml -e json | fzf --preview 'bat --color=always {}'

# Find images
fd -e jpg -e png -e gif | fzf --preview 'termux-open {}'

# Find markdown files
fd -e md | fzf --preview 'bat --color=always -l markdown {}'
```

### 4. **Set fd as Default for fzf**

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Use fd instead of find for fzf
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'

# For CTRL-T (file finder)
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"

# For ALT-C (directory finder)
export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'

# Better preview with bat
export FZF_CTRL_T_OPTS="
  --preview 'bat --color=always --style=numbers --line-range=:500 {}'
  --preview-window=right:60%:wrap
  --bind='ctrl-/:toggle-preview'
  --bind='ctrl-u:preview-up,ctrl-d:preview-down'
"

export FZF_ALT_C_OPTS="
  --preview 'exa -lah --icons {} || ls -lah {}'
  --preview-window=right:60%
"
```

---

## 🔍 FZF + Ripgrep Integration

### 1. **Content Search with Preview**

```bash
# Search and preview results
rg --line-number --no-heading --color=always . | \
  fzf --ansi \
      --delimiter : \
      --preview 'bat --color=always {1} --highlight-line {2}' \
      --preview-window 'right,60%,+{2}+3/3,~3'

# Simpler version
rg --line-number . | \
  fzf --delimiter : \
      --preview 'bat --color=always {1} --highlight-line {2}'
```

### 2. **Interactive Ripgrep Search**

```bash
# Live search - type to search, see results in real-time
rg --line-number --no-heading . 2>/dev/null | \
  fzf --ansi \
      --delimiter : \
      --preview 'bat --style=numbers --color=always {1} --highlight-line {2}' \
      --preview-window '+{2}-/2'
```

### 3. **Search Specific File Types**

```bash
# Search in JavaScript files
rg --type js --line-number . | \
  fzf --delimiter : \
      --preview 'bat --color=always -l javascript {1} --highlight-line {2}'

# Search in Python files
rg --type py --line-number . | \
  fzf --delimiter : \
      --preview 'bat --color=always -l python {1} --highlight-line {2}'

# Search in markdown files
rg --type md --line-number . | \
  fzf --delimiter : \
      --preview 'bat --color=always -l markdown {1} --highlight-line {2}'
```

### 4. **Search and Open in Editor**

```bash
# Open file at the exact line
vim $(rg --line-number . | \
  fzf --delimiter : \
      --preview 'bat --color=always {1} --highlight-line {2}' | \
  cut -d: -f1-2 | tr ':' ' ' | awk '{print "+" $2 " " $1}')

# Or use this cleaner version with editor that supports +line syntax
$EDITOR $(rg --line-number . | \
  fzf --delimiter : | \
  awk -F: '{print "+" $2 " " $1}')
```

---

## 🎯 The Complete Trinity

### 1. **Ultimate Search Function**

This combines all three tools for the perfect search experience:

```bash
# Add to ~/.bashrc or ~/.zshrc
# Search files by name, then by content
search() {
    local choice
    choice=$(echo -e "📁 Search Files\n🔍 Search Content\n📂 Search Directories" | fzf --height=10)
    
    case "$choice" in
        *"Search Files"*)
            fd --type f --hidden --exclude .git | \
            fzf --preview 'bat --color=always --style=numbers {}'
            ;;
        *"Search Content"*)
            rg --line-number --no-heading --color=always . | \
            fzf --ansi --delimiter : \
                --preview 'bat --color=always {1} --highlight-line {2}' \
                --preview-window '+{2}+3/3'
            ;;
        *"Search Directories"*)
            fd --type d --hidden --exclude .git | \
            fzf --preview 'exa -lah --icons {} || ls -lah {}'
            ;;
    esac
}
```

### 2. **Smart Search and Open**

```bash
# Searches by filename OR content, then opens the file
sopen() {
    local file
    local line
    
    # First, let user choose: search by name or content
    local mode=$(echo -e "By Filename\nBy Content" | fzf --height=5 --prompt="Search mode: ")
    
    if [[ "$mode" == "By Filename" ]]; then
        # Search by filename with fd
        file=$(fd --type f --hidden --exclude .git | \
               fzf --preview 'bat --color=always --style=numbers {}')
        [ -n "$file" ] && $EDITOR "$file"
    else
        # Search by content with rg
        local result=$(rg --line-number --no-heading --color=always . | \
                       fzf --ansi --delimiter : \
                           --preview 'bat --color=always {1} --highlight-line {2}' \
                           --preview-window '+{2}+3/3')
        [ -n "$result" ] && {
            file=$(echo "$result" | cut -d: -f1)
            line=$(echo "$result" | cut -d: -f2)
            $EDITOR "+$line" "$file"
        }
    fi
}
```

### 3. **Project-Wide Search**

```bash
# Search across project with context
psearch() {
    local query="$1"
    if [ -z "$query" ]; then
        echo "Usage: psearch <pattern>"
        return 1
    fi
    
    rg --line-number --no-heading --color=always "$query" | \
    fzf --ansi \
        --delimiter : \
        --preview "bat --color=always {1} --highlight-line {2} --style=numbers" \
        --preview-window 'right,60%,+{2}+3/3,~3' \
        --bind 'enter:execute($EDITOR +{2} {1})'
}
```

---

## 💼 Real-World Workflows

### Workflow 1: **Code Navigation**

```bash
# Find function definition
function_find() {
    rg --line-number "^(function|def|class|fn|func)" | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2} -r {2}:' \
        --preview-window '+{2}+5/3'
}

# Find TODO/FIXME comments
todo_find() {
    rg --line-number "TODO|FIXME|HACK|XXX|NOTE" | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}'
}

# Find imports/requires
import_find() {
    rg --line-number "^(import|require|from|#include)" | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}'
}
```

### Workflow 2: **Git Integration**

```bash
# Search through git-tracked files only
git_search() {
    rg --line-number . $(git rev-parse --show-toplevel) | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}'
}

# Find changed files
git_changed() {
    git diff --name-only | \
    fzf --preview 'git diff --color=always {} | bat --color=always -l diff'
}

# Search in git history
git_history_search() {
    git log --oneline --color=always | \
    fzf --ansi \
        --preview 'git show --color=always {1}' \
        --bind 'enter:execute(git show {1} | less -R)'
}

# Interactive git file browser
git_file_history() {
    git ls-files | \
    fzf --preview 'git log --color=always --oneline {} | head -20'
}
```

### Workflow 3: **Documentation Search**

```bash
# Search markdown documentation
doc_search() {
    fd -e md | \
    fzf --preview 'bat --color=always -l markdown {}' \
        --bind 'enter:execute($EDITOR {})'
}

# Search within markdown files
doc_content_search() {
    rg --type md --line-number . | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always -l markdown {1} --highlight-line {2}'
}

# Search code comments
comment_search() {
    rg --line-number "(//|#|\*|<!--)" | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}'
}
```

### Workflow 4: **Log File Analysis**

```bash
# Find and view log files
log_viewer() {
    fd -e log -e txt --max-depth 5 | \
    fzf --preview 'tail -100 {} | bat --color=always -l log' \
        --bind 'enter:execute(less {})'
}

# Search within logs
log_search() {
    local pattern="$1"
    rg --line-number "$pattern" $(fd -e log) | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2} -l log'
}

# Error log finder
error_search() {
    rg --line-number -i "error|exception|fail|fatal" $(fd -e log) | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2} -l log --color=always'
}
```

### Workflow 5: **Configuration File Management**

```bash
# Find and edit config files
config_edit() {
    fd -H -e conf -e config -e cfg -e ini -e yaml -e yml -e json -e toml . ~ ~/.config /etc 2>/dev/null | \
    fzf --preview 'bat --color=always {}' \
        --bind 'enter:execute($EDITOR {})'
}

# Search within config files
config_search() {
    local pattern="$1"
    rg --line-number -g "*.{conf,config,cfg,yaml,yml,json,toml}" "$pattern" ~ ~/.config 2>/dev/null | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}'
}
```

---

## 🔥 Advanced Recipes

### Recipe 1: **Interactive Grep with Live Preview**

```bash
# Add to ~/.bashrc or ~/.zshrc
# Live interactive search with real-time preview
rgi() {
    local initial_query="$1"
    rg --line-number --no-heading --color=always --smart-case "${initial_query:-.}" | \
    fzf --ansi \
        --disabled \
        --delimiter : \
        --bind "change:reload:rg --line-number --no-heading --color=always --smart-case {q} || true" \
        --preview 'bat --color=always {1} --highlight-line {2} --style=numbers' \
        --preview-window 'right,60%,+{2}+3/3,~3' \
        --bind 'enter:execute($EDITOR +{2} {1})'
}
```

### Recipe 2: **Fuzzy File Finder with Multiple Actions**

```bash
# Multi-action file finder
ff() {
    local file
    file=$(fd --type f --hidden --follow --exclude .git | \
           fzf --preview 'bat --color=always --style=numbers {}' \
               --bind 'ctrl-e:execute($EDITOR {})+abort' \
               --bind 'ctrl-o:execute(xdg-open {} || termux-open {})+abort' \
               --bind 'ctrl-y:execute-silent(echo {} | termux-clipboard-set || echo {} | xclip -selection clipboard)+abort' \
               --bind 'ctrl-d:execute(rm -i {})+reload(fd --type f)' \
               --header 'CTRL-E: Edit | CTRL-O: Open | CTRL-Y: Copy path | CTRL-D: Delete')
    echo "$file"
}
```

### Recipe 3: **Smart Code Search**

```bash
# Search code with file type detection
code_search() {
    local pattern="$1"
    local file_types="js,jsx,ts,tsx,py,go,rs,java,c,cpp,h,hpp,sh,bash,zsh"
    
    rg --line-number --color=always --type-add "code:*.{$file_types}" -t code "$pattern" | \
    fzf --ansi \
        --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2} --style=full' \
        --preview-window 'right,60%,border-left,+{2}+3/3,~3' \
        --bind 'ctrl-/:toggle-preview' \
        --bind 'ctrl-e:execute($EDITOR +{2} {1})' \
        --bind 'ctrl-o:execute(bat {1})' \
        --header 'CTRL-E: Edit | CTRL-O: View | CTRL-/: Toggle preview'
}
```

### Recipe 4: **Recursive Directory Browser**

```bash
# Browse directories recursively with instant switch
dbrowse() {
    while true; do
        local dir
        dir=$(fd --type d --hidden --max-depth 5 --exclude .git | \
              fzf --preview 'exa -lah --icons {} 2>/dev/null || ls -lah {}' \
                  --preview-window 'right,60%' \
                  --header 'Select directory (ESC to exit)' \
                  --bind 'ctrl-o:execute(cd {} && $SHELL)+abort')
        
        if [ -z "$dir" ]; then
            break
        fi
        
        cd "$dir" && echo "Changed to: $(pwd)"
    done
}
```

### Recipe 5: **Multi-Pattern Search**

```bash
# Search for multiple patterns
multi_search() {
    echo "Enter search patterns (one per line, empty line to finish):"
    local patterns=()
    while IFS= read -r line; do
        [ -z "$line" ] && break
        patterns+=("$line")
    done
    
    if [ ${#patterns[@]} -eq 0 ]; then
        echo "No patterns provided"
        return 1
    fi
    
    # Combine patterns with OR
    local combined=$(IFS='|'; echo "${patterns[*]}")
    
    rg --line-number --color=always "$combined" | \
    fzf --ansi --delimiter : \
        --preview 'bat --color=always {1} --highlight-line {2}' \
        --preview-window '+{2}+3/3'
}
```

### Recipe 6: **Project File Statistics**

```bash
# Analyze project files
project_stats() {
    echo "📊 Project Statistics"
    echo "===================="
    echo ""
    
    echo "📁 File Types:"
    fd --type f | sed 's/.*\.//' | sort | uniq -c | sort -rn | head -10
    
    echo ""
    echo "📂 Largest Files:"
    fd --type f -x ls -lh {} | sort -k5 -h -r | head -10 | awk '{print $5, $9}'
    
    echo ""
    echo "🔍 Code Statistics:"
    rg --count-matches "" --type-add 'code:*.{js,py,go,rs,java}' -t code | \
        awk -F: '{sum+=$2} END {print "Total Lines of Code: " sum}'
}
```

---

## 📚 Custom Functions Library

Add these to your `~/.bashrc` or `~/.zshrc`:

### Complete Functions Collection

```bash
# ============================================
# FZF + FD + RG Functions Library
# ============================================

# --- FILE OPERATIONS ---

# Find and edit files
fe() {
    local file
    file=$(fd --type f --hidden --follow --exclude .git | \
           fzf --preview 'bat --color=always --style=numbers {}' \
               --preview-window=right:60%) && \
    $EDITOR "$file"
}

# Find and edit by content
fc() {
    local file line
    local result=$(rg --line-number --no-heading --color=always . | \
                   fzf --ansi --delimiter : \
                       --preview 'bat --color=always {1} --highlight-line {2}' \
                       --preview-window '+{2}+3/3')
    [ -n "$result" ] && {
        file=$(echo "$result" | cut -d: -f1)
        line=$(echo "$result" | cut -d: -f2)
        $EDITOR "+$line" "$file"
    }
}

# Find and delete files safely
frm() {
    local files
    files=$(fd --type f | fzf -m --preview 'bat --color=always {}')
    [ -n "$files" ] && echo "$files" | xargs -I {} rm -i {}
}

# Find and copy files
fcp() {
    local files dest
    files=$(fd --type f | fzf -m --preview 'bat --color=always {}')
    [ -n "$files" ] && {
        read -p "Destination: " dest
        echo "$files" | xargs -I {} cp {} "$dest"
    }
}

# Find and move files
fmv() {
    local files dest
    files=$(fd --type f | fzf -m --preview 'bat --color=always {}')
    [ -n "$files" ] && {
        read -p "Destination: " dest
        echo "$files" | xargs -I {} mv {} "$dest"
    }
}

# --- DIRECTORY OPERATIONS ---

# Smart cd with fd and fzf
fcd() {
    local dir
    dir=$(fd --type d --hidden --follow --exclude .git . "${1:-.}" | \
          fzf --preview 'exa -lah --icons {} 2>/dev/null || ls -lah {}' \
              --preview-window=right:60%) && \
    cd "$dir"
}

# cd to parent directory interactively
fup() {
    local dir
    dir=$(pwd | sed 's|/|\n|g' | tail -n +2 | \
          fzf --preview 'ls -lah /$(echo {} | sed "s|^|$(pwd | sed "s|$PWD||; s|/|\n|g" | tail -n +2 | grep -B100 {} | tr "\n" "/" | sed "s|/$||")|/")') && \
    cd "/${dir}"
}

# Create and cd into directory
fmkcd() {
    local dir
    read -p "Directory name: " dir
    [ -n "$dir" ] && mkdir -p "$dir" && cd "$dir"
}

# --- SEARCH OPERATIONS ---

# Interactive ripgrep
frg() {
    local pattern="${1:-.}"
    rg --line-number --no-heading --color=always --smart-case "$pattern" | \
    fzf --ansi \
        --delimiter : \
        --bind "change:reload:rg --line-number --no-heading --color=always --smart-case {q} || true" \
        --preview 'bat --color=always {1} --highlight-line {2}' \
        --preview-window '+{2}+3/3'
}

# Search and replace across files
fsr() {
    local pattern replacement
    read -p "Search pattern: " pattern
    read -p "Replace with: " replacement
    
    local files=$(rg -l "$pattern" | fzf -m --preview "rg --color=always '$pattern' {}")
    
    if [ -n "$files" ]; then
        echo "$files" | while read -r file; do
            sed -i "s/$pattern/$replacement/g" "$file"
            echo "Updated: $file"
        done
    fi
}

# Find duplicate files
fdup() {
    fd --type f -x md5sum | \
    sort | \
    uniq -w32 -D | \
    cut -c35- | \
    fzf -m --preview 'bat --color=always {}'
}

# --- GIT OPERATIONS ---

# Git file selector
fgf() {
    git ls-files | \
    fzf --preview 'bat --color=always {}' \
        --bind 'enter:execute($EDITOR {})'
}

# Git branch switcher with preview
fgb() {
    local branch
    branch=$(git branch --all | grep -v HEAD | \
             sed 's/^..//' | sed 's#remotes/origin/##' | \
             sort -u | \
             fzf --preview 'git log --oneline --graph --date=short --color=always --pretty="format:%C(auto)%cd %h%d %s" {} | head -50') && \
    git checkout "$branch"
}

# Git commit browser
fgc() {
    git log --oneline --color=always | \
    fzf --ansi \
        --preview 'git show --color=always {1}' \
        --bind 'enter:execute(git show {1} | less -R)'
}

# Git stash browser
fgs() {
    git stash list | \
    fzf --preview 'git stash show -p {1}' \
        --bind 'enter:execute(git stash pop {1})'
}

# Interactive git add
fga() {
    git status --short | \
    fzf -m --preview 'git diff --color=always {2}' | \
    awk '{print $2}' | \
    xargs git add
}

# --- PROCESS OPERATIONS ---

# Kill process interactively
fkill() {
    local pid
    pid=$(ps aux | sed 1d | \
          fzf -m --preview 'echo {}' --preview-window=down:3:wrap | \
          awk '{print $2}')
    [ -n "$pid" ] && echo "$pid" | xargs kill -${1:-9}
}

# Process tree viewer
fptree() {
    ps auxf | \
    fzf --preview 'echo {}' \
        --preview-window=down:5:wrap \
        --header 'Process Tree'
}

# --- PACKAGE OPERATIONS ---

# Search and install packages (Termux)
fpkg() {
    pkg search '' | \
    fzf -m --preview 'pkg show {1}' \
        --bind 'enter:execute(pkg install {1})+abort'
}

# Browse installed packages
fpkgl() {
    pkg list-installed | \
    fzf --preview 'pkg show {}'
}

# --- UTILITY FUNCTIONS ---

# Quick note taker
fnote() {
    local note_dir="$HOME/notes"
    mkdir -p "$note_dir"
    
    local choice=$(echo -e "📝 New Note\n📖 Browse Notes\n🔍 Search Notes" | fzf --height=10)
    
    case "$choice" in
        *"New Note"*)
            read -p "Note name: " name
            $EDITOR "$note_dir/${name}.md"
            ;;
        *"Browse Notes"*)
            local note=$(fd -e md . "$note_dir" | \
                         fzf --preview 'bat --color=always -l markdown {}')
            [ -n "$note" ] && $EDITOR "$note"
            ;;
        *"Search Notes"*)
            local result=$(rg --line-number . "$note_dir" | \
                           fzf --delimiter : \
                               --preview 'bat --color=always {1} --highlight-line {2}')
            if [ -n "$result" ]; then
                local file=$(echo "$result" | cut -d: -f1)
                local line=$(echo "$result" | cut -d: -f2)
                $EDITOR "+$line" "$file"
            fi
            ;;
    esac
}

# History with better preview
fhist() {
    eval $(history | \
           fzf --tac --no-sort \
               --preview 'echo {}' \
               --preview-window=down:3:wrap | \
           sed 's/^[ ]*[0-9]*[ ]*//')
}

# Environment variable browser
fenv() {
    printenv | \
    fzf --preview 'echo {}' \
        --preview-window=down:3:wrap
}

# Find and open URLs
furl() {
    rg -o 'https?://[^\s]+' | \
    sort -u | \
    fzf --preview 'echo {}' | \
    xargs termux-open-url
}

# Bookmark manager
fbm() {
    local bookmark_file="$HOME/.bookmarks"
    touch "$bookmark_file"
    
    local choice=$(echo -e "➕ Add Bookmark\n📖 Open Bookmark\n🗑️ Remove Bookmark" | fzf --height=10)
    
    case "$choice" in
        *"Add Bookmark"*)
            read -p "URL: " url
            read -p "Description: " desc
            echo "$desc | $url" >> "$bookmark_file"
            ;;
        *"Open Bookmark"*)
            cat "$bookmark_file" | \
            fzf | \
            cut -d'|' -f2 | \
            xargs termux-open-url
            ;;
        *"Remove Bookmark"*)
            local line=$(cat "$bookmark_file" | fzf)
            [ -n "$line" ] && sed -i "\|$line|d" "$bookmark_file"
            ;;
    esac
}

# Clipboard history (requires termux-clipboard-get)
fclip() {
    local clip_dir="$HOME/.clipboard_history"
    mkdir -p "$clip_dir"
    
    # Save current clipboard
    termux-clipboard-get > "$clip_dir/$(date +%s).txt" 2>/dev/null
    
    # Browse history
    fd . "$clip_dir" -x cat | \
    fzf --preview 'echo {}' \
        --bind 'enter:execute(echo {} | termux-clipboard-set)+abort'
}

# File content preview
fpreview() {
    fd --type f | \
    fzf --preview 'bat --color=always --style=full {}' \
        --preview-window=right:70%
}

# Disk usage analyzer
fdu() {
    fd --type f -x du -h {} | \
    sort -h | \
    fzf --preview 'bat --color=always {}' \
        --bind 'enter:execute(echo {} | awk "{print \$2}" | xargs -I {} $EDITOR {})'
}

# --- COMBINED POWER FUNCTIONS ---

# Universal finder
finder() {
    local choice
    choice=$(echo -e "📁 Files\n📂 Directories\n🔍 Content\n🌳 Git\n⚙️ Processes\n📦 Packages" | \
             fzf --height=15 --prompt="What to find? ")
    
    case "$choice" in
        *"Files"*) fe ;;
        *"Directories"*) fcd ;;
        *"Content"*) fc ;;
        *"Git"*) fgf ;;
        *"Processes"*) fkill ;;
        *"Packages"*) fpkg ;;
    esac
}

# Smart open
sopen() {
    local target
    target=$(fd --type f --hidden | \
             fzf --preview '[[ $(file --mime {}) =~ binary ]] && 
                            echo "Binary file" || 
                            bat --color=always --style=numbers {}')
    
    if [ -n "$target" ]; then
        local mime=$(file --mime-type -b "$target")
        case "$mime" in
            text/*) $EDITOR "$target" ;;
            image/*) termux-open "$target" ;;
            video/*) termux-open "$target" ;;
            application/pdf) termux-open "$target" ;;
            *) $EDITOR "$target" ;;
        esac
    fi
}

# === END OF FUNCTIONS LIBRARY ===
```

---

## ⚡ Performance Optimization

### Speed Comparison

```bash
# Benchmark traditional vs modern tools

# Traditional (slow)
time find . -type f -name "*.js"          # ~5-10 seconds
time grep -r "function" .                 # ~10-30 seconds

# Modern (fast)
time fd -e js                              # ~0.5-1 second
time rg "function"                         # ~0.5-2 seconds
```

### Optimization Tips

```bash
# 1. Limit search depth
fd --max-depth 3
rg --max-depth 3

# 2. Exclude unnecessary directories
fd --exclude node_modules --exclude .git --exclude target

# 3. Use specific file types
rg --type js "pattern"        # Only JavaScript
fd -e py                       # Only Python files

# 4. Use .gitignore
# Both fd and rg respect .gitignore by default

# 5. Disable smart case if not needed
rg --case-sensitive "Pattern"

# 6. Use fixed strings for exact matches
rg --fixed-strings "exact.string"

# 7. Limit results
fd | head -100 | fzf
rg "pattern" | head -100 | fzf
```

### Configuration for Maximum Speed

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Optimized fd defaults
export FD_OPTIONS="--hidden --follow --exclude .git --exclude node_modules --max-depth 6"

# Optimized rg defaults
export RIPGREP_CONFIG_PATH="$HOME/.ripgreprc"

# Create ~/.ripgreprc
cat > ~/.ripgreprc << 'EOF'
# Exclude directories
--glob=!.git/
--glob=!node_modules/
--glob=!vendor/
--glob=!target/
--glob=!build/
--glob=!dist/

# Smart case by default
--smart-case

# Show line numbers
--line-number

# Follow symlinks
--follow

# Max columns for display
--max-columns=200

# Max depth
--max-depth=10
EOF

# Optimized fzf defaults
export FZF_DEFAULT_OPTS='
  --height=40%
  --layout=reverse
  --info=inline
  --border=rounded
  --margin=1
  --padding=1
  --no-scrollbar
'

# Use fd for fzf
export FZF_DEFAULT_COMMAND="fd --type f --hidden --follow --exclude .git --exclude node_modules"
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
export FZF_ALT_C_COMMAND="fd --type d --hidden --follow --exclude .git --exclude node_modules"
```

---

## 📱 Termux-Specific Tips

### Storage Setup

```bash
# Setup storage access first
termux-setup-storage

# Quick access to common directories
alias phone='cd ~/storage/shared && fzf | xargs termux-open'
alias pics='cd ~/storage/shared/DCIM && fd -e jpg -e png | fzf --preview "termux-open {}"'
alias downloads='cd ~/storage/downloads && fzf --preview "bat --color=always {}" | xargs termux-open'
```

### Android Integration

```bash
# Share files from Termux
fshare() {
    fd --type f | \
    fzf --preview 'bat --color=always {}' | \
    xargs -I {} termux-share {}
}

# Open with Android app
fopen() {
    fd --type f | \
    fzf --preview 'file {}' | \
    xargs termux-open
}

# Send SMS with content search
fsms() {
    local content=$(rg . | fzf --preview 'bat --color=always {1}')
    [ -n "$content" ] && termux-sms-send -n "$1" "$content"
}

# Copy to Android clipboard
fclip() {
    fd --type f | \
    fzf --preview 'bat --color=always {}' | \
    xargs cat | \
    termux-clipboard-set
}
```

### Quick Launch Scripts

```bash
# Create launcher script
cat > ~/launcher.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash

action=$(echo -e "\
🔍 Search Files
📁 Browse Files
🔎 Search Content
📂 Jump to Directory
📝 Edit Notes
🌐 Open Bookmark
📱 Phone Storage
⚙️ Edit Config
📊 Project Stats
🔧 System Info" | fzf --height=20 --prompt="Quick Action: ")

case "$action" in
    *"Search Files"*) fe ;;
    *"Browse Files"*) ff ;;
    *"Search Content"*) fc ;;
    *"Jump to Directory"*) fcd ;;
    *"Edit Notes"*) fnote ;;
    *"Open Bookmark"*) fbm ;;
    *"Phone Storage"*) cd ~/storage/shared && fzf | xargs termux-open ;;
    *"Edit Config"*) config_edit ;;
    *"Project Stats"*) project_stats ;;
    *"System Info"*) termux-info ;;
esac
EOF

chmod +x ~/launcher.sh

# Add widget shortcut
mkdir -p ~/.shortcuts
ln -s ~/launcher.sh ~/.shortcuts/Launcher
```

### Battery and Performance

```bash
# Check if running on battery
battery_check() {
    local battery=$(termux-battery-status | jq -r '.percentage')
    if [ "$battery" -lt 20 ]; then
        echo "⚠️ Low battery ($battery%). Using fast mode."
        export FD_OPTIONS="--max-depth 3"
        export FZF_DEFAULT_OPTS="--height=20% --no-preview"
    fi
}

# Call at shell startup
battery_check
```

---

## 🎨 Visual Themes

### Dracula Theme

```bash
export FZF_DEFAULT_OPTS='
  --color=fg:#f8f8f2,bg:#282a36,hl:#bd93f9
  --color=fg+:#f8f8f2,bg+:#44475a,hl+:#bd93f9
  --color=info:#ffb86c,prompt:#50fa7b,pointer:#ff79c6
  --color=marker:#ff79c6,spinner:#ffb86c,header:#6272a4
  --border=rounded
  --preview-window=border-rounded
'
```

### Nord Theme

```bash
export FZF_DEFAULT_OPTS='
  --color=fg:#e5e9f0,bg:#3b4252,hl:#81a1c1
  --color=fg+:#e5e9f0,bg+:#434c5e,hl+:#81a1c1
  --color=info:#eacb8a,prompt:#bf6069,pointer:#b48dac
  --color=marker:#a3be8b,spinner:#b48dac,header:#a3be8b
  --border=rounded
'
```

### Monokai Theme

```bash
export FZF_DEFAULT_OPTS='
  --color=fg:#f8f8f2,bg:#272822,hl:#66d9ef
  --color=fg+:#f8f8f2,bg+:#44475a,hl+:#ff79c6
  --color=info:#e6db74,prompt:#a6e22e,pointer:#ae81ff
  --color=marker:#a6e22e,spinner:#ae81ff,header:#75715e
  --border=rounded
'
```

---

## 🐛 Troubleshooting

### Common Issues

#### **1. Command not found**
```bash
# Verify installations
which fd rg fzf

# Reinstall if needed
pkg install fzf ripgrep fd
```

#### **2. Preview not working**
```bash
# Install bat for better previews
pkg install bat

# Verify bat works
bat --version

# Test preview
echo "test" | fzf --preview 'bat --color=always {}'
```

#### **3. Colors not showing**
```bash
# Enable colors
export FZF_DEFAULT_OPTS='--ansi'

# For ripgrep
rg --color=always "pattern"

# For fd
fd --color=always
```

#### **4. Slow performance**
```bash
# Reduce search depth
fd --max-depth 3

# Exclude large directories
fd --exclude node_modules --exclude .git

# Use .gitignore
# Both fd and rg respect .gitignore by default
```

#### **5. Keybindings not working**
```bash
# Source key-bindings
source /data/data/com.termux/files/usr/share/fzf/key-bindings.bash

# Or for zsh
source /data/data/com.termux/files/usr/share/fzf/key-bindings.zsh

# Add to shell RC file
echo 'source /data/data/com.termux/files/usr/share/fzf/key-bindings.bash' >> ~/.bashrc
```

---

## 📊 Cheat Sheet

```
╔═══════════════════════════════════════════════════════════════╗
║                    FZF + RG + FD CHEAT SHEET                  ║
╠═══════════════════════════════════════════════════════════════╣
║ FD (FILE FINDER)                                              ║
║ fd [pattern]              → Find files matching pattern       ║
║ fd -e ext                 → Find files with extension         ║
║ fd -t d                   → Find directories only             ║
║ fd -t f                   → Find files only                   ║
║ fd -H                     → Include hidden files              ║
║ fd --exclude pattern      → Exclude pattern                   ║
║ fd --max-depth N          → Limit depth                       ║
╠═══════════════════════════════════════════════════════════════╣
║ RIPGREP (CONTENT SEARCH)                                      ║
║ rg pattern                → Search for pattern                ║
║ rg -i pattern             → Case-insensitive                  ║
║ rg -w pattern             → Match whole words                 ║
║ rg -t type pattern        → Search specific file type         ║
║ rg -l pattern             → Show filenames only               ║
║ rg -n pattern             → Show line numbers                 ║
║ rg -C 3 pattern           → Show 3 lines context              ║
║ rg --hidden pattern       → Include hidden files              ║
╠═══════════════════════════════════════════════════════════════╣
║ FZF (FUZZY FINDER)                                            ║
║ fzf                       → Basic fuzzy find                  ║
║ fzf -m                    → Multi-select mode                 ║
║ fzf --preview 'cmd'       → Show preview                      ║
║ fzf --height 40%          → Set height                        ║
║ fzf --reverse             → Reverse layout                    ║
║ fzf --ansi                → Enable ANSI colors                ║
╠═══════════════════════════════════════════════════════════════╣
║ COMBINATIONS                                                  ║
║ fd | fzf                  → Interactive file finder           ║
║ rg . | fzf                → Interactive content search        ║
║ fd -t d | fzf             → Interactive directory picker      ║
║ fd | fzf --preview 'bat {}'  → File finder with preview      ║
║ rg -l pat | fzf --preview 'bat {}'  → Search with preview    ║
╠═══════════════════════════════════════════════════════════════╣
║ KEYBINDINGS (in fzf)                                          ║
║ ↑/↓ or Ctrl-J/K           → Navigate                          ║
║ TAB                       → Select (multi-mode)               ║
║ Shift-TAB                 → Deselect                          ║
║ Enter                     → Confirm                           ║
║ Ctrl-C / ESC              → Exit                              ║
║ Ctrl-/                    → Toggle preview                    ║
║ Ctrl-U/D                  → Preview scroll                    ║
╠═══════════════════════════════════════════════════════════════╣
║ SHELL KEYBINDINGS                                             ║
║ Ctrl-T                    → File finder                       ║
║ Ctrl-R                    → History search                    ║
║ Alt-C                     → Directory jump                    ║
╚═══════════════════════════════════════════════════════════════╝
```

---

## 📚 Additional Resources

### Official Documentation
- **fzf:** https://github.com/junegunn/fzf
- **ripgrep:** https://github.com/BurntSushi/ripgrep
- **fd:** https://github.com/sharkdp/fd

### Complementary Tools
- **bat:** https://github.com/sharkdp/bat (Better cat with syntax highlighting)
- **exa:** https://github.com/ogham/exa (Modern ls replacement)
- **delta:** https://github.com/dandavison/delta (Better git diff)
- **tokei:** https://github.com/XAMPPRocky/tokei (Code statistics)

### Learning Resources
- fzf wiki examples: https://github.com/junegunn/fzf/wiki/examples
- ripgrep user guide: https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md
- fd user guide: https://github.com/sharkdp/fd#tutorial

---

## 🎯 Quick Start Guide

### For Complete Beginners

**Step 1: Test Each Tool**
```bash
# Test fd
fd .md                    # Find all markdown files

# Test rg
rg "TODO"                 # Find all TODOs in your code

# Test fzf
echo -e "apple\nbanana\ncherry" | fzf    # Try fuzzy search
```

**Step 2: Combine Two Tools**
```bash
# fd + fzf
fd -e js | fzf

# rg + fzf
rg -l "function" | fzf
```

**Step 3: Add Preview**
```bash
# Install bat first
pkg install bat

# Then try with preview
fd | fzf --preview 'bat --color=always {}'
```

**Step 4: Use Pre-made Functions**
```bash
# Copy the functions from this guide to ~/.bashrc
# Then use them:
fe          # Find and edit file
fc          # Find by content and edit
fcd         # Jump to directory
```

### For Intermediate Users

Focus on these high-impact functions:
- `fe` - Daily file editing
- `fc` - Content-based file finding
- `fcd` - Quick directory navigation
- `frg` - Interactive search
- `fgb` - Git branch switching

### For Advanced Users

Create your own combinations:
- Integrate with your existing scripts
- Create project-specific search functions
- Build automation workflows
- Customize preview commands
- Create shell widgets

---

**This guide is current as of October 2025 and optimized for Termux.**

*Happy searching! 🎉*
```

---

# 🎓 Detailed Explanation

## How These Tools Work Together

### **The Pipeline Philosophy**

Unix/Linux tools follow a simple philosophy: do one thing well and chain tools together. Here's how our trinity embodies this:

```bash
# The Flow:
fd (finds files) → fzf (lets you choose) → bat (shows content)

# In practice:
fd --type f | fzf --preview 'bat --color=always {}'
```

### **Real Example Breakdown**

Let's break down this powerful command:

```bash
rg --line-number --color=always . | \
  fzf --ansi \
      --delimiter : \
      --preview 'bat --color=always {1} --highlight-line {2}' \
      --preview-window '+{2}+3/3'
```

**What's happening:**

1. **`rg --line-number --color=always .`**
   - Search all files in current directory
   - Show line numbers
   - Keep colors in output

2. **`fzf --ansi`**
   - Accept colored input from rg
   - Make it interactive

3. **`--delimiter :`**
   - Split results at `:` character
   - Results look like: `file.js:42:function hello()`

4. **`--preview 'bat --color=always {1} --highlight-line {2}'`**
   - Show the file (`{1}` = filename)
   - Highlight the line (`{2}` = line number)

5. **`--preview-window '+{2}+3/3'`**
   - Jump to the line in preview
   - Show 3 lines of context

### **Why It's Fast**

```bash
# Traditional approach (slow):
find . -name "*.js" -exec grep -l "function" {} \;
# Problems: Spawns new process for each file, no parallelism

# Modern approach (fast):
rg -t js "function"
# Advantages: Single process, parallel search, skips binaries
```

## Daily Workflow Examples

### **Morning Developer Routine**

```bash
# 1. Check project status
cd ~/projects
fd --type d --max-depth 1 | fzf | xargs -I {} cd {}

# 2. See what you were working on
rg "TODO|FIXME" | fzf

# 3. Quick git status
git status

# 4. Start working
fe    # Find and edit file
```

### **Code Review Workflow**

```bash
# 1. Find recently changed files
git diff --name-only | fzf --preview 'git diff --color=always {}'

# 2. Search for specific patterns
rg "console.log" | fzf --preview 'bat --color=always {1} --highlight-line {2}'

# 3. Check for todos
rg "TODO" -A 2 | fzf
```

### **Debugging Workflow**

```bash
# 1. Find error logs
fd -e log | fzf --preview 'tail -50 {} | bat -l log --color=always'

# 2. Search for errors
rg "error|exception" $(fd -e log) | fzf

# 3. Check stack traces
rg "at.*\.js:" | fzf --preview 'bat --color=always {1}'
```

## Performance Tips Explained

### **Why These Are Fast**

1. **fd is faster than find because:**
   - Written in Rust (compiled, not interpreted)
   - Parallel execution by default
   - Ignores .git and respects .gitignore
   - Doesn't stat files unnecessarily

2. **rg is faster than grep because:**
   - Uses finite automata for regex
   - Memory-mapped file reading
   - Parallel search across files
   - Skips binary files automatically
   - Respects .gitignore

3. **fzf is fast because:**
   - Written in Go (compiled)
   - Efficient fuzzy matching algorithm
   - Async preview generation
   - Minimal memory footprint

### **Optimization Strategies**

```bash
# Bad: Search everything
fd | rg "pattern"

# Good: Limit scope
fd -e js -e py --max-depth 3 | rg "pattern"

# Better: Use type filters
rg -t javascript -t python "pattern"

# Best: Combine with exclusions
rg -t js "pattern" --glob "!node_modules/*" --glob "!dist/*"
```

## Customization Philosophy

### **Start Simple, Add Complexity**

```bash
# Level 1: Basic usage
fd | fzf

# Level 2: Add preview
fd | fzf --preview 'cat {}'

# Level 3: Better preview
fd | fzf --preview 'bat --color=always {}'

# Level 4: Full featured
fd --type f --hidden --follow --exclude .git | \
  fzf --preview 'bat --color=always --style=full {}' \
      --preview-window 'right:60%:border-left' \
      --bind 'ctrl-/:toggle-preview'
```

### **Build Your Own Functions**

Start with what you need most:

```bash
# If you edit files a lot:
fe() { vim $(fd | fzf --preview 'bat --color=always {}') }

# If you search code a lot:
fs() { rg -l "$1" | fzf --preview 'rg --color=always "$1" {}' }

# If you switch directories often:
fcd() { cd $(fd -t d | fzf --preview 'ls -lah {}') }
```

---

## 🎯 Practice Exercises

Try these to master the trinity:

### **Exercise 1: File Hunter**
```bash
# Find all JavaScript files modified in last 7 days
fd -e js --changed-within 7d | fzf --preview 'bat {}'
```

### **Exercise 2: Code Detective**
```bash
# Find all functions in your codebase
rg "^(function|def|class)" | fzf --preview 'bat --color=always {1}'
```

### **Exercise 3: Project Navigator**
```bash
# Create a project switcher
cd ~/projects && cd $(fd --type d --max-depth 1 | fzf --preview 'ls -lah {}')
```

### **Exercise 4: Todo Manager**
```bash
# Find and fix todos
rg "TODO" | fzf --preview 'bat --color=always {1} --highlight-line {2}' | cut -d: -f1 | xargs $EDITOR
```

---

**Sources:**
1. fzf Official Repository: https://github.com/junegunn/fzf
2. ripgrep Official Repository: https://github.com/BurntSushi/ripgrep
3. fd Official Repository: https://github.com/sharkdp/fd
4. fzf Wiki and Examples: https://github.com/junegunn/fzf/wiki
5. ripgrep User Guide: https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md