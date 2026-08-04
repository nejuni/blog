--- 
title: "rsync"
description: "readme"
date: 2025-10-29T14:10:52Z
lastmod: 2025-10-29T14:27:52Z
tags: ["Archive"]
draft: false
---

# rsync - Remote Sync Tool for Termux

**Last Updated:** October 2025  
**Official Website:** https://rsync.samba.org/  
**GitHub Repository:** https://github.com/WayneD/rsync  
**Latest Stable Version:** 3.3.0 (released April 2024)

---

## Table of Contents
- [Overview](#overview)
- [Installation in Termux](#installation-in-termux)
- [Basic Syntax](#basic-syntax)
- [Most Useful Daily Commands](#most-useful-daily-commands)
- [Common Options](#common-options)
- [Practical Examples](#practical-examples)
- [Advanced Use Cases](#advanced-use-cases)
- [Tips for Termux Users](#tips-for-termux-users)
- [Troubleshooting](#troubleshooting)

---

## Overview

**rsync** is a fast, versatile file synchronization and transfer tool. It uses a delta-transfer algorithm that only sends differences between files, making it extremely efficient for:

- Backing up files locally or remotely
- Mirroring directories
- Incremental backups
- Synchronizing files between devices

### Key Features (as of 2025)
- **Delta-transfer algorithm** - only transfers changed portions
- **Compression** - reduces network bandwidth usage
- **Preservation** - maintains permissions, timestamps, ownership
- **Partial transfers** - resume interrupted transfers
- **SSH integration** - secure remote transfers
- **Exclude patterns** - flexible file filtering

---

## Installation in Termux

```bash
# Update package list
pkg update

# Install rsync
pkg install rsync

# Verify installation
rsync --version
```

**Expected output:**
```
rsync  version 3.3.0  protocol version 31
```

---

## Basic Syntax

```bash
rsync [OPTIONS] SOURCE DESTINATION
```

### Important Notes:
- **Trailing slash matters!**
  - `rsync -av /source/ /dest/` - copies **contents** of source
  - `rsync -av /source /dest/` - copies source **directory itself**

---

## Most Useful Daily Commands

### 1. **Basic Local Copy**
```bash
rsync -av /source/ /destination/
```
- `-a` = archive mode (preserves permissions, timestamps, etc.)
- `-v` = verbose (shows files being transferred)

### 2. **Copy with Progress Bar**
```bash
rsync -av --progress /source/ /destination/
```
Shows real-time transfer progress.

### 3. **Dry Run (Test Before Executing)**
```bash
rsync -av --dry-run /source/ /destination/
```
Shows what **would** be transferred without actually doing it.

### 4. **Delete Files in Destination Not in Source**
```bash
rsync -av --delete /source/ /destination/
```
Makes destination an exact mirror of source.

### 5. **Copy Over SSH (Remote Sync)**
```bash
rsync -avz -e ssh user@remote:/path/to/source/ /local/destination/
```
- `-z` = compress during transfer
- `-e ssh` = use SSH protocol

### 6. **Exclude Files/Directories**
```bash
rsync -av --exclude='*.tmp' --exclude='cache/' /source/ /destination/
```

### 7. **Include Only Specific Files**
```bash
rsync -av --include='*.jpg' --exclude='*' /source/ /destination/
```

### 8. **Resume Interrupted Transfer**
```bash
rsync -av --partial --progress /source/ /destination/
```
- `--partial` = keep partially transferred files

### 9. **Backup with Timestamp**
```bash
rsync -av /source/ /backup/$(date +%Y%m%d)/
```

### 10. **Show Total Transfer Statistics**
```bash
rsync -av --stats /source/ /destination/
```

---

## Common Options

| Option | Description |
|--------|-------------|
| `-a, --archive` | Archive mode (recursive, preserves everything) |
| `-v, --verbose` | Increase verbosity |
| `-r, --recursive` | Recurse into directories |
| `-u, --update` | Skip files newer on destination |
| `-z, --compress` | Compress file data during transfer |
| `-h, --human-readable` | Output numbers in human-readable format |
| `-P` | Same as `--partial --progress` |
| `-n, --dry-run` | Perform trial run without changes |
| `--delete` | Delete files not in source |
| `--exclude=PATTERN` | Exclude files matching pattern |
| `--include=PATTERN` | Include files matching pattern |
| `--max-size=SIZE` | Don't transfer files larger than SIZE |
| `--min-size=SIZE` | Don't transfer files smaller than SIZE |
| `--backup` | Make backups of existing files |
| `--stats` | Show file transfer statistics |
| `-e ssh` | Specify SSH as remote shell |

---

## Practical Examples

### Example 1: Backup Photos to External Storage
```bash
# Backup all photos from DCIM to external SD card
rsync -avh --progress ~/storage/dcim/ /sdcard/Backups/Photos/

# With exclusions
rsync -avh --progress \
  --exclude='*.thumbnails' \
  --exclude='.trashed' \
  ~/storage/dcim/ /sdcard/Backups/Photos/
```

### Example 2: Sync Documents Between Devices
```bash
# From Termux to remote server
rsync -avz -e ssh ~/Documents/ user@192.168.1.100:/home/user/mobile-docs/

# From remote server to Termux
rsync -avz -e ssh user@192.168.1.100:/home/user/docs/ ~/Documents/
```

### Example 3: Incremental Backup with Date
```bash
# Create dated backup directory
BACKUP_DIR="/sdcard/Backups/$(date +%Y-%m-%d)"
rsync -avh --progress ~/storage/shared/ "$BACKUP_DIR/"
```

### Example 4: Mirror Directory (Exact Copy)
```bash
# Make destination identical to source
rsync -avh --delete --progress /source/ /destination/
```

### Example 5: Copy Only Modified Files
```bash
# Update only changed files (don't overwrite newer files)
rsync -avu --progress /source/ /destination/
```

### Example 6: Bandwidth Limiting
```bash
# Limit transfer to 1000 KB/s
rsync -av --bwlimit=1000 /source/ /destination/
```

### Example 7: Exclude Multiple Patterns
```bash
rsync -av \
  --exclude='*.tmp' \
  --exclude='*.cache' \
  --exclude='node_modules/' \
  --exclude='.git/' \
  ~/Projects/ /sdcard/Backups/Projects/
```

### Example 8: Copy Only Specific File Types
```bash
# Backup only images
rsync -av \
  --include='*/' \
  --include='*.jpg' \
  --include='*.png' \
  --include='*.gif' \
  --exclude='*' \
  ~/storage/shared/ ~/Backups/Images/
```

### Example 9: Resume Large File Transfer
```bash
# If transfer was interrupted, resume it
rsync -avP /large-files/ /destination/
```

### Example 10: Sync with Size Limits
```bash
# Only sync files between 1KB and 10MB
rsync -av \
  --min-size=1K \
  --max-size=10M \
  /source/ /destination/
```

---

## Advanced Use Cases

### Automated Backup Script
```bash
#!/data/data/com.termux/files/usr/bin/bash

# backup-script.sh
SOURCE="$HOME/storage/shared/Important"
DEST="/sdcard/Backups/Important-$(date +%Y%m%d_%H%M%S)"
LOG_FILE="$HOME/backup.log"

echo "Starting backup at $(date)" >> "$LOG_FILE"

rsync -avh \
  --progress \
  --stats \
  --exclude='*.tmp' \
  --exclude='cache/' \
  "$SOURCE/" "$DEST/" >> "$LOG_FILE" 2>&1

if [ $? -eq 0 ]; then
    echo "Backup completed successfully at $(date)" >> "$LOG_FILE"
else
    echo "Backup failed at $(date)" >> "$LOG_FILE"
fi
```

**Make it executable:**
```bash
chmod +x backup-script.sh
```

### Sync with Remote Server Using SSH Key
```bash
# First, set up SSH key (if not already done)
ssh-keygen -t rsa -b 4096
ssh-copy-id user@remote-server

# Then sync without password prompt
rsync -avz -e "ssh -i ~/.ssh/id_rsa" \
  ~/Documents/ \
  user@remote-server:/backup/mobile/
```

### Two-Way Sync (Bidirectional)
```bash
# Sync from local to remote
rsync -avzu /local/path/ user@remote:/remote/path/

# Sync from remote to local
rsync -avzu user@remote:/remote/path/ /local/path/
```

**⚠️ Warning:** This doesn't handle conflicts automatically. Use with caution.

---

## Tips for Termux Users

### 1. **Storage Permissions**
```bash
# Grant storage access to Termux first
termux-setup-storage
```

### 2. **Common Termux Paths**
- Internal storage: `~/storage/shared/` or `/sdcard/`
- Downloads: `~/storage/downloads/`
- DCIM (Camera): `~/storage/dcim/`
- Documents: `~/storage/documents/`

### 3. **Testing Commands Safely**
Always use `--dry-run` first:
```bash
rsync -av --dry-run /source/ /destination/
```

### 4. **Monitoring Progress**
For large transfers, use:
```bash
rsync -avh --progress --stats /source/ /destination/
```

### 5. **Dealing with Special Characters**
Use quotes around paths with spaces:
```bash
rsync -av "/path/with spaces/" "/another path/"
```

### 6. **Network Transfers**
For WiFi transfers between devices, ensure both are on the same network and SSH server is running on the destination.

---

## Troubleshooting

### Issue: "Permission denied"
**Solution:**
```bash
# Check permissions
ls -la /path/to/directory

# Grant storage access in Termux
termux-setup-storage

# Use appropriate user permissions
rsync -av --chmod=ugo=rwX /source/ /destination/
```

### Issue: "Connection refused" (remote sync)
**Solution:**
```bash
# Ensure SSH is running on remote device
# In Termux, install and start SSH:
pkg install openssh
sshd

# Check SSH port (default 8022 in Termux)
rsync -av -e "ssh -p 8022" /source/ user@remote:/dest/
```

### Issue: Transfer is too slow
**Solution:**
```bash
# Use compression for network transfers
rsync -avz /source/ /destination/

# Or disable compression for local transfers
rsync -av --no-compress /source/ /destination/
```

### Issue: "rsync: command not found"
**Solution:**
```bash
pkg update
pkg install rsync
```

### Issue: Files not being deleted with --delete
**Solution:**
```bash
# Ensure correct syntax (source has trailing slash)
rsync -av --delete /source/ /destination/

# Check dry-run first
rsync -av --delete --dry-run /source/ /destination/
```

---

## Quick Reference Card

### Essential Command Template
```bash
rsync -avh --progress [--delete] [--exclude='pattern'] /source/ /destination/
```

### Most Used Options Combination
```bash
# Local backup with progress
rsync -avhP /source/ /backup/

# Remote sync compressed
rsync -avzP -e ssh /local/ user@remote:/path/

# Exact mirror (delete extra files)
rsync -avh --delete /source/ /mirror/

# Safe test run
rsync -avn /source/ /destination/
```

---

## Additional Resources

- **Official Documentation:** https://rsync.samba.org/documentation.html
- **Man Page:** `man rsync` (in Termux: `rsync --help`)
- **GitHub Repository:** https://github.com/WayneD/rsync
- **Rsync Examples:** https://rsync.samba.org/examples.html

---

## Version History Notes

### rsync 3.3.0 (April 2024) - Latest
- Improved performance for large file transfers
- Enhanced support for extended attributes
- Better error reporting and handling
- Security improvements

### Key Changes from 3.2.x to 3.3.0:
- New `--copy-as` option for permission handling
- Improved `--mkpath` functionality
- Enhanced checksum algorithms
- Better handling of symbolic links

---

**Created for Termux users | Updated October 2025**
```

---

## How to Use This Guide

### Step 1: Save the Markdown File
```bash
# In Termux, create the file
nano rsync-guide.md

# Paste the content above, then save with Ctrl+X, Y, Enter
```

### Step 2: View the Guide Anytime
```bash
# Install a markdown viewer (optional)
pkg install mdcat

# View with formatting
mdcat rsync-guide.md

# Or use basic cat/less
less rsync-guide.md
```

### Step 3: Start with Basic Examples

**Example 1: Backup Your Documents**
```bash
# First, check what will be copied (dry run)
rsync -avn ~/Documents/ ~/storage/shared/Backups/Documents/

# If looks good, do the actual sync
rsync -avh --progress ~/Documents/ ~/storage/shared/Backups/Documents/
```

**Example 2: Sync Photos to SD Card**
```bash
# Backup camera photos
rsync -avh --progress ~/storage/dcim/Camera/ /sdcard/PhotoBackup/
```

**Example 3: Mirror a Directory**
```bash
# Make an exact copy (including deletions)
rsync -avh --delete --progress ~/Important/ ~/Important_Backup/
```

### Step 4: Practice with Dry Runs
Before any major operation, **always test first**:
```bash
rsync -avn [your-options] /source/ /destination/
```
The `-n` flag shows what would happen without actually doing it.

### Step 5: Create Your Own Backup Script
```bash
# Create a simple backup script
nano my-backup.sh
```

Add this content:
```bash
#!/data/data/com.termux/files/usr/bin/bash

# Simple backup script
echo "Starting backup..."

rsync -avh --progress \
  --exclude='*.tmp' \
  ~/storage/shared/Important/ \
  ~/storage/shared/Backups/Important-$(date +%Y%m%d)/

echo "Backup completed!"
```

Make it executable:
```bash
chmod +x my-backup.sh
./my-backup.sh
```

---

## Key Concepts Explained

### 1. **Archive Mode (-a)**
This is equivalent to: `-rlptgoD`
- `r` = recursive (include subdirectories)
- `l` = copy symlinks as symlinks
- `p` = preserve permissions
- `t` = preserve modification times
- `g` = preserve group
- `o` = preserve owner
- `D` = preserve device files and special files

### 2. **Trailing Slash Difference**
```bash
# WITH trailing slash - copies CONTENTS
rsync -av /source/ /dest/
# Result: /dest/file1, /dest/file2

# WITHOUT trailing slash - copies DIRECTORY
rsync -av /source /dest/
# Result: /dest/source/file1, /dest/source/file2
```

### 3. **Delete Option Behavior**
```bash
rsync -av --delete /source/ /destination/
```
- Deletes files in destination that don't exist in source
- Makes destination an exact mirror
- **Use with caution!** Test with `--dry-run` first

### 4. **Exclude vs Include**
```bash
# Exclude specific patterns
rsync -av --exclude='*.tmp' /source/ /dest/

# Include only specific files (exclude everything else)
rsync -av --include='*.pdf' --exclude='*' /source/ /dest/

# Complex filtering
rsync -av \
  --include='*/' \
  --include='*.jpg' \
  --include='*.png' \
  --exclude='*' \
  /source/ /dest/
```

---

## Safety Checklist

Before running any rsync command:

1. ✅ **Always use `--dry-run` first** with new commands
2. ✅ **Double-check source and destination paths**
3. ✅ **Be careful with `--delete` flag** - it permanently removes files
4. ✅ **Check trailing slashes** - they change behavior
5. ✅ **Test with small directory first** before large operations
6. ✅ **Have backups** of important data before major syncs
7. ✅ **Verify permissions** on destination directory

---

This guide covers everything you need for daily rsync usage in Termux as of 2025. The information is based on the latest rsync version 3.3.0 and official documentation.

**Sources:**
- Official rsync website: https://rsync.samba.org/
- GitHub repository: https://github.com/WayneD/rsync
- Official documentation and man pages
- Termux wiki and community resources