--- 
title: "restic"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:21:52Z
tags: ["Cloud"]
draft: false
---

# Restic Backup Tool - Complete Guide for Termux (2025)

> **Version:** Latest stable (v0.17+)  
> **Platform:** Termux (Android)  
> **Official Website:** https://restic.net  
> **GitHub:** https://github.com/restic/restic  
> **Last Updated:** October 2025

---

## Table of Contents
1. [What is Restic?](#what-is-restic)
2. [Installation & Setup](#installation--setup)
3. [Repository Management](#repository-management)
4. [Backup Operations](#backup-operations)
5. [Restore Operations](#restore-operations)
6. [Snapshot Management](#snapshot-management)
7. [Repository Maintenance](#repository-maintenance)
8. [Advanced Features](#advanced-features)
9. [Supported Backends](#supported-backends)
10. [Real-World Examples](#real-world-examples)
11. [Automation & Scheduling](#automation--scheduling)
12. [Troubleshooting](#troubleshooting)

---

## What is Restic?

Restic is a modern backup program that is:
- **Fast** - Incremental backups with deduplication
- **Secure** - Encrypted by design (AES-256)
- **Efficient** - Only backs up changed data
- **Verifiable** - Can check backup integrity
- **Easy** - Simple command structure
- **Cross-platform** - Works everywhere

**Key Features:**
- Incremental backups with deduplication
- End-to-end encryption (AES-256)
- Multiple storage backends (local, cloud, SFTP, etc.)
- Snapshot-based backups
- Data verification and consistency checks
- Resume interrupted backups
- Compression support

**Why Restic over simple file copy?**
- Versioning (keep multiple backup versions)
- Deduplication (saves space)
- Encryption (security)
- Integrity checking (data verification)
- Incremental (only backs up changes)

---

## Installation & Setup

### Install Restic in Termux
```bash
pkg install restic
```

### Verify Installation
```bash
restic version
```

### Check Available Commands
```bash
restic help
```

### Get Help for Specific Command
```bash
restic help <command>

# Examples:
restic help backup
restic help restore
restic help snapshots
```

---

## Repository Management

### 1. Initialize a Repository

A repository is where restic stores your backups. You must initialize it once.

#### Local Repository (on SD card)
```bash
restic init --repo /sdcard/backup-repo

# You'll be prompted to enter a password (REMEMBER THIS!)
# Example:
# enter password for new repository: ********
# enter password again: ********
```

#### Cloud Repository (various backends)

**Amazon S3:**
```bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
restic init --repo s3:s3.amazonaws.com/bucket-name/restic
```

**Backblaze B2:**
```bash
export B2_ACCOUNT_ID="your-account-id"
export B2_ACCOUNT_KEY="your-account-key"
restic init --repo b2:bucket-name:restic
```

**Google Cloud Storage:**
```bash
export GOOGLE_PROJECT_ID="your-project-id"
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/credentials.json"
restic init --repo gs:bucket-name:/restic
```

**SFTP (SSH server):**
```bash
restic init --repo sftp:user@host:/backup/restic
```

**REST Server:**
```bash
restic init --repo rest:http://hostname:8000/
```

**Rclone Backend (use any rclone remote):**
```bash
restic init --repo rclone:remote:path
```

### 2. Using Password Files (Avoid Typing Password)

Create a password file:
```bash
echo "your-super-secret-password" > ~/.restic-password
chmod 600 ~/.restic-password
```

Use with commands:
```bash
restic --repo /sdcard/backup-repo --password-file ~/.restic-password snapshots
```

Or set as environment variable:
```bash
export RESTIC_PASSWORD_FILE=~/.restic-password
export RESTIC_REPOSITORY=/sdcard/backup-repo

# Now you can use shorter commands:
restic snapshots
```

### 3. Environment Variables

Set these to avoid typing repo and password every time:
```bash
# Add to ~/.bashrc or ~/.zshrc
export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Or for cloud:
export RESTIC_REPOSITORY="s3:s3.amazonaws.com/bucket/restic"
export AWS_ACCESS_KEY_ID="your-key"
export AWS_SECRET_ACCESS_KEY="your-secret"
```

Apply changes:
```bash
source ~/.bashrc
```

### 4. Check Repository Info
```bash
restic stats

# Detailed statistics
restic stats --mode raw-data
restic stats --mode restore-size
```

---

## Backup Operations

### 1. Basic Backup

```bash
restic backup /path/to/backup

# Examples:
restic backup /sdcard/Documents
restic backup /sdcard/DCIM /sdcard/Documents
restic backup ~/storage/downloads
```

### 2. Backup with Tags
```bash
restic backup /sdcard/Documents --tag documents --tag important

# Tags help organize and filter snapshots later
restic backup /sdcard/DCIM --tag photos --tag "$(date +%Y-%m)"
```

### 3. Backup with Description
```bash
restic backup /sdcard/Projects --tag work --tag "Project backup before major changes"
```

### 4. Exclude Files and Directories

#### Using patterns:
```bash
# Exclude specific patterns
restic backup /sdcard/Documents \
  --exclude "*.tmp" \
  --exclude "*.cache" \
  --exclude ".git"

# Exclude multiple patterns
restic backup /sdcard \
  --exclude "*.tmp" \
  --exclude "*.log" \
  --exclude "node_modules" \
  --exclude "__pycache__"
```

#### Using exclude file:
```bash
# Create exclude file
cat > ~/.restic-exclude <<EOF
*.tmp
*.log
*.cache
.git/
node_modules/
__pycache__/
.DS_Store
Thumbs.db
EOF

# Use exclude file
restic backup /sdcard/Documents --exclude-file ~/.restic-exclude
```

#### Exclude directories:
```bash
restic backup /sdcard \
  --exclude /sdcard/Android \
  --exclude /sdcard/.thumbnails \
  --exclude /sdcard/Download
```

### 5. Exclude with Larger Exclude File
```bash
# More comprehensive exclude file
cat > ~/.restic-exclude-comprehensive <<'EOF'
# System files
*.tmp
*.temp
*.log
*.cache
.DS_Store
Thumbs.db
desktop.ini

# Development
node_modules/
.git/
.svn/
.hg/
__pycache__/
*.pyc
*.pyo
.venv/
venv/

# Build outputs
dist/
build/
*.o
*.so
*.dll
*.exe

# Android specific
.thumbnails/
.android_secure/

# Media caches
*.part
*.crdownload
EOF

restic backup /sdcard --exclude-file ~/.restic-exclude-comprehensive
```

### 6. Include Only Specific Files
```bash
# Backup only specific file types
restic backup /sdcard/DCIM \
  --exclude "*" \
  --include "*.jpg" \
  --include "*.png" \
  --include "*.mp4"
```

### 7. Dry Run (Test Backup)
```bash
restic backup /sdcard/Documents --dry-run --verbose

# Shows what would be backed up without actually doing it
```

### 8. Backup with Progress
```bash
restic backup /sdcard/Documents --verbose

# Or with more detail:
restic backup /sdcard/Documents --verbose=2
```

### 9. One File System Only
```bash
# Don't cross filesystem boundaries
restic backup /sdcard --one-file-system
```

### 10. Backup with Custom Host
```bash
# Useful if backing up from multiple devices
restic backup /sdcard/Documents --host pixel7pro

# Later you can filter snapshots by host
```

---

## Restore Operations

### 1. List Snapshots
```bash
restic snapshots

# Output shows:
# ID        Time                 Host        Tags        Paths
# ----------------------------------------------------------------
# 4bba301e  2025-10-28 10:15:23  localhost   [documents] /sdcard/Documents
```

### 2. Restore Latest Snapshot
```bash
# Restore to original location
restic restore latest --target /

# Restore to different location
restic restore latest --target /sdcard/restore
```

### 3. Restore Specific Snapshot
```bash
# Get snapshot ID first
restic snapshots

# Restore specific snapshot
restic restore 4bba301e --target /sdcard/restore

# Or use short ID (first 8 chars)
restic restore 4bba301e --target /sdcard/restore
```

### 4. Restore Specific Files/Directories
```bash
# Restore only specific path
restic restore latest --target /sdcard/restore --include /sdcard/Documents/important.pdf

# Restore specific directory
restic restore latest --target /sdcard/restore --path /sdcard/Documents/Projects
```

### 5. Restore with Include/Exclude
```bash
# Restore only specific file types
restic restore latest --target /sdcard/restore \
  --include "*.pdf" \
  --include "*.docx"

# Restore everything except
restic restore latest --target /sdcard/restore \
  --exclude "*.tmp" \
  --exclude "*.log"
```

### 6. Restore from Specific Tag
```bash
# Find snapshots with tag
restic snapshots --tag photos

# Restore from tagged snapshot
restic restore latest --tag photos --target /sdcard/restore-photos
```

### 7. Restore Specific File Version
```bash
# List all versions of a file
restic find --path /sdcard/Documents/report.pdf

# Restore from specific snapshot
restic restore a1b2c3d4 --target /sdcard/restore --include /sdcard/Documents/report.pdf
```

### 8. Verify Before Restore
```bash
# Check what would be restored
restic ls latest --path /sdcard/Documents

# Or more detailed
restic ls latest /sdcard/Documents/important-folder
```

---

## Snapshot Management

### 1. List All Snapshots
```bash
restic snapshots

# More detailed view
restic snapshots --verbose

# Group by host, paths, tags
restic snapshots --group-by host
restic snapshots --group-by tags
```

### 2. List Snapshots with Filters
```bash
# By tag
restic snapshots --tag photos

# By host
restic snapshots --host pixel7pro

# By path
restic snapshots --path /sdcard/Documents

# Multiple filters
restic snapshots --tag important --host pixel7pro
```

### 3. Show Snapshot Details
```bash
# Show files in a snapshot
restic ls latest

# Show specific snapshot
restic ls 4bba301e

# Show with file details
restic ls latest --long
```

### 4. Compare Snapshots (Diff)
```bash
# Compare two snapshots
restic diff snapshot-id-1 snapshot-id-2

# Compare latest with previous
restic diff latest latest-1

# Example output shows Added, Removed, Modified files
```

### 5. Find Files in Snapshots
```bash
# Find file by name
restic find report.pdf

# Find by pattern
restic find "*.pdf"

# Find in specific path
restic find --path /sdcard/Documents "*.pdf"

# Case-insensitive search
restic find --ignore-case "readme"
```

### 6. Mount Snapshots (Browse as Filesystem)
```bash
# Mount all snapshots
mkdir -p ~/restic-mount
restic mount ~/restic-mount

# Now you can browse:
# ls ~/restic-mount/snapshots/latest/sdcard/Documents

# Unmount when done
fusermount -u ~/restic-mount
```

### 7. Dump File from Snapshot
```bash
# Print file contents from snapshot
restic dump latest /sdcard/Documents/notes.txt

# Save to file
restic dump latest /sdcard/Documents/report.pdf > ~/restored-report.pdf
```

### 8. Delete Snapshots (Forget)

⚠️ **Important:** Use `forget` to mark snapshots for deletion, then `prune` to actually remove data.

```bash
# Delete specific snapshot
restic forget 4bba301e

# Delete snapshots with tag
restic forget --tag old-backup

# Keep last N snapshots
restic forget --keep-last 10

# Keep snapshots by policy
restic forget \
  --keep-last 5 \
  --keep-daily 7 \
  --keep-weekly 4 \
  --keep-monthly 12 \
  --keep-yearly 3

# Apply to all snapshots with prune
restic forget --keep-last 10 --prune

# Dry run first
restic forget --keep-last 10 --dry-run
```

### 9. Retention Policies

```bash
# Comprehensive retention policy
restic forget \
  --keep-last 3 \          # Keep 3 most recent
  --keep-hourly 24 \       # Keep 24 hourly
  --keep-daily 7 \         # Keep 7 daily
  --keep-weekly 4 \        # Keep 4 weekly
  --keep-monthly 12 \      # Keep 12 monthly
  --keep-yearly 5 \        # Keep 5 yearly
  --prune                  # Remove unreferenced data

# Per tag retention
restic forget \
  --tag photos \
  --keep-last 10 \
  --keep-monthly 6
```

---

## Repository Maintenance

### 1. Check Repository Integrity
```bash
# Basic check
restic check

# Read all data (thorough but slow)
restic check --read-data

# Check specific percentage of data
restic check --read-data-subset=10%
```

### 2. Prune Repository (Remove Unused Data)
```bash
# Remove data not referenced by any snapshot
restic prune

# Dry run first
restic prune --dry-run

# Prune with compression (newer restic versions)
restic prune --repack-small

# More aggressive pruning
restic prune --max-unused 5%
```

### 3. Rebuild Index
```bash
# Rebuild repository index
restic rebuild-index
```

### 4. Optimize Repository
```bash
# Compact repository packs
restic prune --repack-small --max-unused 0

# This can significantly reduce repository size
```

### 5. Check Statistics
```bash
# Show repository statistics
restic stats

# Show raw data stats
restic stats --mode raw-data

# Show restore size (actual backup size)
restic stats --mode restore-size

# Show blobs and trees
restic stats --mode blobs-per-file
```

### 6. Copy Snapshots to Another Repository
```bash
# Copy all snapshots
restic copy --repo2 /path/to/other-repo

# Copy specific snapshots
restic copy --repo2 /path/to/other-repo latest

# Copy with tag
restic copy --repo2 /path/to/other-repo --tag important
```

### 7. Migrate Repository (Change Encryption)
```bash
# Migrate to new repository with different password
restic init --repo /new/repo
restic copy --from-repo /old/repo --repo /new/repo

# Then verify and delete old repo
```

---

## Advanced Features

### 1. Parallel Operations

```bash
# Use multiple CPU cores for backup
restic backup /sdcard/Documents -o local.connections=8

# For cloud backends
restic backup /sdcard/Documents -o s3.connections=16
```

### 2. Compression

```bash
# Compression is enabled by default in newer versions
# Check repository version
restic cat config

# For older repos, you can upgrade
restic migrate upgrade_repo_v2
```

### 3. Custom Caching

```bash
# Set cache directory
export RESTIC_CACHE_DIR=/sdcard/.restic-cache

# Disable cache
restic backup /sdcard/Documents --no-cache

# Clear cache
restic cache --cleanup
```

### 4. Limit Bandwidth

```bash
# Limit upload speed
restic backup /sdcard/Documents --limit-upload 1024  # 1 MB/s

# Limit download speed
restic restore latest --target /sdcard/restore --limit-download 2048  # 2 MB/s
```

### 5. JSON Output (for scripting)

```bash
# Backup with JSON output
restic backup /sdcard/Documents --json

# Snapshots as JSON
restic snapshots --json

# Parse with jq
restic snapshots --json | jq '.[-1].id'
```

### 6. Backup stdin

```bash
# Backup command output
tar -czf - /sdcard/Documents | restic backup --stdin --stdin-filename documents.tar.gz

# Backup database dump
mysqldump database | restic backup --stdin --stdin-filename database.sql

# Backup logs
journalctl | restic backup --stdin --stdin-filename system.log
```

### 7. Using Rclone Backend

```bash
# First configure rclone remote (e.g., "mydrive")
# Then use with restic:
export RESTIC_REPOSITORY="rclone:mydrive:restic-backup"
restic init
restic backup /sdcard/Documents
```

### 8. REST Server (Self-hosted Backend)

```bash
# Start REST server
restic serve --listen :8000 --path /backup/restic

# Use from client
export RESTIC_REPOSITORY="rest:http://server-ip:8000/"
restic init
restic backup /sdcard/Documents
```

---

## Supported Backends

### 1. Local
```bash
restic init --repo /sdcard/backup-repo
restic init --repo ~/backup
```

### 2. SFTP (SSH)
```bash
restic init --repo sftp:user@host:/backup/restic
restic init --repo sftp://user@host:2222//absolute/path
```

### 3. Amazon S3
```bash
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
restic init --repo s3:s3.amazonaws.com/bucket/restic
```

### 4. Backblaze B2
```bash
export B2_ACCOUNT_ID="..."
export B2_ACCOUNT_KEY="..."
restic init --repo b2:bucket-name:restic
```

### 5. Wasabi
```bash
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
restic init --repo s3:s3.wasabisys.com/bucket/restic
```

### 6. Azure Blob Storage
```bash
export AZURE_ACCOUNT_NAME="..."
export AZURE_ACCOUNT_KEY="..."
restic init --repo azure:container:/restic
```

### 7. Google Cloud Storage
```bash
export GOOGLE_PROJECT_ID="..."
export GOOGLE_APPLICATION_CREDENTIALS="..."
restic init --repo gs:bucket:/restic
```

### 8. Rclone (Any rclone remote)
```bash
restic init --repo rclone:remote:path
```

### 9. REST Server
```bash
restic init --repo rest:http://hostname:8000/
restic init --repo rest:https://user:pass@hostname:8000/
```

### 10. Swift (OpenStack)
```bash
export OS_AUTH_URL="..."
export OS_USERNAME="..."
export OS_PASSWORD="..."
export OS_REGION_NAME="..."
export OS_TENANT_NAME="..."
restic init --repo swift:container:/restic
```

---

## Real-World Examples

### Example 1: Daily Photo Backup Script

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-photos.sh

# Configuration
export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Acquire wake lock
termux-wake-lock

# Backup photos
restic backup /sdcard/DCIM \
  --tag photos \
  --tag "$(date +%Y-%m)" \
  --exclude "*.thumbnails" \
  --exclude ".trashed*" \
  --verbose

# Keep retention policy
restic forget \
  --keep-daily 7 \
  --keep-weekly 4 \
  --keep-monthly 12 \
  --prune

# Check integrity (once a week)
if [ "$(date +%u)" -eq 7 ]; then
  restic check --read-data-subset=10%
fi

# Notify
termux-notification -t "Restic Backup" -c "Photo backup completed"

# Release wake lock
termux-wake-unlock

# Log
echo "Backup completed at $(date)" >> /sdcard/backup.log
```

Make executable:
```bash
chmod +x ~/backup-photos.sh
```

### Example 2: Complete System Backup

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-full.sh

export RESTIC_REPOSITORY="rclone:mydrive:restic-backup"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Create exclude file
cat > /tmp/restic-exclude <<EOF
/sdcard/Android/data/*
/sdcard/Android/obb/*
/sdcard/.thumbnails/*
/sdcard/Download/*.part
/sdcard/Download/*.crdownload
*.tmp
*.temp
*.cache
EOF

# Backup with exclusions
restic backup \
  /sdcard/Documents \
  /sdcard/DCIM \
  /sdcard/Pictures \
  /sdcard/Music \
  /data/data/com.termux/files/home \
  --tag full-backup \
  --tag "$(hostname)" \
  --exclude-file /tmp/restic-exclude \
  --verbose

# Retention
restic forget \
  --keep-last 3 \
  --keep-daily 7 \
  --keep-weekly 4 \
  --keep-monthly 6 \
  --keep-yearly 2 \
  --prune

# Statistics
restic stats --mode restore-size

# Cleanup
rm /tmp/restic-exclude
```

### Example 3: Incremental Document Backup

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Backup only changed documents

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Backup documents
restic backup /sdcard/Documents \
  --tag documents \
  --exclude "*.tmp" \
  --exclude ".git/" \
  --exclude "node_modules/" \
  --one-file-system \
  --verbose

# Show what changed
restic diff latest latest-1
```

### Example 4: Scheduled Backup with Cron

```bash
# Install termux-services
pkg install cronie termux-services

# Restart termux services
sv-enable crond

# Edit crontab
crontab -e

# Add daily backup at 2 AM
0 2 * * * /data/data/com.termux/files/home/backup-photos.sh

# Or every 6 hours
0 */6 * * * /data/data/com.termux/files/home/backup-docs.sh
```

### Example 5: Backup Before System Change

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Pre-update snapshot

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Create snapshot before system update
restic backup \
  /data/data/com.termux/files/home \
  /sdcard/Documents \
  --tag pre-update \
  --tag "$(date +%Y-%m-%d)" \
  --verbose

echo "Snapshot created. You can now proceed with updates."
```

### Example 6: Restore After Data Loss

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Emergency restore script

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Show available snapshots
echo "Available snapshots:"
restic snapshots

# Restore latest
echo ""
read -p "Restore latest snapshot? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
  restic restore latest \
    --target /sdcard/restore-$(date +%Y%m%d-%H%M%S) \
    --verbose
  echo "Restore completed in /sdcard/restore-$(date +%Y%m%d-%H%M%S)"
fi
```

### Example 7: Cloud Backup with Progress

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Backup to cloud with progress monitoring

export RESTIC_REPOSITORY="s3:s3.amazonaws.com/my-backup/restic"
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Backup with progress
restic backup /sdcard/Documents \
  --verbose=2 \
  --tag cloud-backup \
  --exclude-file ~/.restic-exclude

# Show statistics
restic stats --mode restore-size
```

### Example 8: Automated Photo Archive

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Archive old photos to cloud

export RESTIC_REPOSITORY="b2:my-archive-bucket:restic"
export B2_ACCOUNT_ID="..."
export B2_ACCOUNT_KEY="..."
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Backup photos older than 30 days
find /sdcard/DCIM -type f -mtime +30 -name "*.jpg" -o -name "*.png" | \
  while read file; do
    restic backup "$file" --tag archive --tag "$(date +%Y-%m)"
  done

# Remove local copies (CAREFUL!)
# find /sdcard/DCIM -type f -mtime +30 -delete
```

### Example 9: Verify Backup Integrity

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Weekly verification script

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

echo "Starting integrity check..."

# Check repository structure
restic check

# Check actual data (10% sample)
restic check --read-data-subset=10%

# Show statistics
echo ""
echo "Repository Statistics:"
restic stats

# Log results
if [ $? -eq 0 ]; then
  echo "$(date): Integrity check PASSED" >> /sdcard/restic-check.log
  termux-notification -t "Restic" -c "Integrity check passed"
else
  echo "$(date): Integrity check FAILED" >> /sdcard/restic-check.log
  termux-notification -t "Restic ERROR" -c "Integrity check failed!"
fi
```

### Example 10: Multi-Device Backup Sync

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Backup from multiple devices to same repo

export RESTIC_REPOSITORY="sftp:user@server:/backup/restic"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Use device-specific host identifier
DEVICE_NAME="$(getprop ro.product.model | tr ' ' '-')"

# Backup with device tag
restic backup /sdcard/Documents \
  --host "$DEVICE_NAME" \
  --tag "$(whoami)" \
  --tag "$DEVICE_NAME" \
  --verbose

# List snapshots from this device only
restic snapshots --host "$DEVICE_NAME"
```

---

## Automation & Scheduling

### Method 1: Termux Cron

```bash
# Install cronie
pkg install cronie termux-services

# Enable cron service
sv-enable crond

# Edit crontab
crontab -e

# Examples:
# Daily at 3 AM
0 3 * * * /data/data/com.termux/files/home/backup-full.sh

# Every 6 hours
0 */6 * * * /data/data/com.termux/files/home/backup-docs.sh

# Weekly on Sunday at 4 AM
0 4 * * 0 /data/data/com.termux/files/home/backup-weekly.sh

# List cron jobs
crontab -l
```

### Method 2: Termux-job-scheduler (Requires Termux:Boot)

```bash
# Install Termux:Boot from F-Droid or Play Store

# Create boot script
mkdir -p ~/.termux/boot
cat > ~/.termux/boot/backup-scheduler.sh <<'EOF'
#!/data/data/com.termux/files/usr/bin/bash

# Schedule daily backup at 2 AM
termux-job-scheduler \
  --script ~/backup-daily.sh \
  --trigger-time 02:00 \
  --period-hours 24
EOF

chmod +x ~/.termux/boot/backup-scheduler.sh
```

### Method 3: Simple Loop Script

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Continuous backup daemon

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

while true; do
  # Backup every 6 hours
  restic backup /sdcard/Documents --tag auto
  
  # Cleanup old snapshots
  restic forget --keep-last 10 --prune
  
  # Sleep for 6 hours
  sleep 21600
done
```

### Method 4: Backup on Event (File Change)

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Backup when files change (requires inotify-tools)

pkg install inotify-tools

export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Watch directory for changes
inotifywait -m -r -e modify,create,delete /sdcard/Documents | \
  while read path action file; do
    echo "Detected $action on $file"
    
    # Wait 60 seconds for more changes
    sleep 60
    
    # Backup
    restic backup /sdcard/Documents --tag auto-sync
  done
```

---

## Troubleshooting

### 1. Common Issues

#### Repository Locked
```bash
# If backup was interrupted
restic unlock

# Check locks
restic list locks

# Remove specific lock
restic unlock --remove-all
```

#### Out of Memory
```bash
# Reduce memory usage
restic backup /sdcard --verbose \
  -o local.connections=2

# Or disable caching
restic backup /sdcard --no-cache
```

#### Slow Backup
```bash
# Increase parallelism
restic backup /sdcard -o local.connections=8

# Use larger pack size
restic backup /sdcard -o pack-size=64
```

#### Permission Denied
```bash
# In Termux, ensure storage permission
termux-setup-storage

# Check file permissions
ls -la /sdcard/Documents
```

### 2. Repository Issues

#### Corrupted Repository
```bash
# Check integrity
restic check

# Rebuild index
restic rebuild-index

# Check with data verification
restic check --read-data
```

#### Repository Too Large
```bash
# Check what's using space
restic stats --mode raw-data

# Prune aggressively
restic prune --max-unused 0

# Consider forget policy
restic forget --keep-last 5 --prune
```

### 3. Verification

#### Verify Backup Success
```bash
# List latest snapshot
restic snapshots latest

# Check specific files
restic ls latest | grep important.pdf

# Compare with original
restic diff $(restic snapshots --last | awk '{print $1}')
```

#### Test Restore
```bash
# Restore single file to verify
restic restore latest \
  --target /tmp/test-restore \
  --include /sdcard/Documents/test.txt

# Compare checksums
sha256sum /sdcard/Documents/test.txt /tmp/test-restore/sdcard/Documents/test.txt
```

### 4. Performance Tuning

```bash
# For fast local storage
restic backup /sdcard \
  -o local.connections=16 \
  --pack-size=128

# For cloud storage
restic backup /sdcard \
  -o s3.connections=32 \
  --limit-upload=5120  # 5 MB/s

# For limited memory
restic backup /sdcard \
  -o local.connections=2 \
  --no-cache
```

### 5. Debug Mode

```bash
# Enable debug output
restic backup /sdcard --verbose=3

# With log file
restic backup /sdcard \
  --verbose=2 \
  2>&1 | tee /sdcard/restic-debug.log
```

### 6. Recovery Scenarios

#### Lost Repository Password
```bash
# Unfortunately, there's NO WAY to recover without password
# Restic uses strong encryption - this is by design
# Always keep multiple copies of your password!
```

#### Partial Data Loss
```bash
# Try to recover what's possible
restic check --read-data

# If some packs are missing
restic rebuild-index
restic check
```

#### Migrate to New Repository
```bash
# Create new repo
restic init --repo /new/backup

# Copy all snapshots
restic copy --repo /old/backup --repo2 /new/backup

# Verify
restic --repo /new/backup snapshots
```

---

## Quick Reference Card

```bash
# REPOSITORY
restic init --repo /path              # Initialize repository
restic snapshots                       # List all snapshots
restic check                           # Verify integrity
restic stats                           # Show statistics

# BACKUP
restic backup /path                    # Create backup
restic backup /path --tag name         # Backup with tag
restic backup /path --exclude "*.tmp"  # Exclude patterns
restic backup /path --dry-run          # Test backup

# RESTORE
restic restore latest --target /dest   # Restore latest
restic restore ID --target /dest       # Restore specific
restic mount /mnt                      # Mount as filesystem
restic dump latest /file               # Extract single file

# SNAPSHOTS
restic snapshots --tag name            # Filter by tag
restic ls latest                       # List files
restic diff ID1 ID2                    # Compare snapshots
restic find "*.pdf"                    # Search files

# MAINTENANCE
restic forget --keep-last 10           # Mark old snapshots
restic prune                           # Remove unused data
restic forget --keep-last 10 --prune   # Combined
restic rebuild-index                   # Rebuild index
restic unlock                          # Remove locks

# USEFUL FLAGS
--verbose, -v                          # Verbose output
--dry-run                              # Test without changes
--json                                 # JSON output
--no-cache                             # Disable cache
--limit-upload N                       # Limit upload speed
--limit-download N                     # Limit download speed
```

---

## Environment Variables Quick Setup

```bash
# Add to ~/.bashrc or ~/.zshrc

# For local repository
export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# For S3-compatible storage
export RESTIC_REPOSITORY="s3:s3.amazonaws.com/bucket/restic"
export AWS_ACCESS_KEY_ID="your-key-id"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# For Backblaze B2
export RESTIC_REPOSITORY="b2:bucket-name:restic"
export B2_ACCOUNT_ID="your-account-id"
export B2_ACCOUNT_KEY="your-account-key"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# For rclone backend
export RESTIC_REPOSITORY="rclone:remote:path"
export RESTIC_PASSWORD_FILE="~/.restic-password"

# Apply changes
source ~/.bashrc
```

---

## Best Practices

### 1. Security
- ✅ Use strong, unique password for repository
- ✅ Store password securely (password manager)
- ✅ Keep backup of repository password
- ✅ Use encrypted remote (cloud) storage
- ✅ Enable 2FA on cloud accounts

### 2. Reliability
- ✅ Test backups regularly (restore test)
- ✅ Run `restic check` periodically
- ✅ Monitor backup logs
- ✅ Use multiple backup destinations
- ✅ Keep offsite backups (3-2-1 rule)

### 3. Efficiency
- ✅ Use appropriate retention policies
- ✅ Run `prune` regularly
- ✅ Exclude unnecessary files
- ✅ Use tags for organization
- ✅ Optimize for your network/storage

### 4. Automation
- ✅ Schedule regular backups
- ✅ Automate maintenance tasks
- ✅ Set up notifications
- ✅ Log all operations
- ✅ Handle errors gracefully

---

## Comparison: Restic vs Rclone

| Feature | Restic | Rclone |
|---------|--------|--------|
| **Purpose** | Backup tool | Sync/copy tool |
| **Encryption** | Built-in (required) | Optional (crypt remote) |
| **Deduplication** | Yes (block-level) | No |
| **Versioning** | Yes (snapshots) | No (unless using crypt) |
| **Incremental** | Yes (automatic) | Yes (with sync) |
| **Storage Format** | Specialized | Native files |
| **Recovery** | Requires restic | Direct file access |
| **Best For** | Versioned backups | File synchronization |

**Use Restic when:**
- You need versioned backups
- You want deduplication
- You need guaranteed encryption
- You want to restore from any point in time

**Use Rclone when:**
- You need simple file sync
- You want direct file access
- You're copying between cloud services
- You need to mount cloud storage

**Use Both:**
- Restic for backups, Rclone as transport
- Example: `export RESTIC_REPOSITORY="rclone:remote:backup"`

---

## Additional Resources

- **Official Documentation:** https://restic.readthedocs.io/
- **GitHub:** https://github.com/restic/restic
- **Forum:** https://forum.restic.net/
- **Security Design:** https://restic.readthedocs.io/en/latest/100_references.html
- **Comparison to other tools:** https://restic.readthedocs.io/en/latest/110_comparison.html

---

## Complete Backup Strategy Example

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Complete backup solution for Termux

# === CONFIGURATION ===
export RESTIC_REPOSITORY="rclone:mydrive:restic-backup"
export RESTIC_PASSWORD_FILE="~/.restic-password"
LOG_FILE="/sdcard/restic-backup.log"

# === FUNCTIONS ===
log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

notify() {
  termux-notification -t "Restic Backup" -c "$1"
}

# === MAIN BACKUP ===
log "=== Starting backup ==="
termux-wake-lock

# Backup critical data
restic backup \
  /sdcard/Documents \
  /sdcard/DCIM \
  /data/data/com.termux/files/home \
  --tag "$(date +%Y-%m)" \
  --tag "$(hostname)" \
  --exclude-file ~/.restic-exclude \
  --verbose 2>&1 | tee -a "$LOG_FILE"

if [ $? -eq 0 ]; then
  log "Backup successful"
  
  # Apply retention policy
  log "Applying retention policy..."
  restic forget \
    --keep-last 5 \
    --keep-daily 7 \
    --keep-weekly 4 \
    --keep-monthly 12 \
    --keep-yearly 2 \
    --prune 2>&1 | tee -a "$LOG_FILE"
  
  # Verify (once a week)
  if [ "$(date +%u)" -eq 7 ]; then
    log "Running integrity check..."
    restic check --read-data-subset=5% 2>&1 | tee -a "$LOG_FILE"
  fi
  
  notify "Backup completed successfully"
else
  log "Backup FAILED"
  notify "❌ Backup FAILED - check logs"
fi

termux-wake-unlock
log "=== Backup finished ==="
```

---

**Last Updated:** October 2025  
**Restic Version:** v0.17+  
**Compatible with:** Termux on Android 7.0+

**Remember:** Restic is designed for security - there is NO way to recover data without your password. Always keep multiple secure copies of your repository password!

```

---

## Detailed Explanation and How to Use

### Understanding Restic Concepts

#### 1. **Repository**
- A repository is like a safe where all your backups are stored
- It's encrypted with your password
- Can be local (SD card) or remote (cloud)
- You initialize it once with `restic init`

#### 2. **Snapshots**
- Each backup creates a "snapshot" - a point-in-time copy
- Snapshots are incremental (only changed data is stored)
- You can restore from any snapshot
- Old snapshots can be deleted to save space

#### 3. **Deduplication**
- Restic breaks files into blocks
- Identical blocks are stored only once
- Saves massive amounts of space
- Works across all files and all snapshots

#### 4. **Encryption**
- ALL data is encrypted before leaving your device
- Uses AES-256 encryption
- Even file names are encrypted
- Cloud provider can't read your data

---

### Getting Started (Step by Step)

#### Step 1: Create Password File
```bash
# Create secure password
echo "MyVerySecurePassword123!" > ~/.restic-password
chmod 600 ~/.restic-password

# IMPORTANT: Back this up somewhere safe!
# Without it, your backups are unrecoverable
```

#### Step 2: Initialize Repository
```bash
# For local backups
export RESTIC_REPOSITORY="/sdcard/backup-repo"
export RESTIC_PASSWORD_FILE="~/.restic-password"
restic init

# For cloud (example with rclone)
export RESTIC_REPOSITORY="rclone:mydrive:restic-backup"
restic init
```

#### Step 3: Create Your First Backup
```bash
# Simple backup
restic backup /sdcard/Documents

# Check what was backed up
restic snapshots
```

#### Step 4: Restore Data
```bash
# List available snapshots
restic snapshots

# Restore latest
restic restore latest --target /sdcard/restored
```

---

### Daily Usage Patterns

#### Pattern 1: Quick Photo Backup
```bash
# Morning - backup new photos
restic backup /sdcard/DCIM --tag morning-photos

# Check backup
restic snapshots --tag morning-photos
```

#### Pattern 2: Document Version Control
```bash
# Before editing important document
restic backup /sdcard/Documents/thesis.pdf --tag before-edit

# After editing
restic backup /sdcard/Documents/thesis.pdf --tag after-edit

# If you need old version
restic find thesis.pdf
restic restore <snapshot-id> --target /tmp --include thesis.pdf
```

#### Pattern 3: Full System Backup
```bash
# Weekly full backup
restic backup \
  /sdcard/Documents \
  /sdcard/DCIM \
  /sdcard/Music \
  /data/data/com.termux/files/home \
  --tag weekly-full
```

---

### Understanding Retention Policies

```bash
# Keep structure:
--keep-last 3      # 3 most recent snapshots
--keep-hourly 24   # One per hour for 24 hours
--keep-daily 7     # One per day for 7 days
--keep-weekly 4    # One per week for 4 weeks
--keep-monthly 12  # One per month for 12 months
--keep-yearly 2    # One per year for 2 years

# Example timeline:
# Today:     3 backups (keep-last 3)
# Yesterday: 1 backup (keep-daily)
# Last week: 1 backup (keep-weekly)
# Last month: 1 backup (keep-monthly)
# Last year: 1 backup (keep-yearly)
```

---

### Practical Scenarios

#### Scenario 1: Accidental File Deletion
```bash
# Oh no! Deleted important file yesterday

# Find the file
restic find important-report.pdf

# Shows which snapshots contain it
# Restore from snapshot before deletion
restic restore abc123def --target ~/recovered --include important-report.pdf
```

#### Scenario 2: Ransomware Protection
```bash
# Your device got infected, files encrypted

# Restic backups are immutable and encrypted
# Restore from clean snapshot before infection
restic snapshots  # Find last good snapshot
restic restore <clean-snapshot-id> --target /sdcard/clean-restore
```

#### Scenario 3: Compare File Versions
```bash
# Check how document evolved

# List all snapshots with the file
restic find proposal.pdf

# Compare two versions
restic diff snapshot1 snapshot2

# Extract specific version
restic dump snapshot1 /sdcard/Documents/proposal.pdf > proposal-v1.pdf
restic dump snapshot2 /sdcard/Documents/proposal.pdf > proposal-v2.pdf
diff proposal-v1.pdf proposal-v2.pdf
```

---

### Performance Tips

#### For Fast Backups:
```bash
restic backup /sdcard \
  -o local.connections=16 \
  --pack-size=128
```

#### For Slow Connections:
```bash
restic backup /sdcard \
  --limit-upload=1024 \  # 1 MB/s
  -o s3.connections=4
```

#### For Limited Memory:
```bash
restic backup /sdcard \
  -o local.connections=2 \
  --no-cache
```

---

This guide provides everything you need to use restic effectively in Termux. The tool is extremely powerful and the encryption means your data is always secure, even when stored on untrusted cloud services.

**Key Takeaway:** Restic = Encrypted + Deduplicated + Versioned + Verifiable Backups