--- 
title: "restic-rclone"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:22:52Z
tags: ["Cloud"]
draft: false
---

# Restic + Rclone: Complete Integration Guide for Termux (2025)

> **Combining the Best of Both Worlds**  
> Restic's powerful backup + Rclone's cloud connectivity  
> **Optimized for Free Google Drive & Dropbox Accounts**  
> **Last Updated:** October 2025

---

## Table of Contents
1. [Why Restic + Rclone?](#why-restic--rclone)
2. [Understanding API Limits](#understanding-api-limits)
3. [Initial Setup](#initial-setup)
4. [Configuration for Free Accounts](#configuration-for-free-accounts)
5. [Optimized Backup Strategies](#optimized-backup-strategies)
6. [Multi-Cloud Setup](#multi-cloud-setup)
7. [Advanced Rate Limit Avoidance](#advanced-rate-limit-avoidance)
8. [Complete Backup Scripts](#complete-backup-scripts)
9. [Monitoring & Maintenance](#monitoring--maintenance)
10. [Troubleshooting Rate Limits](#troubleshooting-rate-limits)
11. [Real-World Examples](#real-world-examples)

---

## Why Restic + Rclone?

### The Power Combination

**Restic provides:**
- ✅ Encryption (AES-256)
- ✅ Deduplication (saves space)
- ✅ Versioning (snapshots)
- ✅ Verification (integrity checks)
- ✅ Incremental backups

**Rclone provides:**
- ✅ 70+ cloud storage backends
- ✅ Reliable transfers
- ✅ Resume capability
- ✅ Bandwidth management
- ✅ Error handling

**Together they enable:**
- 🚀 Encrypted backups to any cloud
- 💰 Use free cloud storage securely
- 🔄 Switch providers anytime
- 📊 Optimal performance
- 🛡️ Maximum security

---

## Understanding API Limits

### Google Drive (Free Account - 15 GB)

**API Quotas (2025):**
- **Queries per day:** 1,000,000,000 (billion) - practically unlimited
- **Queries per 100 seconds per user:** 1,000
- **Queries per 100 seconds:** 10,000
- **Upload limit:** No official limit, but throttled after ~750 GB/day
- **Download limit:** Throttled after ~10 TB/day

**Practical Limits:**
- **Small files (<10 MB):** ~10 requests/second sustained
- **Large files (>100 MB):** ~2-3 concurrent uploads
- **Daily usage:** Stay under 50 GB/day to avoid throttling
- **Burst protection:** Space operations at least 0.1-0.2 seconds apart

**Rate Limit Errors:**
```
403: Rate Limit Exceeded
403: User Rate Limit Exceeded
429: Too Many Requests
```

### Dropbox (Free Account - 2 GB)

**API Quotas (2025):**
- **API calls per hour:** ~6,000 (varies by endpoint)
- **Upload limit:** 150 MB per file for single upload
- **Chunk upload:** Up to 350 GB per file
- **Bandwidth:** Generally unlimited but throttled
- **Files per folder:** 300,000 files

**Practical Limits:**
- **Small operations:** ~1-2 per second
- **Large uploads:** ~1 concurrent upload
- **Daily usage:** Stay under 20 GB/day
- **Recommended:** Use chunked uploads for files >10 MB

**Rate Limit Errors:**
```
429: Too Many Requests
503: Service Unavailable (temporary)
```

### Key Differences

| Feature | Google Drive (Free) | Dropbox (Free) |
|---------|---------------------|----------------|
| **Storage** | 15 GB | 2 GB |
| **Daily Limit** | ~50 GB practical | ~20 GB practical |
| **API Tolerance** | Higher | Lower |
| **Best For** | Large backups | Small/frequent |
| **Restic Packs** | Works well | Needs tuning |

---

## Initial Setup

### Step 1: Install Required Tools

```bash
# Ensure both are installed
pkg install restic rclone

# Verify versions
restic version  # Should be 0.17+
rclone version  # Should be 1.68+
```

### Step 2: Configure Rclone Remotes

#### Configure Google Drive

```bash
rclone config

# Interactive steps:
n  # New remote
gdrive  # Name it
drive  # Choose Google Drive
# Leave client_id blank (or use your own)
# Leave client_secret blank
1  # Full access
# Leave root_folder_id blank
# Leave service_account_file blank
n  # No advanced config
y  # Use web browser for auth
# Browser opens - authorize
y  # Looks good
q  # Quit config

# Test connection
rclone lsd gdrive:
```

#### Configure Dropbox

```bash
rclone config

# Interactive steps:
n  # New remote
dropbox  # Name it
dropbox  # Choose Dropbox
# Leave client_id blank
# Leave client_secret blank
n  # No advanced config
y  # Use web browser for auth
# Browser opens - authorize
y  # Looks good
q  # Quit config

# Test connection
rclone lsd dropbox:
```

### Step 3: Create Backup Directories

```bash
# Create directories in cloud storage
rclone mkdir gdrive:Backups/Restic
rclone mkdir dropbox:Backups/Restic

# Verify
rclone lsd gdrive:Backups
rclone lsd dropbox:Backups
```

### Step 4: Initialize Restic Repositories

#### For Google Drive

```bash
# Set password (SAVE THIS SECURELY!)
echo "your-super-secure-password-gdrive" > ~/.restic-password-gdrive
chmod 600 ~/.restic-password-gdrive

# Initialize repository
export RESTIC_REPOSITORY="rclone:gdrive:Backups/Restic"
export RESTIC_PASSWORD_FILE=~/.restic-password-gdrive

restic init

# Verify
restic snapshots
restic stats
```

#### For Dropbox

```bash
# Set password (can be different or same)
echo "your-super-secure-password-dropbox" > ~/.restic-password-dropbox
chmod 600 ~/.restic-password-dropbox

# Initialize repository
export RESTIC_REPOSITORY="rclone:dropbox:Backups/Restic"
export RESTIC_PASSWORD_FILE=~/.restic-password-dropbox

restic init

# Verify
restic snapshots
restic stats
```

---

## Configuration for Free Accounts

### Optimized Rclone Configuration

Create/edit `~/.config/rclone/rclone.conf`:

#### Google Drive Optimized Settings

```ini
[gdrive]
type = drive
scope = drive
token = {"access_token":"...","token_type":"Bearer","refresh_token":"...","expiry":"..."}
team_drive = 
root_folder_id = 

# OPTIMIZATIONS FOR FREE ACCOUNT
# Reduce parallel operations to avoid rate limits
# For restic, this is crucial

[gdrive-restic]
type = drive
scope = drive
token = {"access_token":"...","token_type":"Bearer","refresh_token":"...","expiry":"..."}

# Rate limiting to avoid API quota
tpslimit = 2
# 2 transactions per second - safe for free accounts

tpslimit_burst = 5
# Allow small bursts up to 5

# Chunk size for uploads (restic packs)
chunk_size = 64M
# Larger chunks = fewer API calls

# Upload cutoff - use chunked upload for files larger than this
upload_cutoff = 8M

# Acknowledge Google Drive abuse check
acknowledge_abuse = true

# Keep retrying on errors
retries = 10

# Exponential backoff for retries
low_level_retries = 20

# Enable server-side copy when possible
server_side_across_configs = true
```

#### Dropbox Optimized Settings

```ini
[dropbox]
type = dropbox
token = {"access_token":"...","token_type":"bearer","refresh_token":"...","expiry":"..."}

# OPTIMIZATIONS FOR FREE ACCOUNT (Stricter limits)

[dropbox-restic]
type = dropbox
token = {"access_token":"...","token_type":"bearer","refresh_token":"...","expiry":"..."}

# Even stricter rate limiting
tpslimit = 1
# 1 transaction per second for Dropbox

tpslimit_burst = 3
# Small burst allowance

# Chunk size for uploads
chunk_size = 48M
# Dropbox chunk upload API limit is 150MB per chunk
# Use 48MB for safety and efficiency

# Upload cutoff
upload_cutoff = 10M

# Retry configuration
retries = 10
low_level_retries = 20

# Enable batch operations where possible
batch_mode = async
batch_size = 20
batch_timeout = 10s
```

### Restic Configuration for Cloud Backends

Create `~/.config/restic/config.sh`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# === GOOGLE DRIVE CONFIGURATION ===
export RESTIC_REPOSITORY_GDRIVE="rclone:gdrive-restic:Backups/Restic"
export RESTIC_PASSWORD_FILE_GDRIVE=~/.restic-password-gdrive

# Rclone options for Google Drive
export RCLONE_DRIVE_CHUNK_SIZE="64M"
export RCLONE_DRIVE_UPLOAD_CUTOFF="8M"
export RCLONE_TPSLIMIT="2"
export RCLONE_TPSLIMIT_BURST="5"
export RCLONE_RETRIES="10"
export RCLONE_LOW_LEVEL_RETRIES="20"

# === DROPBOX CONFIGURATION ===
export RESTIC_REPOSITORY_DROPBOX="rclone:dropbox-restic:Backups/Restic"
export RESTIC_PASSWORD_FILE_DROPBOX=~/.restic-password-dropbox

# Rclone options for Dropbox
export RCLONE_DROPBOX_CHUNK_SIZE="48M"
export RCLONE_DROPBOX_UPLOAD_CUTOFF="10M"

# === RESTIC OPTIMIZATIONS ===
# Pack size - critical for reducing API calls
# Larger packs = fewer files = fewer API calls
# But too large = more memory usage
export RESTIC_PACK_SIZE="67108864"  # 64 MB

# Connection limits to avoid overwhelming API
export RESTIC_CONNECTIONS="2"  # Max 2 parallel operations

# Cache directory
export RESTIC_CACHE_DIR="/sdcard/.restic-cache"

# Compression (enabled by default in newer versions)
export RESTIC_COMPRESSION="auto"

# Timeouts
export RESTIC_IO_TIMEOUT="5m"
```

Source this file before using restic:
```bash
source ~/.config/restic/config.sh
```

Or add to `~/.bashrc`:
```bash
echo "source ~/.config/restic/config.sh" >> ~/.bashrc
```

---

## Optimized Backup Strategies

### Strategy 1: Low-Frequency, Large Backups (Google Drive)

**Best for:** Weekly/monthly full backups

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-gdrive-weekly.sh

source ~/.config/restic/config.sh

# Use Google Drive
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

# Optimize for large backup
restic backup /sdcard/Documents /sdcard/DCIM \
  --tag weekly \
  --tag "$(date +%Y-W%V)" \
  --exclude-file ~/.restic-exclude \
  -o rclone.connections=2 \
  -o rclone.transfers=2 \
  --pack-size 64 \
  --verbose

# Apply retention
restic forget \
  --keep-last 4 \
  --keep-weekly 8 \
  --keep-monthly 12 \
  --prune

# Log
echo "Weekly backup completed: $(date)" >> /sdcard/backup-weekly.log
```

### Strategy 2: High-Frequency, Small Backups (Dropbox)

**Best for:** Daily document backups

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-dropbox-daily.sh

source ~/.config/restic/config.sh

# Use Dropbox
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_DROPBOX"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_DROPBOX"

# Optimize for small, frequent backup
restic backup /sdcard/Documents \
  --tag daily \
  --tag "$(date +%Y-%m-%d)" \
  --exclude "*.tmp" \
  --exclude ".git/" \
  -o rclone.connections=1 \
  -o rclone.transfers=1 \
  --pack-size 32 \
  --verbose

# Minimal retention for space
restic forget \
  --keep-daily 7 \
  --keep-weekly 4 \
  --prune

echo "Daily backup completed: $(date)" >> /sdcard/backup-daily.log
```

### Strategy 3: Smart Incremental (Both)

**Best for:** Backing up different data types to appropriate services

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-smart.sh

source ~/.config/restic/config.sh

# === PHOTOS TO GOOGLE DRIVE (larger storage) ===
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

echo "Backing up photos to Google Drive..."
restic backup /sdcard/DCIM \
  --tag photos \
  --tag "$(date +%Y-%m)" \
  --exclude "*.thumbnails" \
  -o rclone.connections=2 \
  --pack-size 64 \
  --verbose

# === DOCUMENTS TO DROPBOX (faster for small files) ===
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_DROPBOX"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_DROPBOX"

echo "Backing up documents to Dropbox..."
restic backup /sdcard/Documents \
  --tag documents \
  --tag "$(date +%Y-%m-%d)" \
  --exclude "*.tmp" \
  -o rclone.connections=1 \
  --pack-size 32 \
  --verbose

echo "Smart backup completed: $(date)"
```

### Strategy 4: Time-Based Throttling

**Best for:** Running during off-peak hours

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-throttled.sh

source ~/.config/restic/config.sh

HOUR=$(date +%H)

# Determine connection settings based on time
if [ $HOUR -ge 2 ] && [ $HOUR -le 6 ]; then
  # Night time: More aggressive (less likely to hit limits)
  CONNECTIONS=4
  TRANSFERS=4
  TPSLIMIT=3
  echo "Using night-time settings (aggressive)"
else
  # Day time: Conservative
  CONNECTIONS=1
  TRANSFERS=1
  TPSLIMIT=1
  echo "Using day-time settings (conservative)"
fi

export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"
export RCLONE_TPSLIMIT="$TPSLIMIT"

restic backup /sdcard/Documents \
  -o rclone.connections=$CONNECTIONS \
  -o rclone.transfers=$TRANSFERS \
  --verbose

echo "Throttled backup completed: $(date)"
```

---

## Multi-Cloud Setup

### Strategy: Split Data Across Services

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-multi-cloud.sh

source ~/.config/restic/config.sh

# === CONFIGURATION ===
# Google Drive: 15 GB - Use for photos and videos
# Dropbox: 2 GB - Use for documents

# === PHOTOS TO GOOGLE DRIVE ===
echo "=== Backing up PHOTOS to Google Drive ==="
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"
export RCLONE_TPSLIMIT="2"

restic backup /sdcard/DCIM /sdcard/Pictures \
  --tag media \
  --tag photos \
  --exclude "*.thumbnails" \
  --exclude ".trashed*" \
  -o rclone.connections=2 \
  --pack-size 64 \
  --verbose

# Check storage usage
echo "Google Drive usage:"
restic stats --mode restore-size

# === DOCUMENTS TO DROPBOX ===
echo ""
echo "=== Backing up DOCUMENTS to Dropbox ==="
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_DROPBOX"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_DROPBOX"
export RCLONE_TPSLIMIT="1"

restic backup /sdcard/Documents \
  --tag documents \
  --exclude "*.tmp" \
  --exclude ".git/" \
  --exclude "node_modules/" \
  -o rclone.connections=1 \
  --pack-size 32 \
  --verbose

# Check storage usage
echo "Dropbox usage:"
restic stats --mode restore-size

# === MAINTENANCE ===
echo ""
echo "=== Applying retention policies ==="

# Google Drive: Keep more (more space)
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"
restic forget --keep-daily 14 --keep-weekly 8 --keep-monthly 12 --prune

# Dropbox: Keep less (limited space)
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_DROPBOX"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_DROPBOX"
restic forget --keep-daily 7 --keep-weekly 4 --prune

echo ""
echo "=== Multi-cloud backup completed at $(date) ==="
```

### Strategy: Redundant Backup (Same Data, Both Services)

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-redundant.sh

source ~/.config/restic/config.sh

BACKUP_PATH="/sdcard/Documents"
TAG="redundant-$(date +%Y%m%d)"

# === PRIMARY: Google Drive ===
echo "=== PRIMARY backup to Google Drive ==="
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

restic backup "$BACKUP_PATH" \
  --tag primary \
  --tag "$TAG" \
  -o rclone.connections=2 \
  --verbose

PRIMARY_STATUS=$?

# === SECONDARY: Dropbox ===
echo ""
echo "=== SECONDARY backup to Dropbox ==="
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_DROPBOX"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_DROPBOX"

restic backup "$BACKUP_PATH" \
  --tag secondary \
  --tag "$TAG" \
  -o rclone.connections=1 \
  --verbose

SECONDARY_STATUS=$?

# === REPORT ===
echo ""
echo "=== Backup Report ==="
if [ $PRIMARY_STATUS -eq 0 ]; then
  echo "✅ Google Drive: SUCCESS"
else
  echo "❌ Google Drive: FAILED"
fi

if [ $SECONDARY_STATUS -eq 0 ]; then
  echo "✅ Dropbox: SUCCESS"
else
  echo "❌ Dropbox: FAILED"
fi

# Notify
if [ $PRIMARY_STATUS -eq 0 ] && [ $SECONDARY_STATUS -eq 0 ]; then
  termux-notification -t "Backup Complete" -c "Both backups successful"
else
  termux-notification -t "Backup Warning" -c "One or more backups failed"
fi
```

---

## Advanced Rate Limit Avoidance

### Technique 1: Exponential Backoff Wrapper

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-with-backoff.sh

source ~/.config/restic/config.sh

# Exponential backoff function
restic_with_backoff() {
  local max_attempts=5
  local timeout=1
  local attempt=1
  local exit_code=0

  while [ $attempt -le $max_attempts ]; do
    echo "Attempt $attempt of $max_attempts..."
    
    "$@"
    exit_code=$?
    
    if [ $exit_code -eq 0 ]; then
      echo "Success!"
      return 0
    fi
    
    # Check if it's a rate limit error
    if [ $exit_code -ne 0 ]; then
      echo "Failed with exit code $exit_code"
      
      if [ $attempt -lt $max_attempts ]; then
        echo "Waiting $timeout seconds before retry..."
        sleep $timeout
        timeout=$((timeout * 2))  # Exponential backoff
        attempt=$((attempt + 1))
      else
        echo "Max attempts reached. Giving up."
        return $exit_code
      fi
    fi
  done
  
  return $exit_code
}

# Use the function
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

restic_with_backoff restic backup /sdcard/Documents \
  --tag smart-backup \
  -o rclone.connections=2 \
  --verbose
```

### Technique 2: Split Large Backups

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-split.sh

source ~/.config/restic/config.sh

export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

# Split into smaller chunks with delays
FOLDERS=(
  "/sdcard/Documents/Projects"
  "/sdcard/Documents/Personal"
  "/sdcard/Documents/Work"
  "/sdcard/DCIM/Camera"
  "/sdcard/DCIM/Screenshots"
)

for folder in "${FOLDERS[@]}"; do
  if [ -d "$folder" ]; then
    echo "Backing up: $folder"
    
    restic backup "$folder" \
      --tag split-backup \
      --tag "$(basename "$folder")" \
      -o rclone.connections=1 \
      --verbose
    
    # Wait between backups to avoid rate limits
    echo "Waiting 60 seconds before next backup..."
    sleep 60
  else
    echo "Skipping non-existent: $folder"
  fi
done

echo "Split backup completed"
```

### Technique 3: Rate Limit Monitor

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-monitored.sh

source ~/.config/restic/config.sh

export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

# Log file for monitoring
LOG="/sdcard/restic-rate-monitor.log"

# Run backup with detailed logging
restic backup /sdcard/Documents \
  --verbose=2 \
  -o rclone.connections=2 \
  2>&1 | tee -a "$LOG"

EXIT_CODE=${PIPESTATUS[0]}

# Check for rate limit errors
if grep -qi "rate.*limit\|429\|403.*quota" "$LOG"; then
  echo "⚠️  RATE LIMIT DETECTED at $(date)" | tee -a "$LOG"
  
  # Send notification
  termux-notification \
    -t "Rate Limit Hit" \
    -c "Backup encountered rate limits. Check log."
  
  # Reduce aggressiveness for next run
  echo "Reducing connection limits..."
  # You could dynamically adjust rclone.conf here
fi

if [ $EXIT_CODE -eq 0 ]; then
  echo "✅ Backup successful at $(date)" | tee -a "$LOG"
else
  echo "❌ Backup failed with code $EXIT_CODE at $(date)" | tee -a "$LOG"
fi
```

### Technique 4: Quota-Aware Backup

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-quota-aware.sh

source ~/.config/restic/config.sh

export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

# Check current usage
echo "Checking current repository size..."
CURRENT_SIZE=$(restic stats --mode raw-data --json | jq -r '.total_size')
CURRENT_SIZE_MB=$((CURRENT_SIZE / 1024 / 1024))

echo "Current repository size: ${CURRENT_SIZE_MB} MB"

# Google Drive free tier: 15 GB (15360 MB)
# Leave 1 GB buffer for safety
MAX_SIZE_MB=14336

if [ $CURRENT_SIZE_MB -gt $MAX_SIZE_MB ]; then
  echo "⚠️  WARNING: Repository size exceeds safe limit!"
  echo "Running aggressive pruning..."
  
  restic forget \
    --keep-last 2 \
    --keep-daily 3 \
    --prune
  
  echo "Pruning complete. Check size:"
  restic stats --mode raw-data
  
  termux-notification \
    -t "Restic Warning" \
    -c "Repository size exceeded limit. Pruned old snapshots."
else
  echo "✅ Repository size OK. Proceeding with backup..."
  
  restic backup /sdcard/Documents \
    --tag quota-aware \
    -o rclone.connections=2 \
    --verbose
fi
```

---

## Complete Backup Scripts

### Script 1: Conservative Daily Backup (Foolproof)

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-conservative.sh
# This script is VERY conservative to avoid any rate limits

set -e  # Exit on error

# Load configuration
source ~/.config/restic/config.sh

# === CONFIGURATION ===
LOG_FILE="/sdcard/backup-conservative.log"
BACKUP_PATHS=(
  "/sdcard/Documents"
  "/sdcard/DCIM"
)

# Use Google Drive (more generous limits)
export RESTIC_REPOSITORY="$RESTIC_REPOSITORY_GDRIVE"
export RESTIC_PASSWORD_FILE="$RESTIC_PASSWORD_FILE_GDRIVE"

# Ultra-conservative rclone settings
export RCLONE_TPSLIMIT="1"  # Only 1 transaction per second
export RCLONE_TPSLIMIT_BURST="2"

# === LOGGING ===
log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# === START ===
log "=== Starting conservative backup ==="
termux-wake-lock

# Create exclude file
cat > /tmp/restic-exclude <<'EOF'
*.tmp
*.temp
*.cache
*.log
.thumbnails/
.trashed*/
node_modules/
.git/
__pycache__/
*.pyc
Android/data/*/cache/
EOF

# Backup each path separately with delays
for path in "${BACKUP_PATHS[@]}"; do
  if [ -d "$path" ]; then
    log "Backing up: $path"
    
    # Ultra-conservative: only 1 connection, small pack size
    restic backup "$path" \
      --tag conservative \
      --tag "$(date +%Y-%m-%d)" \
      --exclude-file /tmp/restic-exclude \
      -o rclone.connections=1 \
      -o rclone.transfers=1 \
      --pack-size 32 \
      --verbose 2>&1 | tee -a "$LOG_FILE"
    
    if [ $? -eq 0 ]; then
      log "✅ Successfully backed up: $path"
    else
      log "❌ Failed to backup: $path"
    fi
    
    # Long delay between paths to be extra safe
    log "Waiting 120 seconds before next backup..."
    sleep 120
  else
    log "⚠️  Path not found: $path"
  fi
done

# === RETENTION ===
log "Applying retention policy..."
restic forget \
  --keep-daily 7 \
  --keep-weekly 4 \
  --keep-monthly 6 \
  --prune 2>&1 | tee -a "$LOG_FILE"

# === STATISTICS ===
log "Repository statistics:"
restic stats --mode restore-size 2>&1 | tee -a "$LOG_FILE"

# === CLEANUP ===
rm -f /tmp/restic-exclude
termux-wake-unlock

log "=== Backup completed ==="

# Notification
termux-notification \
  -t "Conservative Backup" \
  -c "Backup completed successfully" \
  --button1 "View Log" \
  --button1-action "termux-open $LOG_FILE"
```

### Script 2: Aggressive Weekend Backup (When You Have Time)

```bash
#!/data/data/com.termux/files/usr/bin/bash
# save as: ~/backup-weekend.sh
# Run on weekends when you can tolerate some rate limiting

source ~/.config/restic/config.sh

LOG_FILE="/sdcard/backup-weekend.log"

log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

log "=== Starting weekend full backup ==="
termux-wake-lock

# Use Google Drive (larger storage)
export RESTIC_REPOSITORY="$RESTIC_