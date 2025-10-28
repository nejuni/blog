--- 
title: "nano"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:18:52Z
tags: ["Editor"]
draft: false
---

# 📝 Nano Text Editor - Complete Guide for Termux (2025)

## Table of Contents
- [About Nano](#about-nano)
- [Basic Usage](#basic-usage)
- [Essential Commands](#essential-commands)
- [File Operations](#file-operations)
- [Editing Commands](#editing-commands)
- [Search and Replace](#search-and-replace)
- [Advanced Features](#advanced-features)
- [Configuration](#configuration)
- [Practical Examples](#practical-examples)
- [Tips and Tricks](#tips-and-tricks)

---

## About Nano

**Nano** is a simple, user-friendly command-line text editor that's perfect for beginners and experienced users alike. It's designed to be easy to use with on-screen command hints.

- **Official Website**: https://www.nano-editor.org/
- **Official Repo**: https://git.savannah.gnu.org/cgit/nano.git
- **Version**: 8.x (latest as of 2025)
- **License**: GNU GPL v3+

### Key Features
- Easy to learn with visible command shortcuts
- Syntax highlighting for multiple languages
- Multi-buffer support (edit multiple files)
- Search and replace with regex support
- Auto-indentation
- Undo/Redo functionality
- Mouse support in compatible terminals

---

## Basic Usage

### Opening Files

```bash
# Open nano (blank file)
nano

# Open/create a specific file
nano filename.txt

# Open file at specific line number
nano +15 filename.txt

# Open file at specific line and column
nano +15,7 filename.txt

# Open file as read-only
nano -v filename.txt

# Open multiple files
nano file1.txt file2.txt file3.txt
```

---

## Essential Commands

> **Note**: In nano, `^` means `Ctrl` key, and `M-` means `Alt` key (or `Esc` then the key)

### Most Important Daily Commands

| Command | Action |
|---------|--------|
| `Ctrl + O` | **Save** file (WriteOut) |
| `Ctrl + X` | **Exit** nano |
| `Ctrl + K` | **Cut** current line |
| `Ctrl + U` | **Paste** cut text |
| `Ctrl + W` | **Search** for text |
| `Alt + W` | **Search next** occurrence |
| `Ctrl + \` | **Replace** text |
| `Ctrl + G` | **Help** - Display help text |
| `Ctrl + C` | Show **cursor position** |
| `Ctrl + _` | **Go to line** number |

### Navigation Commands

| Command | Action |
|---------|--------|
| `Ctrl + A` | Go to **beginning of line** |
| `Ctrl + E` | Go to **end of line** |
| `Ctrl + Y` | **Page up** |
| `Ctrl + V` | **Page down** |
| `Alt + \` | Go to **first line** |
| `Alt + /` | Go to **last line** |
| `Ctrl + ←` | Move **one word backward** |
| `Ctrl + →` | Move **one word forward** |
| `Alt + (` | Go to **beginning of paragraph** |
| `Alt + )` | Go to **end of paragraph** |

---

## File Operations

### Saving Files

```bash
# Save current file
Ctrl + O
# Then press Enter to confirm

# Save with a new name (Save As)
Ctrl + O
# Type new filename and press Enter

# Save and exit
Ctrl + O → Enter → Ctrl + X

# Exit without saving
Ctrl + X → N (when prompted)
```

### Working with Multiple Files

```bash
# Open additional file
Alt + <
# Or: Alt + Shift + ,

# Switch between open files
Alt + ,  # Previous file
Alt + .  # Next file

# Close current buffer
Ctrl + X

# Insert another file into current one
Ctrl + R
# Then type filename and press Enter
```

---

## Editing Commands

### Cut, Copy, and Paste

```bash
# Cut current line
Ctrl + K

# Cut from cursor to end of line
Alt + T

# Copy current line (without deleting)
Alt + 6

# Paste cut/copied text
Ctrl + U

# Cut selected text (after marking)
Ctrl + K
```

### Select Text (Marking)

```bash
# Start selection (mark text)
Alt + A
# (Or: Ctrl + 6)

# Move cursor to select text
# Use arrow keys or navigation commands

# Cut marked text
Ctrl + K

# Copy marked text
Alt + 6

# Cancel selection
Alt + A (again)
```

### Undo and Redo

```bash
# Undo last action
Alt + U

# Redo last undone action
Alt + E
```

### Delete Operations

```bash
# Delete character under cursor
Ctrl + D

# Delete character before cursor
Backspace

# Delete word to the right
Ctrl + Delete

# Delete from cursor to end of line
Ctrl + K (on empty marked region)
```

---

## Search and Replace

### Basic Search

```bash
# Search forward
Ctrl + W
# Type search term and press Enter

# Search backward
Alt + W (after a forward search)

# Search next occurrence
Alt + W

# Search previous occurrence
Alt + Q

# Toggle case-sensitive search
Alt + C (while in search prompt)

# Toggle regex search
Alt + R (while in search prompt)
```

### Find and Replace

```bash
# Start replace function
Ctrl + \

# Type text to find → Enter
# Type replacement text → Enter

# Options:
Y - Replace this instance
N - Skip this instance
A - Replace all instances
Ctrl + C - Cancel
```

### Advanced Search Examples

```bash
# Case-insensitive search
Ctrl + W → Alt + C → type search term

# Regex search
Ctrl + W → Alt + R → type regex pattern

# Search backward
Ctrl + W → Alt + B → type search term
```

---

## Advanced Features

### Syntax Highlighting

Nano automatically detects file types and applies syntax highlighting:

```bash
# Open Python file (auto-highlighted)
nano script.py

# Open shell script
nano script.sh

# Open configuration file
nano config.conf
```

### Line Numbers

```bash
# Show line numbers
Alt + #
# Or: Alt + Shift + 3

# Toggle line numbers on/off
Alt + # (again)

# Go to specific line
Ctrl + _
# Type line number and press Enter
```

### Auto-Indentation

```bash
# Enable auto-indent (usually on by default)
Alt + I

# Indent current line
Alt + }

# Unindent current line
Alt + {
```

### Spell Check

```bash
# Start spell checker (if installed)
Ctrl + T

# Requires 'spell' or 'aspell' package:
pkg install aspell aspell-en
```

---

## Configuration

### Custom Configuration File

Create/edit `~/.nanorc`:

```bash
nano ~/.nanorc
```

### Recommended Configuration

```bash
# ~/.nanorc - Nano configuration file

## Enable syntax highlighting
include /data/data/com.termux/files/usr/share/nano/*.nanorc

## Show line numbers by default
set linenumbers

## Auto-indent new lines
set autoindent

## Convert typed tabs to spaces
set tabstospaces

## Set tab size to 4 spaces
set tabsize 4

## Enable smooth scrolling
set smooth

## Enable mouse support
set mouse

## Don't wrap long lines
set nowrap

## Show a guide bar at column 80
# set guidestripe 80

## Backup files on save
set backup
set backupdir "~/nano_backups"

## Remember cursor position
set positionlog

## Allow suspended mode (Ctrl+Z)
set suspend

## Enable multiple file buffers
set multibuffer

## Show cursor position constantly
set constantshow

## Smart home key (toggle between start of text and start of line)
set smarthome

## Use bold instead of reverse video for title bar
set titlecolor bold,white,blue
set statuscolor bold,white,green
set keycolor bold,white,blue
set functioncolor green
```

### Apply Configuration

```bash
# Create backup directory
mkdir -p ~/nano_backups

# Reload nano with new config
# (just open nano again)
nano test.txt
```

---

## Practical Examples

### Example 1: Edit a Configuration File

```bash
# Open config file
nano ~/.bashrc

# Add line at the end
# Navigate: Ctrl + End (Alt + /)
# Type: alias ll='ls -la'

# Save and exit
# Ctrl + O → Enter → Ctrl + X
```

### Example 2: Quick Text File Creation

```bash
# Create new shopping list
nano shopping-list.txt

# Type your list
# Milk
# Bread
# Eggs

# Save with Ctrl + O → Enter
# Exit with Ctrl + X
```

### Example 3: Search and Replace All

```bash
# Open file
nano document.txt

# Replace all "old" with "new"
# Press: Ctrl + \
# Search for: old
# Replace with: new
# Press: A (to replace all)
```

### Example 4: Edit Multiple Files

```bash
# Open multiple files
nano file1.txt file2.txt file3.txt

# Edit first file
# ...make changes...

# Switch to next file
# Alt + .

# Edit second file
# ...make changes...

# Save all and exit
# Ctrl + O → Enter (for each file)
# Ctrl + X (when all saved)
```

### Example 5: Copy Between Files

```bash
# Open two files
nano file1.txt file2.txt

# In file1.txt, mark text
# Alt + A → select text → Alt + 6 (copy)

# Switch to file2.txt
# Alt + .

# Paste
# Ctrl + U

# Save both files
# Ctrl + O → Enter
# Alt + . (switch back)
# Ctrl + O → Enter
```

### Example 6: Create a Python Script

```bash
# Create Python file (with syntax highlighting)
nano hello.py

# Type Python code:
#!/usr/bin/env python3

def main():
    print("Hello, World!")
    
if __name__ == "__main__":
    main()

# Save: Ctrl + O → Enter
# Exit: Ctrl + X

# Make executable and run
chmod +x hello.py
python hello.py
```

### Example 7: View File (Read-Only)

```bash
# Open in read-only mode
nano -v important-file.txt

# View only, cannot save changes
# Exit with Ctrl + X
```

### Example 8: Insert File Content

```bash
# Open main file
nano main-document.txt

# Position cursor where you want to insert

# Insert another file
# Ctrl + R
# Type: section2.txt
# Press Enter

# Content inserted at cursor position
```

---

## Tips and Tricks

### Productivity Tips

1. **Quick Save Without Prompts**
   ```bash
   # Enable in .nanorc
   set quickblank  # Clear status messages quickly
   ```

2. **Soft Wrapping for Long Lines**
   ```bash
   # Enable soft wrap
   Alt + $
   # Text wraps visually but doesn't insert newlines
   ```

3. **Justify/Format Paragraph**
   ```bash
   # Justify current paragraph
   Ctrl + J
   ```

4. **Count Words**
   ```bash
   # Mark text: Alt + A
   # Show cursor position: Ctrl + C
   # Displays character/word count for selection
   ```

5. **Refresh Screen**
   ```bash
   # If display gets corrupted
   Ctrl + L
   ```

6. **Toggle Help Display**
   ```bash
   # Hide/show bottom help lines
   Alt + X
   ```

### Common Termux-Specific Issues

```bash
# If Ctrl key doesn't work in Termux:
# Use Volume Down as Ctrl key (Termux default)
# Or enable Extra Keys row in Termux settings

# If colors don't show:
# Make sure syntax files are available
ls /data/data/com.termux/files/usr/share/nano/

# If mouse support issues:
# Add to .nanorc: set mouse
```

### Keyboard Shortcuts Summary Card

```
┌─────────────────────────────────────────────┐
│         NANO QUICK REFERENCE                │
├─────────────────────────────────────────────┤
│  FILE OPERATIONS                            │
│  Ctrl+O  Save         Ctrl+X  Exit          │
│  Ctrl+R  Insert File  Alt+<   New File      │
│                                             │
│  NAVIGATION                                 │
│  Ctrl+A  Line Start   Ctrl+E  Line End      │
│  Ctrl+Y  Page Up      Ctrl+V  Page Down     │
│  Ctrl+_  Go to Line   Alt+\   First Line    │
│                                             │
│  EDITING                                    │
│  Ctrl+K  Cut Line     Ctrl+U  Paste         │
│  Alt+6   Copy Line    Alt+A   Mark Text     │
│  Alt+U   Undo         Alt+E   Redo          │
│  Ctrl+D  Delete Char  Ctrl+J  Justify       │
│                                             │
│  SEARCH                                     │
│  Ctrl+W  Search       Ctrl+\  Replace       │
│  Alt+W   Next Match   Alt+Q   Previous      │
│                                             │
│  DISPLAY                                    │
│  Alt+#   Line Numbers Alt+X   Hide Help     │
│  Alt+$   Soft Wrap    Ctrl+L  Refresh       │
│                                             │
│  MULTI-FILE                                 │
│  Alt+,   Prev File    Alt+.   Next File     │
│  Alt+<   Open New     Ctrl+X  Close Buffer  │
└─────────────────────────────────────────────┘
```

---

## Command Line Options

```bash
# Useful nano command-line options for Termux:

nano -l filename.txt          # Show line numbers
nano -i filename.txt          # Auto-indent
nano -E filename.txt          # Convert tabs to spaces
nano -w filename.txt          # No line wrapping
nano -m filename.txt          # Enable mouse
nano -c filename.txt          # Constantly show cursor position
nano -B filename.txt          # Backup before saving
nano -A filename.txt          # Enable smart home key
nano -S filename.txt          # Enable smooth scrolling
nano -Y python script.py      # Force syntax highlighting type

# Combine multiple options:
nano -liEmwc filename.txt     # Line numbers, indent, tabs to spaces, mouse, cursor pos
```

---

## Quick Start Workflow

### For Beginners (3-Step Process):

1. **Open/Create File**
   ```bash
   nano myfile.txt
   ```

2. **Edit & Save**
   - Type your content
   - Press `Ctrl + O` to save
   - Press `Enter` to confirm

3. **Exit**
   ```bash
   Ctrl + X
   ```

### For Daily Use (Common Tasks):

```bash
# Edit system file
nano ~/.bashrc

# Create quick note
nano notes.txt

# View file (read-only)
nano -v important.txt

# Edit with line numbers
nano -l script.sh

# Edit multiple related files
nano index.html style.css script.js
```

---

## Resources

- **Official Website**: https://www.nano-editor.org/
- **Documentation**: https://www.nano-editor.org/docs.php
- **Git Repository**: https://git.savannah.gnu.org/cgit/nano.git
- **Man Page**: `man nano` (in Termux: may need `pkg install man`)
- **Help in Nano**: Press `Ctrl + G`

---

## Troubleshooting

### Common Issues in Termux:

1. **Ctrl Key Not Working**
   - Use Volume Down button as Ctrl
   - Or enable Extra Keys row: Settings → Extra Keys

2. **No Syntax Highlighting**
   ```bash
   pkg install nano
   # Reinstall to get syntax files
   ```

3. **Cannot Save File**
   - Check file permissions
   - Make sure directory exists
   - Use absolute path if needed

4. **Screen Corruption**
   - Press `Ctrl + L` to refresh

5. **Backup Directory Error**
   ```bash
   mkdir -p ~/nano_backups
   ```

---

## Conclusion

Nano is perfect for Termux because it's:
- ✅ Lightweight and fast
- ✅ Easy to learn
- ✅ Full-featured for most tasks
- ✅ Shows commands on screen
- ✅ Works great on mobile devices

**Most Used Commands**: `Ctrl+O` (save), `Ctrl+X` (exit), `Ctrl+W` (search), `Ctrl+K` (cut), `Ctrl+U` (paste)

---

*Last Updated: October 2025*  
*Nano Version: 8.x*  
*Platform: Termux/Linux*

---

# How to Use This Guide

## Getting Started

Save this guide for offline reference:

```bash
# Save this guide to your device
nano ~/nano-guide.md
# Paste this content
# Save: Ctrl+O → Enter → Ctrl+X

# View anytime with:
nano ~/nano-guide.md
# Or use any markdown viewer
```

## Practice Examples

Try these exercises to master nano:

### Exercise 1: Basic Editing
```bash
nano practice.txt
# Type: "Hello Termux"
# Save: Ctrl+O → Enter
# Exit: Ctrl+X
# Verify: cat practice.txt
```

### Exercise 2: Cut and Paste
```bash
nano practice.txt
# Type multiple lines
# Cut a line: Ctrl+K
# Move cursor
# Paste: Ctrl+U
# Save and exit
```

### Exercise 3: Search and Replace
```bash
nano practice.txt
# Add text with repeated words
# Replace: Ctrl+\
# Find: "old"
# Replace: "new"
# Choose: A (all)
```

### Exercise 4: Multiple Files
```bash
nano file1.txt file2.txt
# Edit file1
# Switch: Alt+.
# Edit file2
# Switch back: Alt+,
# Save all and exit
```

---

## Sources & Citations

This guide was compiled from the following sources:

1. **Official Nano Documentation** - https://www.nano-editor.org/
2. **GNU Nano Manual** - https://www.nano-editor.org/dist/latest/nano.html
3. **Nano Git Repository** - https://git.savannah.gnu.org/cgit/nano.git
4. **Termux Documentation** - Various community resources for Termux-specific usage

All commands and features documented here are accurate as of October 2025 and compatible with nano version 8.x available in Termux repositories.