--- 
title: "rg"
description: "readme"
date: 2025-10-28T14:05:52Z
lastmod: 2025-10-28T14:05:52Z
tags: ["Sort"]
draft: false
---

# rg (ripgrep) - A Lightning-Fast Search Tool

**Official Repository:** https://github.com/BurntSushi/ripgrep  
**Version:** Latest (2025)  
**Platform:** Termux (Android)  
**Author:** Andrew Gallant (BurntSushi)

---

## Table of Contents
1. [Introduction](#introduction)
2. [Why Use ripgrep?](#why-use-ripgrep)
3. [Basic Syntax](#basic-syntax)
4. [Most Useful Daily Commands](#most-useful-daily-commands)
5. [Advanced Usage](#advanced-usage)
6. [Search Patterns & Regex](#search-patterns--regex)
7. [Practical Examples](#practical-examples)
8. [Configuration](#configuration)
9. [Tips & Tricks](#tips--tricks)
10. [Performance Optimization](#performance-optimization)

---

## Introduction

**ripgrep** (`rg`) is a line-oriented search tool that recursively searches your current directory for a regex pattern. It's designed to be faster than grep, ag (The Silver Searcher), and ack while respecting your gitignore rules.

**Key Features:**
- **Blazingly fast** - Often 5-10x faster than alternatives
- **Respects .gitignore** - Automatically skips ignored files
- **Smart defaults** - Recursive search, skip binary files
- **Cross-platform** - Works on Linux, macOS, Windows, Android (Termux)
- **Full regex support** - Perl-compatible regular expressions
- **Comprehensive file type filtering** - Built-in support for 100+ file types
- **Compressed file search** - Can search inside gzip, bzip2, xz, lz4, lzma, brotli, zstd
- **Automatic encoding detection** - Handles UTF-16, Latin-1, etc.
- **Multiline search** - Search across multiple lines
- **Replace functionality** - Find and replace with `-r`

---

## Why Use ripgrep?

| Feature | grep | ag (Silver Searcher) | rg (ripgrep) |
|---------|------|----------------------|--------------|
| Speed | Baseline | 2-5x faster | 5-10x faster |
| Git-aware | No | Yes | Yes |
| Config files | Limited | Yes | Yes (.ripgreprc) |
| Unicode support | Basic | Good | Excellent |
| Compressed files | No | No | Yes |
| Multiline search | Limited | No | Yes |
| Memory usage | Low | Medium | Low |
| File type filtering | Manual | Good | Excellent |

**Benchmark Example** (searching 1GB of code):
- `grep -r`: ~15 seconds
- `ag`: ~3 seconds  
- `rg`: ~0.8 seconds

---

## Basic Syntax

```bash
rg [OPTIONS] PATTERN [PATH...]
```

- **PATTERN**: What to search for (regex supported)
- **PATH**: Where to search (defaults to current directory)
- **OPTIONS**: Flags to modify search behavior

---

## Most Useful Daily Commands

### 1. **Basic Search**
```bash
# Search for 'function' in current directory
rg function

# Search in specific directory
rg function ~/Projects

# Search in specific file
rg function script.py
```

### 2. **Case Sensitivity**
```bash
# Case-insensitive search
rg -i function

# Smart case (insensitive unless pattern has uppercase)
rg -S function    # insensitive
rg -S Function    # sensitive

# Force case-sensitive
rg -s function
```

### 3. **File Type Filtering**
```bash
# Search only in Python files
rg -t py import

# Search in multiple file types
rg -t py -t js function

# Exclude file types
rg -T js function

# List all supported file types
rg --type-list
```

### 4. **Show Context**
```bash
# Show 3 lines before match
rg -B 3 error

# Show 3 lines after match
rg -A 3 error

# Show 3 lines before AND after (context)
rg -C 3 error

# Alternative syntax
rg --context 3 error
```

### 5. **Include Hidden and Ignored Files**
```bash
# Include hidden files (like .bashrc)
rg --hidden config

# Include ignored files (from .gitignore)
rg --no-ignore error

# Include both hidden and ignored
rg --hidden --no-ignore password

# Short form
rg -uu password   # -u once = --no-ignore, twice = --hidden --no-ignore
rg -uuu password  # three times = --no-ignore --hidden --binary
```

### 6. **Show Only Filenames**
```bash
# List files with matches
rg -l function

# List files WITHOUT matches
rg --files-without-match function

# Count matches per file
rg -c function
```

### 7. **Word Boundaries**
```bash
# Match whole words only
rg -w test    # matches 'test' but not 'testing'

# Case-insensitive whole word
rg -iw test
```

### 8. **Line Numbers and Colors**
```bash
# Show line numbers (default)
rg -n function

# Hide line numbers
rg -N function

# Show column numbers
rg --column function

# Disable colors
rg --color never function

# Force colors (useful for piping)
rg --color always function | less -R
```

### 9. **Search in Specific Files**
```bash
# Search in files matching glob pattern
rg function -g '*.py'

# Search in multiple patterns
rg function -g '*.py' -g '*.js'

# Exclude files matching pattern
rg function -g '!test*'

# Complex glob
rg function -g '**/src/**/*.py'
```

### 10. **Replace Text**
```bash
# Show what would be replaced (dry run)
rg old_function -r new_function

# Actually replace (save to new files)
rg old_function -r new_function --passthru > output.txt

# Replace in-place (use with caution!)
# Note: rg doesn't have built-in in-place replace, combine with sed
rg -l old_function | xargs sed -i 's/old_function/new_function/g'
```

---

## Advanced Usage

### Multiline Search
```bash
# Search across multiple lines
rg -U 'function.*\n.*return' 

# Match patterns spanning lines (dot matches newline)
rg -U 'START.*END'

# Multiline with context
rg -U -C 2 'import.*\n.*from'
```

### Fixed String Search (Literal)
```bash
# Treat pattern as literal string (not regex)
rg -F 'function()'

# Useful for searching special characters
rg -F '$variable[0]'
```

### Search with OR Logic
```bash
# Search for multiple patterns (OR)
rg 'error|warning|fail'

# Using multiple -e flags
rg -e error -e warning -e fail
```

### Invert Match
```bash
# Show lines that DON'T match
rg -v function

# Show files without matches
rg --files-without-match TODO
```

### Max Depth Control
```bash
# Search only in current directory (no subdirectories)
rg --max-depth 1 function

# Search up to 3 levels deep
rg --max-depth 3 function
```

### Follow Symbolic Links
```bash
# Follow symlinks
rg -L function
```

### Search Compressed Files
```bash
# Search in gzip, bzip2, xz, etc.
rg -z function archive.gz

# Auto-detect compression
rg --search-zip function
```

### Limit Results
```bash
# Show only first 10 matches
rg -m 10 function

# Stop after first match per file
rg -m 1 function
```

### Output Formatting
```bash
# JSON output (useful for scripts)
rg --json function

# NULL separated output
rg --null function

# Show only matching parts
rg -o 'error: \w+'

# Custom output format
rg --replace '$1' 'import (\w+)'
```

### Pre-filtering
```bash
# Use external program for pre-filtering
rg --pre zcat function compressed_logs

# Search in PDFs (requires pdftotext)
rg --pre 'pdftotext - -' search_term *.pdf
```

### Statistics
```bash
# Show search stats
rg --stats function

# Output includes:
# - Files searched
# - Lines searched
# - Matches found
# - Time taken
```

---

## Search Patterns & Regex

### Basic Regex Patterns
```bash
# Start of line
rg '^function'

# End of line
rg 'return$'

# Any character
rg 'f.nction'

# Zero or more
rg 'colou*r'

# One or more
rg 'error+'

# Optional
rg 'https?://'

# Character class
rg '[0-9]+'

# Negated character class
rg '[^a-z]'
```

### Advanced Regex
```bash
# Word boundary
rg '\btest\b'

# Non-greedy match
rg 'start.*?end'

# Capture groups
rg '(error|warning): (.+)'

# Lookahead
rg 'function(?=\()'

# Lookbehind
rg '(?<=def )\w+'

# Case-insensitive flag in pattern
rg '(?i)error'
```

### Common Patterns
```bash
# Email addresses
rg '\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'

# IP addresses
rg '\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b'

# URLs
rg 'https?://[^\s]+'

# Phone numbers (US)
rg '\b\d{3}[-.]?\d{3}[-.]?\d{4}\b'

# Dates (YYYY-MM-DD)
rg '\b\d{4}-\d{2}-\d{2}\b'

# Hex colors
rg '#[0-9a-fA-F]{6}\b'

# Function definitions (Python)
rg 'def \w+\([^)]*\):'

# TODO comments
rg '(TODO|FIXME|HACK|XXX):'

# Import statements (Python)
rg '^import \w+|^from \w+ import'

# Environment variables
rg '\$\{?[A-Z_][A-Z0-9_]*\}?'
```

---

## Practical Examples

### Example 1: Find TODOs in Your Project
```bash
# Basic TODO search
rg TODO

# Case-insensitive with context
rg -i -C 2 'todo|fixme|hack|xxx'

# Only in code files
rg -t py -t js -t java 'TODO:'

# With author name
rg 'TODO.*@(\w+)' -o
```

### Example 2: Search Logs for Errors
```bash
# Find all errors
rg -i error /var/log/

# Errors in last hour (combine with find)
find /var/log -mmin -60 -type f | xargs rg -i error

# Count errors per file
rg -c -i error /var/log/

# Errors with context
rg -C 5 -i 'error|exception|fail' /var/log/

# Multiple error types
rg -e ERROR -e WARN -e FATAL
```

### Example 3: Code Analysis
```bash
# Find function definitions (Python)
rg 'def \w+\(' -t py

# Find all imports
rg '^import |^from ' -t py

# Find security issues
rg -i 'password|secret|api_key' -g '!*.md'

# Find deprecated functions
rg '@deprecated' -A 2

# Find long lines (over 100 chars)
rg '.{100,}'

# Dead code (functions never called)
rg 'def (\w+)' -o | sort -u > defined.txt
rg '\b\w+\(' -o | sort -u > called.txt
comm -23 defined.txt called.txt
```

### Example 4: Search Configuration Files
```bash
# Find all config files
rg -l -g '*.conf' -g '*.config' -g '*.ini' .

# Search in config files only
rg -g '*.{conf,config,ini,yaml,yml,json}' database

# Hidden config files
rg --hidden -g '.*.conf' port
```

### Example 5: Search in Termux Storage
```bash
# Find text in all documents
rg -t txt -t md -t pdf 'search term' ~/storage/

# Search in specific app directories
rg pattern ~/storage/shared/Android/data/

# Find in downloads
rg -i 'receipt|invoice' ~/storage/downloads/
```

### Example 6: Find and Replace Workflow
```bash
# Step 1: Preview what will be replaced
rg 'old_name' -r 'new_name'

# Step 2: Check which files will be affected
rg -l 'old_name'

# Step 3: Backup
rg -l 'old_name' | xargs -I {} cp {} {}.backup

# Step 4: Replace (using sed)
rg -l 'old_name' | xargs sed -i 's/old_name/new_name/g'

# Alternative: Using rg and save output
rg 'old_name' -r 'new_name' --passthru file.txt > file_new.txt
```

### Example 7: Finding Duplicate Code
```bash
# Find similar function signatures
rg 'def (\w+)\(' -o -r '$1' -t py | sort | uniq -c | sort -rn

# Find repeated strings
rg -o '"[^"]+"' | sort | uniq -c | sort -rn | head -20
```

### Example 8: Search Multiple Projects
```bash
# Search in multiple project directories
rg function ~/Projects/*/src/

# Search with specific file types across projects
rg -t py import ~/Projects/*/
```

### Example 9: Grep Through Command Output
```bash
# Search in command output
ls -la | rg '\.sh$'

# Search in git log
git log | rg 'bug fix'

# Search in process list
ps aux | rg python
```

### Example 10: Finding Security Issues
```bash
# Find hardcoded credentials
rg -i 'password\s*=|api_key\s*=|secret\s*=' -g '!*.md'

# Find SQL injection vulnerabilities
rg 'execute.*%s|query.*format|cursor\.execute.*%'

# Find potential XSS
rg 'innerHTML|document\.write'

# Find insecure functions
rg 'eval\(|exec\(|system\('
```

---

## Configuration

### Creating .ripgreprc

Create a config file for default options:

```bash
# Location
~/.ripgreprc

# Or set custom location
export RIPGREP_CONFIG_PATH=~/myconfig/.ripgreprc
```

**Example .ripgreprc:**
```bash
# Always use smart case
--smart-case

# Always show line numbers
--line-number

# Always show colors
--colors=line:fg:yellow
--colors=line:style:bold
--colors=path:fg:green
--colors=path:style:bold
--colors=match:fg:black
--colors=match:bg:yellow
--colors=match:style:nobold

# Default to showing 2 lines of context
--context=2

# Exclude directories
--glob=!.git/*
--glob=!node_modules/*
--glob=!.venv/*
--glob=!__pycache__/*
--glob=!*.min.js
--glob=!*.pyc

# Follow symlinks
--follow

# Search hidden files by default
--hidden

# Maximum columns to show
--max-columns=150

# Show stats
--stats
```

### Using the Config File
```bash
# Config is loaded automatically if in ~/.ripgreprc
rg pattern

# Disable config for one search
rg --no-config pattern

# Use alternative config
RIPGREP_CONFIG_PATH=~/other.ripgreprc rg pattern
```

### Custom File Types

Add custom file type definitions:

```bash
# In .ripgreprc
--type-add=web:*.{html,css,js,jsx,ts,tsx}
--type-add=config:*.{json,yaml,yml,toml,ini,conf}
--type-add=docs:*.{md,rst,txt,adoc}

# Then use them
rg -t web function
rg -t config database
rg -t docs TODO
```

---

## Tips & Tricks

### 1. **Create Useful Aliases**

Add to `~/.bashrc` or `~/.zshrc`:

```bash
# Basic search aliases
alias rgf='rg --files | rg'              # Search in filenames
alias rgi='rg -i'                         # Case-insensitive
alias rgl='rg -l'                         # List files with matches
alias rgc='rg -C 3'                       # Always show context

# Specific search types
alias rgtodo='rg -i "(TODO|FIXME|HACK|XXX):"'
alias rgerr='rg -i "error|exception|fail"'
alias rgimport='rg "^import |^from "'

# Search with stats
alias rgs='rg --stats'

# Hidden and ignored
alias rga='rg -uu'                        # Search everything

# Pretty output
alias rgp='rg --pretty'

# Code-specific
alias rgpy='rg -t py'
alias rgjs='rg -t js'
alias rgjson='rg -t json'
```

### 2. **Searching Filenames**

```bash
# List all files
rg --files

# Filter filenames
rg --files | rg pattern

# Find specific file
rg --files | rg config.json

# Files with extension
rg --files | rg '\.py$'
```

### 3. **Interactive Search with fzf**

```bash
# Search and select with fzf
rg --files | fzf

# Search content and open in editor
rg --line-number pattern | fzf | cut -d: -f1-2 | xargs -I {} sh -c 'nvim +$(echo {} | cut -d: -f2) $(echo {} | cut -d: -f1)'

# Preview matches in fzf
rg --line-number --color=always pattern | fzf --ansi --preview 'echo {}'
```

### 4. **Combining with Other Tools**

```bash
# Count total matches
rg pattern | wc -l

# Sort results
rg pattern | sort

# Unique results
rg pattern | sort -u

# Most common matches
rg -o pattern | sort | uniq -c | sort -rn

# Pipe to less with colors
rg --color always pattern | less -R

# Copy results to clipboard (Termux)
rg pattern | termux-clipboard-set
```

### 5. **Performance Tips**

```bash
# Search specific directories only
rg pattern src/ lib/

# Use file type filters
rg -t py pattern     # Faster than rg pattern *.py

# Limit search depth
rg --max-depth 2 pattern

# Skip large files
rg --max-filesize 1M pattern

# Use fixed strings when no regex needed
rg -F 'exact.string'

# Single-threaded for low-power devices
rg --threads 1 pattern
```

### 6. **Debugging Searches**

```bash
# Show which files are being searched
rg --debug pattern 2>&1 | rg 'searching'

# Show why files are ignored
rg --debug pattern 2>&1 | rg 'ignored'

# Show stats
rg --stats pattern

# Dry run (show what would be searched)
rg --files
```

### 7. **Working with Binary Files**

```bash
# Search binary files (disabled by default)
rg --binary pattern

# Search with encoding detection
rg --auto-hybrid-regex pattern

# Specific encoding
rg --encoding utf16 pattern
```

### 8. **Batch Operations**

```bash
# Edit all files with matches
rg -l pattern | xargs vim

# Delete lines with pattern (backup first!)
rg -l pattern | xargs sed -i '/pattern/d'

# Add comment to matching lines
rg -l 'def ' | xargs sed -i 's/def /# TODO: Review\ndef /'

# Count matches per file and sum
rg -c pattern | awk -F: '{sum+=$2} END {print sum}'
```

### 9. **Exclude Common Noise**

```bash
# Exclude minified files
rg pattern -g '!*.min.js' -g '!*.min.css'

# Exclude dependencies
rg pattern -g '!node_modules/*' -g '!vendor/*' -g '!*.lock'

# Exclude test files
rg pattern -g '!*test*' -g '!*spec*'

# Exclude build artifacts
rg pattern -g '!build/*' -g '!dist/*' -g '!target/*'
```

### 10. **Termux-Specific Uses**

```bash
# Search in app data
rg pattern ~/storage/shared/Android/

# Find text in all files
rg -t txt pattern ~/storage/

# Search compressed logs
rg -z pattern ~/storage/downloads/*.gz

# Find in WhatsApp messages (if accessible)
rg "search term" ~/storage/shared/WhatsApp/

# Search photos metadata (requires exiftool)
rg --pre 'exiftool' pattern ~/storage/DCIM/
```

---

## Performance Optimization

### Understanding Performance

ripgrep is already very fast, but here are ways to make it even faster:

```bash
# 1. Use file type filters (pre-filters files)
rg -t py pattern          # Fast
rg pattern '*.py'         # Slower

# 2. Limit search scope
rg pattern src/           # Fast (specific dir)
rg pattern ./             # Slower (everything)

# 3. Use fixed strings when possible
rg -F 'literal'           # Fastest
rg 'literal'              # Fast (simple regex)
rg '(complex|regex)*'     # Slower

# 4. Reduce depth
rg --max-depth 2 pattern  # Faster
rg pattern                # Slower (unlimited depth)

# 5. Skip large files
rg --max-filesize 5M pattern

# 6. Adjust threads (usually auto is best)
rg --threads 4 pattern
```

### Benchmark Your Searches

```bash
# Time a search
time rg pattern

# With stats
rg --stats pattern

# Compare different approaches
time rg -F pattern
time rg pattern
time rg -t py pattern
```

### Memory Considerations (Termux)

```bash
# For large searches on limited RAM
rg --mmap pattern              # Use memory mapping

# Or disable mmap if having issues
rg --no-mmap pattern

# Process fewer threads on low-end devices
rg --threads 2 pattern
```

---

## Common Use Cases

### 1. **Code Review**
```bash
# Find all TODOs assigned to you
rg 'TODO.*@username'

# Find recent changes (with git)
git diff main | rg '^[+-].*function'

# Find commented-out code
rg '^\s*(#|//|/\*).*\bfunction\b'
```

### 2. **Documentation**
```bash
# Find all markdown links
rg '\[.*\]\(.*\)' -t md

# Find broken links (local files)
rg -o '\[.*\]\((.*)\)' -t md | cut -d'(' -f2 | cut -d')' -f1 | while read file; do [ ! -f "$file" ] && echo "Broken: $file"; done

# Extract all code blocks
rg -U '```.*?\n(.*?)```' -t md
```

### 3. **Data Analysis**
```bash
# Count unique IP addresses
rg -o '\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b' | sort -u | wc -l

# Find most common error messages
rg -o 'Error: .*' | sort | uniq -c | sort -rn | head

# Extract emails
rg -o '\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b' | sort -u
```

### 4. **Security Auditing**
```bash
# Find API keys
rg -i 'api[_-]?key|apikey|api_secret' -g '!*.md'

# Find hardcoded passwords
rg -i 'password\s*=\s*["\']' -g '!test*'

# Find SQL queries (potential injection)
rg 'SELECT.*FROM.*WHERE.*\+|execute.*format'

# Find debug code left in production
rg -i 'console\.log|print\(|var_dump|dd\('
```

---

## Quick Reference Card

### Most Common Flags

| Flag | Description |
|------|-------------|
| `-i` | Case-insensitive |
| `-s` | Case-sensitive |
| `-S` | Smart case |
| `-w` | Word boundaries |
| `-v` | Invert match |
| `-l` | Files with matches |
| `-c` | Count per file |
| `-n` | Show line numbers |
| `-N` | Hide line numbers |
| `-A NUM` | Show NUM lines after |
| `-B NUM` | Show NUM lines before |
| `-C NUM` | Show NUM lines context |
| `-t TYPE` | Filter by file type |
| `-T TYPE` | Exclude file type |
| `-g GLOB` | Include files matching glob |
| `-F` | Fixed string (literal) |
| `-U` | Multiline search |
| `-o` | Only show matches |
| `-r` | Replace matches |
| `--hidden` | Include hidden files |
| `--no-ignore` | Include ignored files |
| `-u` | Reduce ignore (repeat to reduce more) |
| `-z` | Search compressed files |
| `-L` | Follow symlinks |
| `--stats` | Show statistics |
| `--files` | List all files |
| `--type-list` | Show file types |

### Common Patterns

| Pattern | Matches |
|---------|---------|
| `^pattern` | Start of line |
| `pattern$` | End of line |
| `\bword\b` | Whole word |
| `pat.*ern` | Any chars between |
| `pat.?ern` | Optional char |
| `[abc]` | Any of a, b, c |
| `[^abc]` | Not a, b, or c |
| `[a-z]` | Range |
| `(a\|b)` | a or b |
| `\d` | Digit |
| `\w` | Word char |
| `\s` | Whitespace |
| `.*` | Greedy |
| `.*?` | Non-greedy |

---

## Comparison with Alternatives

### ripgrep vs grep
```bash
# grep requires -r for recursion
grep -r pattern .

# rg is recursive by default
rg pattern

# grep includes binary files by default
grep -r pattern . 2>/dev/null

# rg skips binary files automatically
rg pattern

# Speed: rg is typically 5-10x faster
```

### ripgrep vs ag (The Silver Searcher)
```bash
# Similar syntax
ag pattern
rg pattern

# rg advantages:
# - Faster (especially on large codebases)
# - Better Unicode support
# - Can search compressed files
# - More configuration options

# Speed: rg is typically 2-3x faster
```

### ripgrep vs find + grep
```bash
# Traditional approach
find . -name "*.py" -exec grep -H "pattern" {} \;

# ripgrep approach (much simpler and faster)
rg -t py pattern

# Speed: rg is typically 10-20x faster
```

---

## Troubleshooting

### Common Issues

**1. No results found but should match:**
```bash
# Check if files are ignored
rg --debug pattern 2>&1 | rg ignored

# Try with -uu to include everything
rg -uu pattern

# Check if pattern needs escaping
rg 'pattern\(\)' or rg -F 'pattern()'
```

**2. Too many results:**
```bash
# Add file type filter
rg -t py pattern

# Limit depth
rg --max-depth 2 pattern

# Use more specific pattern
rg '\bexact_pattern\b'
```

**3. Slow searches:**
```bash
# Check what's being searched
rg --files | wc -l

# Exclude large directories
rg pattern -g '!large_dir/*'

# Show stats to see what's slow
rg --stats pattern
```

**4. Encoding issues:**
```bash
# Try different encoding
rg --encoding utf16 pattern

# Use auto-detection
rg --auto-hybrid-regex pattern
```

---

## Advanced Real-World Examples

### Example: Project-wide Refactoring

```bash
# Step 1: Find all occurrences
rg 'oldFunction' -l

# Step 2: Preview changes
rg 'oldFunction' -r 'newFunction'

# Step 3: Count occurrences
rg -c 'oldFunction'

# Step 4: Backup affected files
rg -l 'oldFunction' | xargs -I {} cp {} {}.bak

# Step 5: Perform replacement
rg -l 'oldFunction' | xargs sed -i 's/oldFunction/newFunction/g'

# Step 6: Verify changes
rg 'newFunction' -c
```

### Example: Log Analysis

```bash
# Find errors in last hour
find /var/log -mmin -60 -type f | xargs rg -i 'error|exception'

# Group by error type
rg -o 'Error: [A-Z]\w+' /var/log/ | sort | uniq -c | sort -rn

# Find correlation
rg 'Error: ConnectionTimeout' -C 5 /var/log/ | rg 'Request ID: \d+'

# Time-based analysis
rg '2025-10-28.*Error' /var/log/

# Extract and analyze
rg -o '"error_code": *"([^"]+)"' /var/log/ | sort | uniq -c
```

### Example: Dependency Audit

```bash
# Find all imports
rg '^import |^from ' -t py > imports.txt

# Find unused imports (compare with usage)
rg '^import (\w+)' -o -r '$1' -t py | sort -u > imported.txt
rg '\b\w+\.' -o -t py | cut -d. -f1 | sort -u > used.txt
comm -23 imported.txt used.txt

# Find deprecated dependencies
rg '@deprecated|@Deprecated' -A 2

# Security check
rg 'requests==|urllib3==' requirements.txt
```

---

## Resources & Documentation

### Official Documentation
- **GitHub Repository:** https://github.com/BurntSushi/ripgrep
- **User Guide:** https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md
- **FAQ:** https://github.com/BurntSushi/ripgrep/blob/master/FAQ.md

### Getting Help
```bash
# Brief help
rg -h

# Detailed help
rg --help

# Man page (if available)
man rg

# Check version
rg --version
```

### Community
- GitHub Issues: https://github.com/BurntSushi/ripgrep/issues
- Reddit: r/commandline, r/rust
- Stack Overflow: Tag `ripgrep`

---

## Cheat Sheet (Quick Copy-Paste)

```bash
# Basic searches
rg pattern                    # Search current directory
rg -i pattern                 # Case-insensitive
rg -w pattern                 # Whole word
rg -v pattern                 # Invert match

# File filtering
rg -t py pattern              # Python files only
rg -T js pattern              # Exclude JavaScript
rg -g '*.conf' pattern        # Glob pattern

# Context
rg -A 3 pattern               # 3 lines after
rg -B 3 pattern               # 3 lines before
rg -C 3 pattern               # 3 lines around

# Output control
rg -l pattern                 # List files only
rg -c pattern                 # Count per file
rg -o pattern                 # Only matching part

# Include hidden/ignored
rg --hidden pattern           # Include hidden
rg --no-ignore pattern        # Include ignored
rg -uu pattern                # Include both

# Advanced
rg -U 'multi.*line'           # Multiline
rg -F 'literal()'             # Fixed string
rg pattern -r replacement     # Replace preview
rg --stats pattern            # Show statistics
```

---

**Last Updated:** October 2025  
**Created for:** Termux Users  
**License:** ripgrep is dual-licensed under MIT or Unlicense

---

## Performance Notes

On a typical project (500MB, 50k files):
- **grep -r**: ~45 seconds
- **ag**: ~8 seconds
- **rg**: ~2 seconds

**ripgrep is approximately 4-20x faster than alternatives!**

---

## Final Tips

1. **Start simple** - Basic search is usually enough: `rg pattern`
2. **Use file types** - Faster and more relevant: `rg -t py pattern`
3. **Learn regex gradually** - Start with literal strings, add complexity as needed
4. **Create a .ripgreprc** - Set your preferred defaults once
5. **Combine with tools** - Pipe to fzf, sed, awk for powerful workflows
6. **Check --type-list** - Discover built-in file type support
7. **Use -l first** - Preview which files match before detailed search
8. **Remember -uu** - When you need to search EVERYTHING
9. **Experiment** - ripgrep is forgiving and fast enough to try different approaches
10. **Read the GUIDE** - The official guide has excellent advanced examples

---

*Happy searching! 🚀*


---

## Sources

1. **Official ripgrep GitHub Repository**: https://github.com/BurntSushi/ripgrep - The primary source for all ripgrep features, documentation, and updates by Andrew Gallant (BurntSushi)
2. **ripgrep User Guide**: https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md - Official comprehensive guide
3. **ripgrep FAQ**: https://github.com/BurntSushi/ripgrep/blob/master/FAQ.md - Frequently asked questions and troubleshooting
4. ripgrep's command-line help and man pages
5. Performance benchmarks and comparison data from the official repository and community testing
6. Practical examples derived from common use cases, best practices, and community contributions

This guide is current as of October 2025 and includes all major features available in the latest version of ripgrep. The tool is actively maintained and regularly updated with performance improvements and new features.