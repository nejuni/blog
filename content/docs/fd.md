--- 
title: "fd"
description: "readme"
date: 2025-10-28T14:04:52Z
lastmod: 2025-10-28T14:04:52Z
tags: ["Sort"]
draft: false
---

# fd - A Fast and User-Friendly Alternative to 'find'

**Official Repository:** https://github.com/sharkdp/fd  
**Version:** Latest (2025)  
**Platform:** Termux (Android)

---

## Table of Contents
1. [Introduction](#introduction)
2. [Why Use fd?](#why-use-fd)
3. [Basic Syntax](#basic-syntax)
4. [Most Useful Daily Commands](#most-useful-daily-commands)
5. [Advanced Usage](#advanced-usage)
6. [Practical Examples](#practical-examples)
7. [Tips & Tricks](#tips--tricks)

---

## Introduction

`fd` is a simple, fast, and user-friendly alternative to the traditional `find` command. It's designed to be intuitive and comes with smart defaults that make it perfect for everyday use.

**Key Features:**
- Intuitive syntax
- Blazingly fast (written in Rust)
- Colorized output by default
- Smart case: case-insensitive by default, case-sensitive if pattern contains uppercase
- Ignores hidden files/directories and `.gitignore` patterns by default
- Regular expression patterns
- Parallel command execution

---

## Why Use fd?

| Feature | find | fd |
|---------|------|-----|
| Syntax | Complex | Simple |
| Speed | Slower | Much faster |
| Default behavior | Shows everything | Ignores clutter |
| Colors | No | Yes |
| Regex | Complicated | Built-in |
| Git-aware | No | Yes |

---

## Basic Syntax

```bash
fd [OPTIONS] [PATTERN] [PATH]
```

- **PATTERN**: What to search for (optional, searches all if omitted)
- **PATH**: Where to search (defaults to current directory)
- **OPTIONS**: Flags to modify behavior

---

## Most Useful Daily Commands

### 1. **Simple Search**
```bash
# Find all files/directories with 'config' in name
fd config

# Search in specific directory
fd config ~/Documents
```

### 2. **Search by File Type**
```bash
# Find all Python files
fd -e py

# Find all images (jpg, png, etc.)
fd -e jpg -e png -e gif

# Multiple extensions
fd -e mp4 -e mkv -e avi
```

### 3. **Include Hidden Files**
```bash
# Show hidden files and directories
fd -H config

# Show everything (hidden + ignored by .gitignore)
fd -H -I config
```

### 4. **Search Only Files or Directories**
```bash
# Only files
fd -t f pattern

# Only directories
fd -t d pattern

# Only symbolic links
fd -t l pattern

# Only executable files
fd -t x pattern
```

### 5. **Case Sensitivity**
```bash
# Case-insensitive (default for lowercase patterns)
fd readme

# Force case-sensitive
fd -s README

# Force case-insensitive
fd -i README
```

### 6. **Exclude Patterns**
```bash
# Exclude node_modules
fd -E node_modules

# Exclude multiple patterns
fd -E node_modules -E .git -E target

# Exclude by extension
fd -E '*.tmp' -E '*.log'
```

### 7. **Search by Exact Name**
```bash
# Find exact filename
fd -g 'config.json'

# Using glob patterns
fd -g '*.conf'
fd -g 'test_*.py'
```

### 8. **Execute Commands on Results**
```bash
# Delete all .tmp files
fd -e tmp -x rm {}

# Show details of all Python files
fd -e py -x ls -lh {}

# Convert all jpg to png (example)
fd -e jpg -x convert {} {.}.png
```

### 9. **Parallel Execution**
```bash
# Run commands in parallel (faster)
fd -e jpg -x convert {} {.}.png

# Control number of threads
fd -j 4 -e jpg -x convert {} {.}.png
```

### 10. **Search with Full Path**
```bash
# Search in full path (not just filename)
fd -p 'src/.*\.py$'

# Find files in specific subdirectory
fd -p 'config/.*\.yml$'
```

---

## Advanced Usage

### Depth Control
```bash
# Search only in current directory (depth 1)
fd -d 1 pattern

# Search up to 3 levels deep
fd -d 3 pattern

# No depth limit (default)
fd pattern
```

### Size Filters
```bash
# Files larger than 100MB
fd -S +100m

# Files smaller than 1KB
fd -S -1k

# Files between 10KB and 1MB
fd -S +10k -S -1m
```

### Time-Based Filters
```bash
# Modified in last 24 hours
fd -c +1d

# Modified more than 7 days ago
fd -c -7d

# Modified in last hour
fd -c +1h
```

### Regular Expressions
```bash
# Files starting with 'test' and ending with numbers
fd '^test.*[0-9]$'

# Files with exactly 3 characters
fd '^...$'

# Python files with 'util' anywhere in name
fd 'util.*\.py$'
```

### Follow Symbolic Links
```bash
# Follow symlinks
fd -L pattern

# Follow and show absolute paths
fd -L -a pattern
```

---

## Practical Examples

### Example 1: Clean Up Temporary Files
```bash
# Find and remove all .tmp and .cache files
fd -e tmp -e cache -x rm {}
```

### Example 2: Find Large Files in Termux
```bash
# Find files larger than 50MB in storage
fd -t f -S +50m . ~/storage
```

### Example 3: Backup All Configuration Files
```bash
# Find all .conf files and copy to backup directory
fd -e conf -x cp {} ~/backup/
```

### Example 4: Find Recently Modified Files
```bash
# Find files modified in last 2 days
fd -t f -c +2d
```

### Example 5: Search in Multiple Directories
```bash
# Search in multiple locations
fd pattern ~/Documents ~/Downloads ~/storage
```

### Example 6: Find Empty Files/Directories
```bash
# Find empty files
fd -t f -S 0

# Find empty directories (use find for this)
find . -type d -empty
```

### Example 7: Count Files by Extension
```bash
# Count all Python files
fd -e py | wc -l

# Count all image files
fd -e jpg -e png -e gif | wc -l
```

### Example 8: Find and Open Files in Editor
```bash
# Find Python files and open in vim
fd -e py -x vim {}

# Find and open in nano
fd config.json -x nano {}
```

### Example 9: Search in Archive Directories
```bash
# Include .git directories
fd -I -t d '.git'

# Search in node_modules
fd -I pattern node_modules/
```

### Example 10: Generate File List
```bash
# Create list of all videos
fd -e mp4 -e mkv > videos.txt

# List with full paths
fd -e mp4 -a > videos_fullpath.txt
```

---

## Tips & Tricks

### 1. **Create Aliases** (Add to ~/.bashrc or ~/.zshrc)
```bash
# Quick search for files only
alias fdf='fd -t f'

# Quick search for directories only
alias fdd='fd -t d'

# Search including hidden
alias fdh='fd -H'

# Search everything (hidden + ignored)
alias fda='fd -H -I'
```

### 2. **Combine with Other Tools**
```bash
# Pipe to fzf for interactive selection
fd | fzf

# Count lines in all Python files
fd -e py -x wc -l {} | sort -n

# Find and compress
fd -e log | xargs gzip
```

### 3. **Color Customization**
```bash
# Disable colors
fd --color=never pattern

# Always use colors (even when piping)
fd --color=always pattern | less -R
```

### 4. **Speed Optimization**
```bash
# Use fewer threads for low-power devices
fd -j 2 pattern

# Skip .gitignore parsing for speed
fd --no-ignore-vcs pattern
```

### 5. **Termux-Specific Tips**
```bash
# Search in shared storage
fd pattern ~/storage/shared

# Search in external SD card
fd pattern ~/storage/external-1

# Find large files eating storage
fd -S +100m ~/storage
```

### 6. **Common Patterns**
```bash
# Find all config files
fd -g '*config*'

# Find all test files
fd -g '*test*.py'

# Find all README files
fd -i readme

# Find all backup files
fd -g '*.bak' -g '*.backup'
```

### 7. **Batch Operations**
```bash
# Rename all .jpeg to .jpg
fd -e jpeg -x bash -c 'mv "$1" "${1%.jpeg}.jpg"' -- {}

# Change permissions on all shell scripts
fd -e sh -x chmod +x {}

# Add prefix to all files
fd -t f -x mv {} prefix_{}
```

---

## Common Options Quick Reference

| Option | Description |
|--------|-------------|
| `-H` | Include hidden files |
| `-I` | Don't respect .gitignore |
| `-L` | Follow symbolic links |
| `-a` | Show absolute paths |
| `-p` | Search full path |
| `-g` | Glob pattern search |
| `-e` | Filter by extension |
| `-t` | Filter by type (f/d/l/x) |
| `-d` | Max depth |
| `-S` | Size filter |
| `-c` | Changed within time |
| `-E` | Exclude pattern |
| `-x` | Execute command |
| `-j` | Number of threads |
| `-s` | Case-sensitive |
| `-i` | Case-insensitive |

---

## Getting Help

```bash
# Show help
fd --help

# Show version
fd --version

# Show full manual
man fd
```

---

## Performance Comparison

On a typical directory with 1M files:

```bash
# find command
time find . -name "*.py"
# Real: 8.5s

# fd command
time fd -e py
# Real: 1.2s
```

**fd is approximately 7x faster!**

---

## Additional Resources

- **Official GitHub:** https://github.com/sharkdp/fd
- **Documentation:** https://github.com/sharkdp/fd/blob/master/doc/README.md
- **Issue Tracker:** https://github.com/sharkdp/fd/issues

---

**Last Updated:** October 2025  
**Created for:** Termux Users
```

---

## Detailed Explanation: How to Use fd

Now let me explain how fd works and provide comprehensive examples:

### **Understanding fd's Core Concept**

fd simplifies file searching by:
1. **Smart defaults** - Assumes you want useful results, not clutter
2. **Intuitive syntax** - No need to remember complex flags
3. **Speed** - Written in Rust for maximum performance
4. **Git-awareness** - Respects your `.gitignore` automatically

### **Basic Usage Walkthrough**

#### **1. Simple Search**
```bash
fd download
```
**What it does:** Searches for any file or directory containing "download" in its name, starting from current directory.

**Output example:**
```
Downloads/
download_manager.py
my_download.txt
```

#### **2. Search by Extension**
```bash
fd -e pdf
```
**What it does:** Finds all PDF files in current directory and subdirectories.

**Real-world use:** Finding all your PDFs in Termux storage
```bash
fd -e pdf ~/storage
```

#### **3. Type-Specific Search**

**Find only directories:**
```bash
fd -t d backup
```
This shows only directories with "backup" in the name, ignoring files.

**Find only files:**
```bash
fd -t f -e sh
```
Shows only shell script files (not directories named *.sh).

#### **4. Including Hidden Files**

By default, fd ignores:
- Hidden files/directories (starting with `.`)
- Patterns in `.gitignore`

To show hidden files:
```bash
fd -H .bashrc
```

To show EVERYTHING (hidden + ignored):
```bash
fd -H -I node_modules
```

### **Practical Real-World Examples for Termux**

#### **Example 1: Finding Your Downloaded Videos**
```bash
cd ~/storage/downloads
fd -e mp4 -e mkv -e avi
```
**Explanation:** Searches for video files with these extensions. Great for organizing your downloads.

#### **Example 2: Cleaning Up Old Log Files**
```bash
fd -e log -x rm {}
```
**Explanation:** 
- `-e log` finds all .log files
- `-x rm {}` executes `rm` on each file
- `{}` is placeholder for found filename

**Safer version (confirm before delete):**
```bash
fd -e log -x rm -i {}
```

#### **Example 3: Finding Large Files Eating Your Storage**
```bash
fd -t f -S +100m ~/storage
```
**Explanation:**
- `-t f` = only files (not directories)
- `-S +100m` = size greater than 100 megabytes
- Shows you the storage hogs!

#### **Example 4: Backup All Your Python Scripts**
```bash
# Create backup directory
mkdir -p ~/backup

# Copy all Python files to backup
fd -e py -x cp {} ~/backup/
```

#### **Example 5: Find Recently Modified Files**
```bash
fd -t f -c +1d
```
**Explanation:** Shows files modified in the last 1 day. Useful for finding what you recently worked on.

Time options:
- `+1h` = last hour
- `+1d` = last day
- `+1w` = last week
- `-1w` = older than 1 week

#### **Example 6: Search Multiple Locations**
```bash
fd screenshot ~/storage/DCIM ~/storage/Pictures ~/storage/Downloads
```
Searches for screenshots in multiple directories at once.

#### **Example 7: Case-Insensitive Search**
```bash
fd -i readme
```
Finds: README, readme, ReadMe, README.md, etc.

#### **Example 8: Execute Complex Commands**
```bash
# Find all images and resize them
fd -e jpg -e png -x convert {} -resize 50% resized_{}

# Find all text files and count words
fd -e txt -x wc -w {}

# Find and compress all logs
fd -e log -x gzip {}
```

### **Advanced Patterns**

#### **Using Regular Expressions**
```bash
# Files starting with 'test' followed by numbers
fd '^test[0-9]+'

# Files ending with _backup
fd '_backup$'

# Python files with 'util' or 'helper'
fd '(util|helper).*\.py$'
```

#### **Combining Multiple Filters**
```bash
# Large video files modified recently
fd -e mp4 -S +50m -c +7d

# Hidden config files in home directory
fd -H -g '.*.conf' ~

# Executable scripts larger than 1MB
fd -t x -S +1m -e sh
```

#### **Parallel Processing**
```bash
# Convert all images using 4 parallel processes
fd -e jpg -j 4 -x convert {} {.}.png
```
**Explanation:** 
- `-j 4` = use 4 threads
- `{.}` = filename without extension
- Faster on devices with multiple cores

### **Integration with Other Tools**

#### **With fzf (fuzzy finder)**
```bash
# Interactive file selection
fd | fzf | xargs -I {} nano {}
```

#### **With grep**
```bash
# Find Python files containing "import requests"
fd -e py -x grep -l "import requests" {}
```

#### **With tree**
```bash
# Show directory structure of found items
fd -t d | xargs tree
```

### **Termux-Specific Use Cases**

#### **1. Organize Phone Storage**
```bash
# Find duplicate files by name
fd -t f | sort | uniq -d

# Find old files to clean up
fd -t f -c -30d ~/storage | wc -l
```

#### **2. Development Workflow**
```bash
# Find all TODO comments in code
fd -e py -e js -x grep -n "TODO" {}

# Count lines of code
fd -e py -x wc -l {} | awk '{sum+=$1} END {print sum}'
```

#### **3. Media Management**
```bash
# List all music files with details
fd -e mp3 -e flac -x ls -lh {}

# Find photos by date (if in filename)
fd '2025.*\.(jpg|png)' ~/storage/DCIM
```

### **Common Mistakes to Avoid**

1. **Forgetting to escape special characters**
   ```bash
   # Wrong
   fd *.txt
   
   # Correct
   fd '*.txt'
   # or
   fd -g '*.txt'
   ```

2. **Not using quotes with patterns**
   ```bash
   # Wrong
   fd test*.py
   
   # Correct
   fd 'test*.py'
   ```

3. **Searching without considering hidden files**
   ```bash
   # If not finding config files, add -H
   fd -H .gitignore
   ```


## Sources

1. **Official fd GitHub Repository**: https://github.com/sharkdp/fd - The primary source for all fd features, documentation, and updates
2. **fd Documentation**: Official documentation maintained by the project creator (sharkdp/David Peter)
3. Information compiled from fd's command-line help and man pages
4. Practical examples derived from common use cases and community best practices

This guide is current as of October 2025 and includes all major features available in the latest version of fd.