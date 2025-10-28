--- 
title: "rclone"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:20:52Z
tags: ["Cloud"]
draft: false
---

# Rclone CLI Tool - Complete Guide for Termux (2025)

> **Version:** Latest stable (v1.68+)  
> **Platform:** Termux (Android)  
> **Official Website:** https://rclone.org  
> **GitHub:** https://github.com/rclone/rclone  
> **Last Updated:** October 2025

---

## Table of Contents
1. [What is Rclone?](#what-is-rclone)
2. [Basic Setup](#basic-setup)
3. [Configuration](#configuration)
4. [Essential Daily Commands](#essential-daily-commands)
5. [Sync & Copy Operations](#sync--copy-operations)
6. [File Management](#file-management)
7. [Mount Operations](#mount-operations)
8. [Advanced Features](#advanced-features)
9. [Useful Flags & Options](#useful-flags--options)
10. [Real-World Examples](#real-world-examples)
11. [Troubleshooting](#troubleshooting)

---

## What is Rclone?

Rclone is a command-line program to manage files on cloud storage. It supports over 70+ cloud storage providers including:
- Google Drive
- Dropbox
- OneDrive
- Amazon S3
- MEGA
- pCloud
- And many more...

**Key Features:**
- Sync files between local and cloud storage
- Mount cloud storage as a local filesystem
- Encryption support
- Multi-threaded transfers
- Bandwidth limiting
- Resume interrupted transfers

---

## Basic Setup

### Verify Installation
```bash
rclone version
```

### Check Available Commands
```bash
rclone help
```

### Get Help for Specific Command
```bash
rclone help <command>
# Example:
rclone help copy
```

---

## Configuration

### 1. Configure a New Remote
```bash
rclone config
```

**Interactive Menu:**
- `n` - New remote
- `d` - Delete remote
- `r` - Rename remote
- `c` - Copy remote
- `s` - Set configuration password
- `q` - Quit config

### 2. List Configured Remotes
```bash
rclone listremotes
```

### 3. Show Config File Location
```bash
rclone config file
```

### 4. Configure Without Interactive Menu (Headless)
```bash
rclone config create <name> <type> [options]

# Example: Configure Google Drive
rclone config create mydrive drive scope drive
```

### 5. Test Remote Connection
```bash
rclone about <remote>:

# Example:
rclone about mydrive:
```

---

## Essential Daily Commands

### 1. **List Files and Directories**

#### List top-level directories
```bash
rclone lsd <remote>:

# Example:
rclone lsd mydrive:
```

#### List all files recursively
```bash
rclone ls <remote>:path

# Example:
rclone ls mydrive:Documents
```

#### List with details (size, modification time)
```bash
rclone lsl <remote>:path

# Example:
rclone lsl mydrive:Photos
```

#### List directories only
```bash
rclone lsf <remote>:path --dirs-only

# Example:
rclone lsf mydrive:Backup --dirs-only
```

#### List files only
```bash
rclone lsf <remote>:path --files-only
```

### 2. **Check Storage Information**
```bash
rclone about <remote>:

# Shows: Total space, Used space, Free space
# Example:
rclone about mydrive:
```

### 3. **Size of Directory**
```bash
rclone size <remote>:path

# Example:
rclone size mydrive:Documents
```

### 4. **Check Files (Dry Run)**
```bash
rclone check source:path dest:path --dry-run

# Example:
rclone check /sdcard/DCIM mydrive:Backup/DCIM --dry-run
```

---

## Sync & Copy Operations

### 1. **Copy Files (One Direction)**
```bash
rclone copy source:path dest:path [flags]

# Copy local to cloud
rclone copy /sdcard/Documents mydrive:Backup/Documents

# Copy cloud to local
rclone copy mydrive:Photos /sdcard/Pictures

# Copy between remotes
rclone copy mydrive:Folder1 dropbox:Folder2
```

**Key Features:**
- Doesn't delete files from destination
- Skips files that haven't changed
- Creates destination if it doesn't exist

### 2. **Sync Files (Mirror)**
```bash
rclone sync source:path dest:path [flags]

# Sync local to cloud (DANGEROUS: deletes files in dest not in source)
rclone sync /sdcard/Documents mydrive:Backup/Documents

# Always test with --dry-run first!
rclone sync /sdcard/Documents mydrive:Backup/Documents --dry-run -v
```

⚠️ **WARNING:** `sync` makes destination identical to source (deletes extra files)

### 3. **Bisync (Two-Way Sync)** ⭐ NEW in 2024+
```bash
rclone bisync source:path dest:path [flags]

# Initial sync (required first time)
rclone bisync /sdcard/Documents mydrive:Documents --resync

# Regular bisync
rclone bisync /sdcard/Documents mydrive:Documents

# With conflict resolution
rclone bisync /sdcard/Documents mydrive:Documents --conflict-resolve newer
```

**Conflict Resolution Options:**
- `none` - Keep both files
- `newer` - Keep newer file
- `older` - Keep older file
- `larger` - Keep larger file
- `smaller` - Keep smaller file

### 4. **Move Files**
```bash
rclone move source:path dest:path [flags]

# Move local to cloud
rclone move /sdcard/OldFiles mydrive:Archive

# Move between cloud providers
rclone move mydrive:Folder dropbox:Folder
```

### 5. **Deduplicate Files**
```bash
rclone dedupe [mode] remote:path

# Modes: interactive, first, newest, oldest, largest, smallest, rename

# Example: Keep newest, delete duplicates
rclone dedupe newest mydrive:Photos
```

---

## File Management

### 1. **Create Directory**
```bash
rclone mkdir remote:path

# Example:
rclone mkdir mydrive:NewFolder
```

### 2. **Delete Directory**
```bash
rclone rmdir remote:path

# Example (only if empty):
rclone rmdir mydrive:OldFolder
```

### 3. **Delete Files**
```bash
# Delete a single file
rclone delete remote:path/file.txt

# Delete all files in a directory (keeps directory structure)
rclone delete remote:path

# Example:
rclone delete mydrive:TempFiles
```

### 4. **Purge Directory (Delete Everything)**
```bash
rclone purge remote:path

# Example (DANGEROUS - deletes directory and all contents):
rclone purge mydrive:OldBackup
```

### 5. **Copy Single File**
```bash
rclone copyto source:path/file.txt dest:path/file.txt

# Example:
rclone copyto /sdcard/photo.jpg mydrive:Photos/vacation.jpg
```

### 6. **Move Single File**
```bash
rclone moveto source:path/file.txt dest:path/newname.txt

# Example:
rclone moveto /sdcard/old.pdf mydrive:Documents/new.pdf
```

### 7. **Cat (Display File Contents)**
```bash
rclone cat remote:path/file.txt

# Example:
rclone cat mydrive:Documents/notes.txt
```

### 8. **Copy URL to Remote**
```bash
rclone copyurl https://example.com/file.zip remote:path/

# Example:
rclone copyurl https://example.com/data.zip mydrive:Downloads/
```

---

## Mount Operations

### 1. **Mount Cloud Storage**
```bash
rclone mount remote:path /local/mount/point [flags]

# Example:
mkdir -p ~/cloud
rclone mount mydrive: ~/cloud --vfs-cache-mode writes

# Mount in background
rclone mount mydrive: ~/cloud --daemon --vfs-cache-mode full
```

### 2. **Unmount**
```bash
fusermount -u /local/mount/point

# Example:
fusermount -u ~/cloud
```

### 3. **Mount with Better Performance (Termux)**
```bash
rclone mount mydrive: ~/cloud \
  --vfs-cache-mode full \
  --vfs-cache-max-size 1G \
  --vfs-cache-max-age 24h \
  --buffer-size 64M \
  --dir-cache-time 72h \
  --daemon
```

### 4. **Check Mount Status**
```bash
# List mounted filesystems
df -h | grep rclone

# Or
mount | grep rclone
```

---

## Advanced Features

### 1. **Serve Cloud Storage via HTTP**
```bash
rclone serve http remote:path --addr :8080

# Example with authentication:
rclone serve http mydrive: --addr :8080 --user admin --pass mypassword

# Access at: http://localhost:8080
```

### 2. **Serve via WebDAV**
```bash
rclone serve webdav remote:path --addr :8081

# Example:
rclone serve webdav mydrive: --addr :8081
```

### 3. **Serve via FTP**
```bash
rclone serve ftp remote:path --addr :2121

# Example:
rclone serve ftp mydrive: --addr :2121 --user ftpuser --pass ftppass
```

### 4. **Crypt (Encrypted Remote)**
```bash
# First, create a crypt remote through config
rclone config create mycrypt crypt remote mydrive:encrypted

# Use it like any remote:
rclone copy /sdcard/private mycrypt:
rclone ls mycrypt:
```

### 5. **Filter Files**
```bash
# Include only specific files
rclone copy /sdcard/DCIM mydrive:Photos --include "*.jpg"

# Exclude specific files
rclone copy /sdcard/Documents mydrive:Backup --exclude "*.tmp"

# Multiple filters
rclone copy /sdcard/Music mydrive:Music \
  --include "*.mp3" \
  --include "*.flac" \
  --exclude "*/.cache/**"
```

### 6. **Bandwidth Limiting**
```bash
# Limit to 1MB/s
rclone copy /sdcard/videos mydrive:Videos --bwlimit 1M

# Limit upload only
rclone sync /sdcard/photos mydrive:Photos --bwlimit 500K

# Time-based limits (slower at night)
rclone copy /sdcard/large mydrive:Backup --bwlimit "08:00,512K 19:00,2M"
```

### 7. **Progress Monitoring**
```bash
# Show progress
rclone copy /sdcard/files mydrive:Backup --progress

# Detailed stats
rclone copy /sdcard/files mydrive:Backup --stats 5s --stats-one-line

# JSON output for scripting
rclone copy /sdcard/files mydrive:Backup --stats 1s --stats-one-line-json
```

### 8. **Resume Interrupted Transfers**
```bash
# Rclone automatically resumes by default
# Force check all files:
rclone copy /sdcard/files mydrive:Backup --check-first
```

---

## Useful Flags & Options

### Global Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--dry-run` | Test without making changes | `rclone sync /sdcard mydrive: --dry-run` |
| `-v` or `--verbose` | Verbose output | `rclone copy /sdcard mydrive: -v` |
| `-P` or `--progress` | Show progress | `rclone copy /sdcard mydrive: -P` |
| `--transfers=N` | Number of parallel transfers (default: 4) | `rclone copy /sdcard mydrive: --transfers=8` |
| `--checkers=N` | Number of checkers (default: 8) | `rclone sync /sdcard mydrive: --checkers=16` |
| `--bwlimit=BANDWIDTH` | Bandwidth limit | `rclone copy /sdcard mydrive: --bwlimit=1M` |
| `--exclude=PATTERN` | Exclude files | `rclone copy /sdcard mydrive: --exclude "*.tmp"` |
| `--include=PATTERN` | Include files | `rclone copy /sdcard mydrive: --include "*.jpg"` |
| `--min-size=SIZE` | Minimum file size | `rclone copy /sdcard mydrive: --min-size=1M` |
| `--max-size=SIZE` | Maximum file size | `rclone copy /sdcard mydrive: --max-size=100M` |
| `--log-file=FILE` | Log to file | `rclone sync /sdcard mydrive: --log-file=sync.log` |
| `--delete-during` | Delete during transfer | `rclone sync /sdcard mydrive: --delete-during` |
| `--backup-dir=DIR` | Move deleted files here | `rclone sync /sdcard mydrive: --backup-dir=mydrive:Trash` |
| `--track-renames` | Track file renames | `rclone sync /sdcard mydrive: --track-renames` |

### Performance Flags

```bash
# Maximum performance
rclone copy /sdcard/files mydrive:Backup \
  --transfers=16 \
  --checkers=16 \
  --buffer-size=64M \
  --drive-chunk-size=64M \
  --use-mmap

# Conservative (low bandwidth/resources)
rclone copy /sdcard/files mydrive:Backup \
  --transfers=2 \
  --checkers=4 \
  --bwlimit=500K
```

---

## Real-World Examples

### Example 1: Daily Photo Backup
```bash
#!/data/data/com.termux/files/usr/bin/bash
# Save as: backup-photos.sh

# Backup DCIM to Google Drive with logging
rclone copy /sdcard/DCIM mydrive:Backup/DCIM \
  --include "*.jpg" \
  --include "*.png" \
  --include "*.mp4" \
  --create-empty-src-dirs \
  --log-file=/sdcard/rclone-backup.log \
  --log-level INFO \
  --stats 1m \
  -P

echo "Backup completed at $(date)" >> /sdcard/rclone-backup.log
```

### Example 2: Sync Documents (Two-Way)
```bash
#!/data/data/com.termux/files/usr/bin/bash
# Save as: sync-docs.sh

# Two-way sync between local and cloud
rclone bisync /sdcard/Documents mydrive:Documents \
  --conflict-resolve newer \
  --verbose \
  --create-empty-src-dirs

# If first time, add --resync flag
```

### Example 3: Encrypted Backup
```bash
# First, create encrypted remote:
# rclone config create mycrypt crypt remote mydrive:encrypted

# Backup sensitive files encrypted
rclone copy /sdcard/Private mycrypt:Backup \
  --progress \
  --log-file=/sdcard/encrypted-backup.log
```

### Example 4: Automated Daily Sync with Termux
```bash
#!/data/data/com.termux/files/usr/bin/bash
# Save as: daily-sync.sh
# Schedule with: termux-job-scheduler

DATE=$(date +"%Y-%m-%d %H:%M:%S")
LOG="/sdcard/sync-log.txt"

echo "=== Sync started: $DATE ===" >> "$LOG"

# Sync multiple folders
rclone sync /sdcard/Documents mydrive:Backup/Documents \
  --exclude ".git/**" \
  --exclude "*.tmp" \
  --log-level INFO \
  --stats 1m

rclone copy /sdcard/DCIM mydrive:Photos \
  --include "*.{jpg,png,mp4}" \
  --log-level INFO

echo "=== Sync completed: $(date +"%Y-%m-%d %H:%M:%S") ===" >> "$LOG"

# Send notification
termux-notification -t "Rclone" -c "Backup completed successfully"
```

### Example 5: Download Entire Folder from Cloud
```bash
# Download with progress bar
rclone copy mydrive:SharedFolder /sdcard/Downloads/SharedFolder \
  -P \
  --transfers=4

# Or sync to keep up-to-date
rclone sync mydrive:SharedFolder /sdcard/Downloads/SharedFolder \
  --dry-run  # Test first!
```

### Example 6: Move Old Files to Archive
```bash
# Move files older than 30 days to archive
rclone move /sdcard/Downloads mydrive:Archive/Downloads \
  --min-age 30d \
  --delete-empty-src-dirs \
  -v
```

### Example 7: Compare Two Locations
```bash
# Check differences between local and remote
rclone check /sdcard/Documents mydrive:Backup/Documents \
  --one-way \
  --missing-on-dst missing-files.txt

# Full comparison (both directions)
rclone check /sdcard/Documents mydrive:Backup/Documents
```

### Example 8: Create Public Link (Google Drive)
```bash
# First, get file path
rclone lsf mydrive:SharedFiles

# Create public link
rclone link mydrive:SharedFiles/document.pdf
```

### Example 9: Mount Google Drive for File Browsing
```bash
# Install termux-auth for better integration
pkg install fuse2

# Create mount point
mkdir -p ~/gdrive

# Mount Google Drive
rclone mount mydrive: ~/gdrive \
  --vfs-cache-mode full \
  --vfs-cache-max-age 24h \
  --vfs-cache-max-size 1G \
  --allow-other \
  --daemon

# Access files
ls ~/gdrive
cd ~/gdrive/Documents
```

### Example 10: Batch Upload with Filter
```bash
# Upload only videos larger than 10MB
rclone copy /sdcard/Videos mydrive:Videos \
  --include "*.mp4" \
  --include "*.mkv" \
  --min-size 10M \
  --transfers=4 \
  -P
```

---

## Troubleshooting

### 1. Check Configuration
```bash
rclone config show
```

### 2. Test Connection
```bash
rclone ls remote: --max-depth 1
```

### 3. Verbose Debugging
```bash
rclone copy source dest -vv --log-file=debug.log
```

### 4. Clear VFS Cache
```bash
rclone rc vfs/forget
```

### 5. Check Rclone Status
```bash
rclone rc core/stats
```

### 6. Common Issues in Termux

**Permission Denied:**
```bash
# Allow storage access
termux-setup-storage

# Check permissions
ls -la /sdcard/
```

**Mount Issues:**
```bash
# Install required packages
pkg install fuse2 fuse3

# Unmount if stuck
fusermount -uz ~/mountpoint
```

**Out of Memory:**
```bash
# Reduce transfers and checkers
rclone copy source dest --transfers=2 --checkers=4
```

### 7. Reset Configuration
```bash
# Backup first
cp ~/.config/rclone/rclone.conf ~/rclone.conf.backup

# Reconfigure
rclone config
```

---

## Quick Reference Card

```bash
# CONFIGURATION
rclone config                    # Configure remotes
rclone listremotes               # List configured remotes
rclone about remote:             # Show storage info

# LISTING
rclone lsd remote:               # List directories
rclone ls remote:path            # List files recursively
rclone lsl remote:path           # List with details
rclone size remote:path          # Show directory size

# COPY & SYNC
rclone copy source: dest:        # Copy files (one-way)
rclone sync source: dest:        # Mirror (DELETES extras)
rclone bisync source: dest:      # Two-way sync
rclone move source: dest:        # Move files

# FILE OPERATIONS
rclone mkdir remote:path         # Create directory
rclone rmdir remote:path         # Remove empty directory
rclone delete remote:path        # Delete files
rclone purge remote:path         # Delete everything
rclone cat remote:path/file      # Display file content

# ADVANCED
rclone mount remote: ~/mount     # Mount as filesystem
rclone serve http remote:        # Serve via HTTP
rclone dedupe newest remote:     # Remove duplicates
rclone check source: dest:       # Compare locations

# USEFUL FLAGS
--dry-run                        # Test without changes
-P, --progress                   # Show progress
-v                               # Verbose output
--transfers=N                    # Parallel transfers
--bwlimit=1M                     # Bandwidth limit
--exclude "*.tmp"                # Exclude pattern
```

---

## Pro Tips for Termux Users

1. **Storage Access:** Always run `termux-setup-storage` first
2. **Battery:** Use `termux-wake-lock` for long operations
3. **Background:** Use `nohup` or `screen` for background tasks
4. **Scheduling:** Use `termux-job-scheduler` for automation
5. **Notifications:** Use `termux-notification` to alert completion
6. **Power:** Keep device charged during large transfers

### Example Background Script
```bash
#!/data/data/com.termux/files/usr/bin/bash
# background-sync.sh

termux-wake-lock

nohup rclone sync /sdcard/Documents mydrive:Backup \
  --log-file=/sdcard/sync.log \
  --stats 5m &

echo "Sync running in background. PID: $!"
echo "Check progress: tail -f /sdcard/sync.log"
```

---

## Additional Resources

- **Official Documentation:** https://rclone.org/docs/
- **Command Reference:** https://rclone.org/commands/
- **Forum:** https://forum.rclone.org/
- **GitHub Issues:** https://github.com/rclone/rclone/issues
- **GitHub Discussions:** https://github.com/rclone/rclone/discussions

---

## License

This guide is provided as-is for educational purposes. Rclone is licensed under MIT License.

**Last Updated:** October 2025  
**Rclone Version:** v1.68+  
**Compatible with:** Termux on Android 7.0+

---

**Remember:** Always test with `--dry-run` before performing destructive operations like `sync` or `purge`!
```

---

## Detailed Explanation and Usage

### How to Use This Guide

#### 1. **First Time Setup**

Start by configuring your first remote (e.g., Google Drive):

```bash
rclone config
```

Follow the interactive prompts:
1. Choose `n` for "New remote"
2. Enter a name (e.g., "mydrive")
3. Choose storage type (e.g., "drive" for Google Drive)
4. Follow authentication steps
5. Save and exit

#### 2. **Basic Operations**

Once configured, you can immediately start using these commands:

**List your files:**
```bash
rclone ls mydrive:
```

**Copy photos to cloud:**
```bash
rclone copy /sdcard/DCIM mydrive:Photos --progress
```

**Download from cloud:**
```bash
rclone copy mydrive:Documents /sdcard/Documents --progress
```

#### 3. **Understanding Sync vs Copy**

**COPY** - Only adds/updates files, never deletes:
```bash
rclone copy /sdcard/Documents mydrive:Backup
# Safe: Original files remain even if removed from source later
```

**SYNC** - Makes destination identical to source (DANGEROUS):
```bash
rclone sync /sdcard/Documents mydrive:Backup
# DANGER: Deletes files in mydrive:Backup not in /sdcard/Documents
```

**BISYNC** - Two-way synchronization (NEW):
```bash
rclone bisync /sdcard/Documents mydrive:Backup --resync  # First time
rclone bisync /sdcard/Documents mydrive:Backup           # Subsequent runs
# Safe: Syncs changes both directions with conflict handling
```

#### 4. **Daily Workflow Examples**

**Morning:** Backup photos taken yesterday
```bash
rclone copy /sdcard/DCIM mydrive:Photos \
  --max-age 24h \
  --progress
```

**Evening:** Sync documents both ways
```bash
rclone bisync /sdcard/Documents mydrive:Documents \
  --conflict-resolve newer \
  --verbose
```

**Weekly:** Archive old downloads
```bash
rclone move /sdcard/Download mydrive:Archive/Downloads \
  --min-age 7d \
  --delete-empty-src-dirs \
  -P
```

#### 5. **Safety Tips**

Always test first with `--dry-run`:
```bash
rclone sync /sdcard/Important mydrive:Backup --dry-run -v
```

Use `--backup-dir` for safety:
```bash
rclone sync /sdcard/Documents mydrive:Documents \
  --backup-dir mydrive:Trash \
  --suffix -backup-$(date +%Y%m%d)
```

Enable logging:
```bash
rclone copy /sdcard/files mydrive:Backup \
  --log-file=/sdcard/rclone.log \
  --log-level INFO
```

#### 6. **Performance Optimization**

For fast WiFi:
```bash
rclone copy /sdcard/Videos mydrive:Videos \
  --transfers=16 \
  --checkers=16 \
  --buffer-size=64M \
  -P
```

For slow/metered connection:
```bash
rclone copy /sdcard/Videos mydrive:Videos \
  --transfers=2 \
  --bwlimit=500K \
  -P
```

#### 7. **Automation in Termux**

Create a backup script (`~/backup.sh`):
```bash
#!/data/data/com.termux/files/usr/bin/bash

# Acquire wake lock
termux-wake-lock

# Run backup
rclone copy /sdcard/DCIM mydrive:Photos \
  --include "*.{jpg,png,mp4}" \
  --log-file=/sdcard/backup.log \
  -P

# Send notification
termux-notification -t "Backup Complete" -c "Photos backed up successfully"

# Release wake lock
termux-wake-unlock
```

Make it executable:
```bash
chmod +x ~/backup.sh
```

Run manually or schedule it.

---

## Key Concepts Explained

### 1. **Remote Syntax**
- `remote:` = root of remote storage
- `remote:path/to/folder` = specific folder
- `/sdcard/` = local Android storage

### 2. **Transfer Modes**
- **copy** = Add/update only
- **sync** = Make identical (deletes extras)
- **move** = Copy then delete source
- **bisync** = Two-way sync

### 3. **Filtering**
- `--include` = Only these files
- `--exclude` = Skip these files
- `--min-age` = Older than X
- `--max-age` = Newer than X
- `--min-size` / `--max-size` = Size filters

### 4. **VFS Cache Modes (for mounting)**
- `off` = No caching (slow but safe)
- `minimal` = Cache file structure only
- `writes` = Cache writes locally first
- `full` = Cache everything (fastest)

---

## Common Use Cases

### Use Case 1: Student - Document Backup
```bash
# Daily sync of study materials
rclone bisync ~/storage/documents mydrive:University \
  --conflict-resolve newer \
  --create-empty-src-dirs
```

### Use Case 2: Photographer - Photo Management
```bash
# Upload today's photos
rclone copy /sdcard/DCIM mydrive:Photography/2025 \
  --include "*.{jpg,JPG,raw,RAW}" \
  --max-age 24h \
  -P

# Archive photos older than 30 days
rclone move /sdcard/DCIM mydrive:Photography/Archive \
  --min-age 30d \
  --create-empty-src-dirs
```

### Use Case 3: Developer - Code Sync
```bash
# Sync git repositories
rclone sync ~/projects mydrive:Backup/Projects \
  --exclude ".git/**" \
  --exclude "node_modules/**" \
  --exclude "*.log" \
  --delete-excluded
```

### Use Case 4: Content Creator - Video Backup
```bash
# Upload rendered videos
rclone copy ~/Videos/Rendered mydrive:YouTube/Uploads \
  --include "*.mp4" \
  --min-size 10M \
  --transfers=4 \
  -P
```

---

This comprehensive guide covers everything you need to use rclone effectively in Termux as of 2025. The commands and features are based on the latest stable releases and official documentation.

**Citations:**
- Official Documentation: https://rclone.org/docs/
- GitHub Repository: https://github.com/rclone/rclone
- Command Reference: https://rclone.org/commands/
- Filtering Guide: https://rclone.org/filtering/
- Bisync Documentation: https://rclone.org/bisync/