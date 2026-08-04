--- 
title: "fzf"
description: "readme"
date: 2025-10-28T14:08:52Z
lastmod: 2025-10-28T14:08:52Z
tags: ["Sort"]
draft: false
---

# 📁 **FZF_COMPLETE_GUIDE.md**


# 🚀 FZF (Fuzzy Finder) - Complete Guide for Termux (2025)

> **Last Updated:** October 2025  
> **Official Repository:** https://github.com/junegunn/fzf  
> **Version:** Latest stable (0.55+)

## 📋 Table of Contents
- [What is fzf?](#what-is-fzf)
- [Basic Usage](#basic-usage)
- [Essential Daily Commands](#essential-daily-commands)
- [Key Bindings](#key-bindings)
- [Advanced Usage](#advanced-usage)
- [Preview Window](#preview-window)
- [Integration Examples](#integration-examples)
- [Customization](#customization)
- [Termux-Specific Tips](#termux-specific-tips)

---

## 🎯 What is fzf?

**fzf** is a blazingly fast, general-purpose command-line fuzzy finder. It's an interactive Unix filter for command-line that can be used with any list: files, command history, processes, hostnames, bookmarks, git commits, etc.

**Key Features:**
- ⚡ Extremely fast (written in Go)
- 🔍 Fuzzy search algorithm
- 🎨 Highly customizable
- 🔌 Works with any shell (bash, zsh, fish)
- 📱 Perfect for Termux on Android

---

## 🔰 Basic Usage

### Simple File Search
```bash
# Search and select files in current directory
fzf

# Search files and open with vim
vim $(fzf)

# Search files with preview
fzf --preview 'cat {}'
```

### Pipe Input to fzf
```bash
# Find and cd into a directory
cd $(find . -type d | fzf)

# Kill a process
kill -9 $(ps aux | fzf | awk '{print $2}')

# Search command history
history | fzf
```

---

## 💼 Essential Daily Commands

### 1. **File Navigation**
```bash
# Quick file finder with preview
find . -type f | fzf --preview 'cat {}' --preview-window=right:50%

# Open file with default editor
$EDITOR $(fzf)

# Multi-select files (use TAB to select multiple)
fzf -m

# Search hidden files too
find . | fzf
```

### 2. **Directory Navigation**
```bash
# Jump to any directory
cd $(find ~ -type d | fzf)

# Quick directory jumper (create alias)
alias cdf='cd $(find . -type d | fzf)'

# Navigate recent directories
dirs -v | fzf | cut -f2 | xargs -I {} cd {}
```

### 3. **Command History Search**
```bash
# Search and execute from history
eval $(history | fzf | sed 's/^[ ]*[0-9]*[ ]*//')

# Better with built-in binding (CTRL-R)
# Just press CTRL-R in terminal
```

### 4. **Process Management**
```bash
# Find and kill process
ps aux | fzf | awk '{print $2}' | xargs kill -9

# Interactive process viewer
ps aux | fzf --preview 'echo {}' --preview-window=down:3:wrap

# Find process by name
alias pskill='ps aux | fzf -m | awk "{print \$2}" | xargs kill -9'
```

### 5. **Git Integration**
```bash
# Checkout git branch
git branch | fzf | xargs git checkout

# View and checkout git commits
git log --oneline | fzf --preview 'git show {1}' | cut -d' ' -f1 | xargs git checkout

# Add files interactively
git status -s | fzf -m | awk '{print $2}' | xargs git add

# Interactive git log
alias glog='git log --oneline --color=always | fzf --ansi --preview "git show {1}"'

# Browse git commits with diff preview
alias gshow='git log --oneline | fzf --preview "git show --color=always {1}"'
```

### 6. **Package Management (Termux)**
```bash
# Search and install packages
pkg search '' | fzf | cut -d'/' -f1 | xargs pkg install

# List installed packages
pkg list-installed | fzf

# Search package descriptions
pkg search '' | fzf --preview 'pkg show {1}'
```

### 7. **Text File Content Search**
```bash
# Search file contents with ripgrep/grep
rg --line-number . | fzf --delimiter : --preview 'bat --color=always {1} --highlight-line {2}'

# Or with grep
grep -r "pattern" . | fzf

# Search and edit file at specific line
$EDITOR $(grep -rn "search" . | fzf | cut -d':' -f1-2 | tr ':' ' ')
```

### 8. **Clipboard/Bookmarks**
```bash
# Create a bookmark manager
alias bm='cat ~/.bookmarks | fzf | xargs -I {} termux-open-url {}'

# Command snippets manager
cat ~/.snippets | fzf | xargs -I {} termux-clipboard-set {}
```

---

## ⌨️ Key Bindings

fzf provides these default shell keybindings (after proper setup):

### Default Keybindings in Terminal
```bash
CTRL-T    # Paste selected files/directories onto command line
CTRL-R    # Search command history
ALT-C     # cd into selected directory
```

### Inside fzf Interface
```bash
# Navigation
↑ / ↓ or CTRL-J / CTRL-K    # Move up/down
CTRL-U / CTRL-D              # Scroll preview up/down
PGUP / PGDN                  # Page up/down

# Selection
TAB                          # Select/deselect (multi-mode)
SHIFT-TAB                    # Deselect
CTRL-A                       # Select all
CTRL-D                       # Deselect all

# Actions
ENTER                        # Confirm selection
ESC / CTRL-C                 # Exit
CTRL-Q                       # Exit (alternative)

# Search
CTRL-/                       # Toggle preview window
CTRL-\                       # Toggle preview wrap
```

---

## 🔥 Advanced Usage

### Multi-Selection
```bash
# Select multiple files (use TAB to select)
fzf -m

# Delete multiple files
find . -type f | fzf -m | xargs rm

# Copy multiple files
find . -type f | fzf -m | xargs -I {} cp {} /destination/
```

### Custom Preview Commands
```bash
# Preview with bat (syntax highlighting)
fzf --preview 'bat --color=always --style=numbers {}'

# Preview images (if supported)
fzf --preview 'termux-open {}'

# Preview with file info
fzf --preview 'ls -lah {}'

# Preview directories
find . -type d | fzf --preview 'ls -lah {}'
```

### Exact Match and Operators
```bash
# Exact match (prefix with ')
fzf --query "'exact

# Operators in search:
# 'word    # Exact match
# ^word    # Prefix match
# word$    # Suffix match
# !word    # Inverse match
```

### Custom Height and Layout
```bash
# Compact mode (40% height)
fzf --height 40%

# Reverse layout (results on top)
fzf --reverse

# No border
fzf --border=none

# Custom border style
fzf --border=rounded
```

---

## 🖼️ Preview Window

### Basic Preview
```bash
# Right side preview
fzf --preview 'cat {}'

# Bottom preview
fzf --preview 'cat {}' --preview-window=down

# Preview with specific size
fzf --preview 'cat {}' --preview-window=right:60%

# Hidden by default (toggle with CTRL-/)
fzf --preview 'cat {}' --preview-window=hidden
```

### Advanced Preview
```bash
# Preview with line numbers and syntax highlighting
fzf --preview 'bat --style=numbers --color=always {}'

# Preview with custom command
fzf --preview 'head -100 {}'

# Conditional preview (files vs directories)
fzf --preview '[ -f {} ] && bat {} || ls -lah {}'
```

---

## 🔗 Integration Examples

### 1. Ultimate File Finder Function
```bash
# Add to ~/.bashrc or ~/.zshrc
ff() {
    local file
    file=$(find ${1:-.} -type f 2>/dev/null | fzf --preview 'bat --color=always --style=numbers {}' --preview-window=right:60%) && $EDITOR "$file"
}
```

### 2. Smart Directory Jumper
```bash
# Jump to frequent directories
fd() {
    local dir
    dir=$(find ${1:-~} -type d 2>/dev/null | fzf --preview 'ls -lah {}' --preview-window=right:50%) && cd "$dir"
}
```

### 3. Git Branch Switcher
```bash
gb() {
    local branch
    branch=$(git branch --all | grep -v HEAD | sed 's/^..//' | fzf --preview 'git log --oneline --graph --color=always {}' --preview-window=right:60%) && git checkout "$branch"
}
```

### 4. Enhanced Kill Process
```bash
fkill() {
    local pid
    pid=$(ps aux | sed 1d | fzf -m --preview 'echo {}' --preview-window=down:3:wrap | awk '{print $2}')
    if [ -n "$pid" ]; then
        echo "$pid" | xargs kill -${1:-9}
    fi
}
```

### 5. Package Search & Install (Termux)
```bash
pki() {
    pkg search '' | fzf -m --preview 'pkg show {1}' --preview-window=right:60% | cut -d'/' -f1 | xargs pkg install
}
```

### 6. Notes Finder
```bash
# Search notes by content
note() {
    local notes_dir="$HOME/notes"
    grep -r "" "$notes_dir" 2>/dev/null | fzf --delimiter : --preview 'bat --color=always {1}' | cut -d: -f1 | xargs $EDITOR
}
```

---

## 🎨 Customization

### Color Schemes
```bash
# Monokai theme
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#272822,hl:#66d9ef --color=fg+:#f8f8f2,bg+:#44475a,hl+:#ff79c6'

# Nord theme
export FZF_DEFAULT_OPTS='--color=fg:#e5e9f0,bg:#3b4252,hl:#81a1c1 --color=fg+:#e5e9f0,bg+:#434c5e,hl+:#81a1c1'

# Dracula theme
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#282a36,hl:#bd93f9 --color=fg+:#f8f8f2,bg+:#44475a,hl+:#bd93f9'
```

### Default Options
Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Set default command (use fd if available, fallback to find)
export FZF_DEFAULT_COMMAND='find . -type f'

# Or with fd (faster):
# export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'

# Default options
export FZF_DEFAULT_OPTS='
  --height 40%
  --layout=reverse
  --border=rounded
  --info=inline
  --preview "bat --color=always --style=numbers {}"
  --preview-window=right:60%:wrap
  --bind="ctrl-/:toggle-preview"
  --bind="ctrl-u:preview-up,ctrl-d:preview-down"
'

# CTRL-T options (file finder)
export FZF_CTRL_T_OPTS="
  --preview 'bat --color=always --style=numbers {}'
  --preview-window=right:60%
"

# ALT-C options (directory finder)
export FZF_ALT_C_OPTS="
  --preview 'ls -lah {}'
  --preview-window=right:60%
"
```

---

## 📱 Termux-Specific Tips

### 1. Enable Key Bindings in Termux
```bash
# For bash (add to ~/.bashrc)
source /data/data/com.termux/files/usr/share/fzf/key-bindings.bash
source /data/data/com.termux/files/usr/share/fzf/completion.bash

# For zsh (add to ~/.zshrc)
source /data/data/com.termux/files/usr/share/fzf/key-bindings.zsh
source /data/data/com.termux/files/usr/share/fzf/completion.zsh
```

### 2. Open Files with Android Apps
```bash
# Open with default app
fzf | xargs -I {} termux-open {}

# Open URLs from bookmarks
cat ~/bookmarks.txt | fzf | xargs termux-open-url
```

### 3. Storage Access
```bash
# Navigate Termux storage
cd ~/storage && find . | fzf | xargs termux-open

# Search in shared storage
alias fshare='cd ~/storage/shared && fzf --preview "bat {}" | xargs termux-open'
```

### 4. Clipboard Integration
```bash
# Copy selected file path to clipboard
fzf | xargs -I {} termux-clipboard-set {}

# Search and copy file content
cat $(fzf) | termux-clipboard-set
```

### 5. Useful Termux Aliases
```bash
# Add to ~/.bashrc or ~/.zshrc

# Quick file opener
alias fo='fzf | xargs termux-open'

# Navigate and open
alias nv='cd $(find ~/storage -type d | fzf) && ls'

# Edit config files
alias conf='cd ~/.config && $EDITOR $(fzf)'

# Search downloads
alias dl='cd ~/storage/downloads && fzf --preview "bat {}" | xargs termux-open'
```

---

## 🛠️ Useful Aliases Collection

Add these to your `~/.bashrc` or `~/.zshrc`:

```bash
# File operations
alias fv='vim $(fzf)'                                    # Find and edit
alias fcd='cd $(find . -type d | fzf)'                   # Find and cd
alias frm='find . -type f | fzf -m | xargs rm -i'        # Find and remove

# Git shortcuts
alias gb='git branch | fzf | xargs git checkout'         # Checkout branch
alias gf='git ls-files | fzf | xargs $EDITOR'            # Edit git file
alias gs='git status -s | fzf'                           # Interactive status

# Process management
alias pk='ps aux | fzf | awk "{print \$2}" | xargs kill' # Kill process
alias psg='ps aux | fzf'                                 # Search processes

# System
alias h='history | fzf'                                  # Search history
alias env='printenv | fzf'                               # Search environment

# Termux specific
alias apk='pkg search "" | fzf | cut -d"/" -f1 | xargs pkg install'
alias stor='cd ~/storage && fzf | xargs termux-open'
```

---

## 📚 Real-World Usage Examples

### Example 1: Quick Project Navigation
```bash
# Jump to any project directory
proj() {
    cd ~/projects
    cd $(find . -maxdepth 2 -type d | fzf --preview 'ls -lah {}')
}
```

### Example 2: Config File Manager
```bash
# Edit any config file quickly
config() {
    local config_dirs="$HOME/.config $HOME"
    find $config_dirs -type f -name ".*" -o -name "*.conf" | \
    fzf --preview 'bat --color=always {}' | \
    xargs $EDITOR
}
```

### Example 3: Log File Viewer
```bash
# View log files with live preview
viewlog() {
    find /var/log ~/storage -type f -name "*.log" 2>/dev/null | \
    fzf --preview 'tail -100 {} | bat --color=always -l log' \
        --preview-window=right:60%:wrap
}
```

### Example 4: Script Runner
```bash
# Find and execute scripts
runscript() {
    find ~/scripts -type f -executable | \
    fzf --preview 'bat --color=always {}' | \
    xargs -I {} bash {}
}
```

### Example 5: SSH Connection Manager
```bash
# Quick SSH from config
sshto() {
    grep "^Host " ~/.ssh/config | \
    sed 's/Host //' | \
    fzf --preview 'grep -A 5 "Host {}" ~/.ssh/config' | \
    xargs -I {} ssh {}
}
```

---

## 🎓 Tips & Tricks

### 1. Combine with Other Tools
```bash
# With ripgrep
rg --files | fzf --preview 'bat --color=always {}'

# With fd (faster find alternative)
fd --type f | fzf --preview 'bat {}'

# With tree
tree -L 2 | fzf
```

### 2. Performance Tips
```bash
# Limit depth for faster searches
find . -maxdepth 3 | fzf

# Exclude directories
find . -not -path "*/node_modules/*" -not -path "*/.git/*" | fzf

# Use fd for better performance
fd --type f --exclude node_modules --exclude .git | fzf
```

### 3. Creating Command Palettes
```bash
# Personal command palette
alias commands='echo "git status
git pull
git push
pkg update
pkg upgrade
termux-setup-storage" | fzf | bash'
```

---

## 🔍 Troubleshooting

### Key bindings not working?
```bash
# Ensure fzf key-bindings are sourced
echo "source /data/data/com.termux/files/usr/share/fzf/key-bindings.bash" >> ~/.bashrc
source ~/.bashrc
```

### Preview not showing?
```bash
# Install bat for better preview
pkg install bat

# Or use cat as fallback
fzf --preview 'cat {}'
```

### Slow performance?
```bash
# Limit search depth
find . -maxdepth 3 | fzf

# Use fd instead of find
pkg install fd
fd --type f | fzf
```

---

## 📖 Additional Resources

- **Official GitHub:** https://github.com/junegunn/fzf
- **Wiki:** https://github.com/junegunn/fzf/wiki
- **Examples:** https://github.com/junegunn/fzf/wiki/examples
- **Termux Wiki:** https://wiki.termux.com

---

## 🎯 Quick Reference Card

```
SEARCH SYNTAX:
'word     → Exact match
^word     → Prefix match
word$     → Suffix match
!word     → Inverse match
|         → OR operator

KEY BINDINGS:
CTRL-T    → File finder
CTRL-R    → History search
ALT-C     → Directory jump
TAB       → Select multiple
ENTER     → Confirm
ESC       → Exit
CTRL-/    → Toggle preview

COMMON PATTERNS:
vim $(fzf)                    → Edit file
cd $(find . -type d | fzf)    → Change directory
kill $(ps aux | fzf | ...)    → Kill process
git checkout $(git branch | fzf)  → Switch branch
```

---

**Happy Fuzzy Finding! 🎉**

*This guide is updated for 2025 and optimized for Termux on Android devices.*

---

## Source Citations

1. **Official fzf Repository**: https://github.com/junegunn/fzf
2. **fzf Wiki**: https://github.com/junegunn/fzf/wiki
3. **Termux Documentation**: https://wiki.termux.com
```

---

# 📖 How to Use This Guide

## For Beginners - Start Here:

### **Step 1: Basic Navigation**
Just type `fzf` in your terminal. You'll see a list of files. Start typing to search, use arrow keys to navigate, and press Enter to select.

```bash
fzf
```

### **Step 2: Try the Magic Keybindings**
These work anywhere in your terminal:

1. **`CTRL-R`** - Press this and start typing any command you've used before. It searches your entire command history!
   
2. **`CTRL-T`** - Press this to search for files and automatically paste the file path where your cursor is.

3. **`ALT-C`** - Press this to quickly jump to any directory.

### **Step 3: Use with Commands**
Try these examples:

```bash
# Open any file with vim
vim $(fzf)

# Delete a file (it will ask before deleting)
rm -i $(fzf)

# View a file
cat $(fzf)

# Copy a file
cp $(fzf) /destination/
```

## For Intermediate Users:

### **Multi-Selection**
Hold `TAB` to select multiple files, then press Enter:

```bash
# Delete multiple files
find . -type f | fzf -m | xargs rm -i

# Copy multiple files
find . -type f | fzf -m | xargs -I {} cp {} ~/backup/
```

### **Preview Window**
See file contents before selecting:

```bash
# Install bat first for syntax highlighting
pkg install bat

# Then use preview
fzf --preview 'bat --color=always {}'
```

## For Advanced Users:

### **Create Your Own Functions**
Add these to `~/.bashrc`:

```bash
# Ultimate file finder with preview
ff() {
    local file
    file=$(find ${1:-.} -type f | fzf --preview 'bat --color=always --style=numbers {}' --preview-window=right:60%) && $EDITOR "$file"
}

# Git branch switcher with preview
gb() {
    git branch --all | grep -v HEAD | sed 's/^..//' | fzf --preview 'git log --oneline --graph --color=always {}' | xargs git checkout
}
```

### **Customize Appearance**
Add to `~/.bashrc`:

```bash
export FZF_DEFAULT_OPTS='
  --height 40%
  --layout=reverse
  --border=rounded
  --preview "bat --color=always --style=numbers {}"
  --bind="ctrl-/:toggle-preview"
'
```

---

## 🎯 Daily Workflow Examples

### **Morning Routine:**
```bash
# Check and update packages
pkg list-installed | fzf

# Navigate to project
cd ~/projects && cd $(find . -maxdepth 2 -type d | fzf)

# Check git status
git status
```

### **Development Workflow:**
```bash
# Quick edit files
vim $(fzf)

# Switch git branches
git branch | fzf | xargs git checkout

# Search code
rg "function" | fzf
```

### **File Management:**
```bash
# Find and open files
fzf | xargs termux-open

# Organize downloads
cd ~/storage/downloads && fzf -m | xargs mv -t ~/organized/
```

---

This markdown file contains everything you need to master fzf in Termux. Start with the basics, experiment with the examples, and gradually incorporate the advanced features into your daily workflow. The key is to practice with the `CTRL-R`, `CTRL-T`, and `ALT-C` keybindings - they'll transform how you work in the terminal!

**Sources:**
- Official fzf GitHub: https://github.com/junegunn/fzf
- fzf Wiki: https://github.com/junegunn/fzf/wiki  
- Termux documentation and community resources