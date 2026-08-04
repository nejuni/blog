--- 
title: "aria2"
description: "readme"
date: 2025-10-28T14:02:52Z
lastmod: 2025-10-28T14:02:52Z
tags: ["Download"]
draft: false
---

# 📥 Aria2 - Complete Guide for Termux (2025)

## Table of Contents
- [About Aria2](#about-aria2)
- [Basic Usage](#basic-usage)
- [Essential Daily Commands](#essential-daily-commands)
- [Advanced Features](#advanced-features)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)

---

## About Aria2

**Aria2** is a lightweight, multi-protocol & multi-source command-line download utility. It supports HTTP/HTTPS, FTP, SFTP, BitTorrent, and Metalink.

- **Official Repository**: https://github.com/aria2/aria2
- **Version**: 1.37.0+ (latest as of 2025)
- **License**: GPL-2.0

### Key Features
- Multi-connection downloading (up to 16 connections per file)
- BitTorrent support with DHT, PEX, encryption
- Metalink support
- RPC interface for remote control
- Resume capability
- Bandwidth throttling

---

## Basic Usage

### Simple Download
```bash
# Download a single file
aria2c https://example.com/file.zip

# Download with custom filename
aria2c -o myfile.zip https://example.com/file.zip

# Download to specific directory
aria2c -d /sdcard/Download https://example.com/file.zip
```

---

## Essential Daily Commands

### 1. **Fast Multi-Connection Download**
```bash
# Use 16 connections (maximum speed)
aria2c -x 16 -s 16 https://example.com/largefile.iso

# -x: maximum connections per server
# -s: split file into N pieces
```

**Example:**
```bash
aria2c -x 16 -s 16 https://releases.ubuntu.com/22.04/ubuntu-22.04-desktop-amd64.iso
```

---

### 2. **Resume Interrupted Downloads**
```bash
# Aria2 automatically resumes if .aria2 control file exists
aria2c -c https://example.com/file.zip

# -c or --continue: continue downloading partially downloaded file
```

**Example:**
```bash
# First attempt (interrupted)
aria2c -x 16 https://example.com/largefile.zip
# Resume later
aria2c -c -x 16 https://example.com/largefile.zip
```

---

### 3. **Download Multiple Files**
```bash
# From a text file (one URL per line)
aria2c -i urls.txt

# With connections per file
aria2c -i urls.txt -x 8 -j 3

# -j: maximum concurrent downloads
```

**Example urls.txt:**
```
https://example.com/file1.zip
https://example.com/file2.mp4
https://example.com/file3.pdf
```

**Command:**
```bash
aria2c -i urls.txt -x 8 -j 2 -d /sdcard/Download
```

---

### 4. **Limit Download Speed**
```bash
# Limit to 1MB/s (useful for background downloads)
aria2c --max-download-limit=1M https://example.com/file.zip

# Limit overall speed
aria2c --max-overall-download-limit=2M -i urls.txt
```

**Example:**
```bash
# Limit to 500KB/s for mobile data saving
aria2c --max-download-limit=500K -x 4 https://example.com/file.zip
```

---

### 5. **BitTorrent Downloads**
```bash
# Download torrent file
aria2c file.torrent

# Download from magnet link
aria2c 'magnet:?xt=urn:btih:...'

# Limit upload speed (seeding)
aria2c --seed-time=0 file.torrent  # Don't seed
aria2c --max-upload-limit=100K file.torrent  # Limit to 100KB/s
```

**Example:**
```bash
# Download Ubuntu via torrent with limited seeding
aria2c --seed-time=5 --max-upload-limit=50K ubuntu-22.04-desktop-amd64.iso.torrent
```

---

### 6. **Download with Authentication**
```bash
# HTTP Basic Authentication
aria2c --http-user=username --http-passwd=password https://example.com/file.zip

# FTP Authentication
aria2c ftp://username:password@ftp.example.com/file.zip
```

**Example:**
```bash
aria2c --http-user=john --http-passwd=secret123 https://private.example.com/data.zip
```

---

### 7. **Download with Proxy**
```bash
# HTTP Proxy
aria2c --all-proxy=http://proxy.example.com:8080 https://example.com/file.zip

# SOCKS5 Proxy
aria2c --all-proxy=socks5://127.0.0.1:1080 https://example.com/file.zip
```

**Example:**
```bash
aria2c --all-proxy=http://192.168.1.1:8080 -x 16 https://example.com/file.zip
```

---

### 8. **Monitor Download Progress**
```bash
# Show detailed progress
aria2c --summary-interval=0 https://example.com/file.zip

# Quiet mode (no output)
aria2c -q https://example.com/file.zip

# Log to file
aria2c --log=download.log --log-level=notice https://example.com/file.zip
```

---

### 9. **Verify Downloaded Files**
```bash
# Check checksum automatically from .sha256 or .md5 file
aria2c --checksum=sha-256=abc123... https://example.com/file.zip

# Metalink with built-in verification
aria2c file.metalink
```

**Example:**
```bash
aria2c --checksum=sha-256=e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855 https://example.com/file.zip
```

---

### 10. **Control Concurrent Downloads**
```bash
# Download maximum 5 files simultaneously
aria2c -j 5 -i urls.txt

# With split per file
aria2c -j 3 -x 8 -i urls.txt

# -j: concurrent downloads
# -x: connections per download
```

---

## Advanced Features

### Using aria2 RPC (Remote Control)

**1. Start aria2 as daemon:**
```bash
aria2c --enable-rpc --rpc-listen-all=false --rpc-listen-port=6800

# With authentication
aria2c --enable-rpc --rpc-secret=mytoken123 --rpc-listen-port=6800
```

**2. Background daemon with config:**
```bash
aria2c --enable-rpc --rpc-listen-port=6800 -D --conf-path=/path/to/aria2.conf
```

**3. Control via web UI:**
- Install webui-aria2: https://github.com/ziahamza/webui-aria2
- Or use: http://ariang.mayswind.net/latest (online interface)

---

### Configuration File

**Create config file:** `~/.aria2/aria2.conf`

```bash
# Create directory
mkdir -p ~/.aria2

# Create config
nano ~/.aria2/aria2.conf
```

**Sample Configuration:**
```conf
# Basic Options
dir=/sdcard/Download
continue=true
max-concurrent-downloads=5
max-connection-per-server=16
split=16
min-split-size=1M

# Speed Limits
max-download-limit=0
max-upload-limit=100K

# BitTorrent
bt-max-peers=50
bt-seed-unverified=false
seed-time=0
bt-tracker-connect-timeout=10

# RPC
enable-rpc=true
rpc-listen-port=6800
rpc-secret=your_secret_token_here

# Advanced
file-allocation=none
disk-cache=32M
check-certificate=true
user-agent=Mozilla/5.0
```

**Use config file:**
```bash
aria2c --conf-path=~/.aria2/aria2.conf https://example.com/file.zip
```

---

### Useful Command Combinations

**1. Fast download with resume and logging:**
```bash
aria2c -x 16 -s 16 -c --log=download.log --log-level=notice -d /sdcard/Download https://example.com/file.zip
```

**2. Batch download with speed limit:**
```bash
aria2c -i urls.txt -j 3 -x 8 --max-download-limit=2M -d /sdcard/Download
```

**3. Torrent download with minimal seeding:**
```bash
aria2c --seed-time=1 --max-upload-limit=50K -d /sdcard/Torrents file.torrent
```

**4. Download with retry and timeout:**
```bash
aria2c -x 16 --retry-wait=10 --max-tries=5 --timeout=60 https://example.com/file.zip
```

---

## Practical Examples

### Example 1: Download Large File (Linux ISO)
```bash
aria2c -x 16 -s 16 -c \
  -d /sdcard/Download \
  --summary-interval=0 \
  https://releases.ubuntu.com/22.04/ubuntu-22.04-desktop-amd64.iso
```

### Example 2: Download YouTube Video (with yt-dlp)
```bash
# First, get direct URL with yt-dlp
yt-dlp -g "https://youtube.com/watch?v=VIDEO_ID" > url.txt

# Then download with aria2
aria2c -x 16 -i url.txt -o video.mp4
```

### Example 3: Download Multiple Files from Text List
```bash
# Create urls.txt
cat > urls.txt << EOF
https://example.com/file1.zip
https://example.com/file2.pdf
https://example.com/file3.mp4
EOF

# Download all with 4 concurrent downloads
aria2c -j 4 -x 8 -c -d /sdcard/Download -i urls.txt
```

### Example 4: Download with Progress Notification
```bash
# Download and notify when complete (Termux)
aria2c -x 16 https://example.com/file.zip && termux-notification --title "Download Complete" --content "File downloaded successfully"
```

### Example 5: Scheduled Download (Night Download)
```bash
# Using at command in Termux
echo "aria2c -x 16 https://example.com/largefile.zip" | at 02:00

# Or using cron
# Add to crontab: 0 2 * * * aria2c -x 16 https://example.com/largefile.zip
```

---

## Important Options Reference

| Option | Description | Example |
|--------|-------------|---------|
| `-x N` | Max connections per server | `-x 16` |
| `-s N` | Split file into N pieces | `-s 16` |
| `-j N` | Max concurrent downloads | `-j 5` |
| `-c` | Continue/resume download | `-c` |
| `-d DIR` | Download directory | `-d /sdcard/Download` |
| `-o FILE` | Output filename | `-o myfile.zip` |
| `-i FILE` | Input file with URLs | `-i urls.txt` |
| `--max-download-limit=SPEED` | Limit download speed | `--max-download-limit=1M` |
| `--max-upload-limit=SPEED` | Limit upload speed | `--max-upload-limit=100K` |
| `--seed-time=MINUTES` | Seed time for torrents | `--seed-time=0` |
| `--conf-path=PATH` | Config file path | `--conf-path=~/.aria2/aria2.conf` |
| `--log=FILE` | Log file path | `--log=download.log` |
| `--checksum=TYPE=DIGEST` | Verify checksum | `--checksum=sha-256=abc...` |

---

## Troubleshooting

### Issue 1: "No URI to download"
**Solution:** Check URL format and quotes for special characters
```bash
aria2c 'https://example.com/file?id=123&key=abc'
```

### Issue 2: Download fails with SSL error
**Solution:** Skip certificate check (use cautiously)
```bash
aria2c --check-certificate=false https://example.com/file.zip
```

### Issue 3: Slow download speed
**Solution:** Increase connections and split
```bash
aria2c -x 16 -s 16 --min-split-size=1M https://example.com/file.zip
```

### Issue 4: Cannot resume download
**Solution:** Ensure .aria2 control file exists in same directory
```bash
# Check for .aria2 file
ls -la | grep .aria2

# Force resume
aria2c -c https://example.com/file.zip
```

### Issue 5: Permission denied in Termux
**Solution:** Request storage permission
```bash
termux-setup-storage
aria2c -d /sdcard/Download https://example.com/file.zip
```

---

## Best Practices

1. **Always use `-c` for large downloads** to enable resume capability
2. **Set appropriate connection limits** (-x 16 is optimal for most cases)
3. **Use config file** for consistent settings
4. **Monitor disk space** before downloading large files
5. **Limit upload speed** for torrents to save bandwidth
6. **Use download lists** (`-i`) for batch downloads
7. **Enable logging** for troubleshooting
8. **Verify checksums** when available

---

## Useful Scripts

### Auto-Resume Script
Save as `~/bin/aria2-resume.sh`:
```bash
#!/data/data/com.termux/files/usr/bin/bash
# Auto-resume all incomplete downloads in a directory

cd "$1" || exit 1
for file in *.aria2; do
    url=$(head -n 1 "$file")
    echo "Resuming: $url"
    aria2c -c -x 16 "$url"
done
```

### Batch Download with Notification
```bash
#!/data/data/com.termux/files/usr/bin/bash
aria2c -j 3 -x 8 -i "$1" && \
termux-notification \
  --title "Batch Download Complete" \
  --content "All files downloaded successfully"
```

---

## Additional Resources

- **Official Documentation**: https://aria2.github.io/manual/en/html/
- **GitHub Repository**: https://github.com/aria2/aria2
- **Web UI (AriaNg)**: https://github.com/mayswind/AriaNg
- **Termux Wiki**: https://wiki.termux.com/wiki/Aria2

---

## Quick Reference Card

```bash
# Fast download
aria2c -x 16 -s 16 URL

# Resume download
aria2c -c URL

# Batch download
aria2c -i urls.txt -j 3

# Limit speed
aria2c --max-download-limit=1M URL

# Torrent
aria2c --seed-time=0 file.torrent

# With config
aria2c --conf-path=~/.aria2/aria2.conf URL
```

---

**Last Updated**: 2025  
**Aria2 Version**: 1.37.0+  
**Tested on**: Termux (Android)

---

# Explanation & How to Use

## Understanding aria2 Architecture

Aria2 works by:
1. **Splitting files** into multiple segments
2. **Downloading segments** simultaneously from multiple connections
3. **Assembling segments** after download completes
4. **Verifying integrity** if checksums provided

## Step-by-Step Usage Guide

### Getting Started (First Time)

**Step 1: Verify Installation**
```bash
aria2c --version
```

**Step 2: Set up storage permissions (Termux)**
```bash
termux-setup-storage
```

**Step 3: Create download directory**
```bash
mkdir -p /sdcard/Download
```

**Step 4: Simple first download**
```bash
aria2c -d /sdcard/Download https://speed.hetzner.de/100MB.bin
```

### Daily Usage Workflow

**For Single Files:**
```bash
# Copy URL
# Open Termux
aria2c -x 16 -s 16 -c -d /sdcard/Download "PASTE_URL_HERE"
```

**For Multiple Files:**
```bash
# Create list
nano urls.txt
# Paste URLs (one per line)
# Save (Ctrl+X, Y, Enter)
aria2c -i urls.txt -j 3 -x 8 -d /sdcard/Download
```

**For Torrents:**
```bash
# Download .torrent file first or copy magnet link
aria2c --seed-time=0 /sdcard/Download/file.torrent
```

### Understanding Key Parameters

**`-x 16`**: Opens 16 connections to the same server
- More connections = faster download (if server allows)
- Default is 1, maximum is 16
- Use 8-16 for large files

**`-s 16`**: Splits file into 16 pieces
- Each piece downloaded separately
- Allows parallel downloading
- Best when equal to `-x` value

**`-j 3`**: Downloads 3 files simultaneously
- Useful for batch downloads
- Don't set too high (causes congestion)
- Recommend 3-5 for mobile

**`-c`**: Continue/resume
- Must-use for large files
- Reads .aria2 control file
- Automatically resumes from last position

### Real-World Examples Explained

**Example A: Download Movie (2GB)**
```bash
aria2c -x 16 -s 16 -c \
  --max-download-limit=5M \
  -d /sdcard/Movies \
  --log=movie-download.log \
  "https://example.com/movie.mp4"
```

**Explanation:**
- `-x 16 -s 16`: Maximum speed with 16 connections
- `-c`: Can resume if interrupted
- `--max-download-limit=5M`: Limit to 5MB/s (won't drain all bandwidth)
- `-d /sdcard/Movies`: Save to Movies folder
- `--log`: Keep log for troubleshooting

---

**Example B: Download Software Package List**
```bash
# urls.txt contains:
https://example.com/app1.apk
https://example.com/app2.apk
https://example.com/app3.apk

aria2c -i urls.txt \
  -j 2 \
  -x 8 \
  -c \
  -d /sdcard/Download/Apps \
  --summary-interval=0
```

**Explanation:**
- `-j 2`: Download 2 apps simultaneously
- `-x 8`: Each app uses 8 connections
- `-c`: Resume any interrupted app download
- `--summary-interval=0`: Show real-time progress

---

**Example C: Background Large Download**
```bash
nohup aria2c -x 16 -s 16 \
  --max-download-limit=2M \
  -d /sdcard/Download \
  -c \
  "https://example.com/large-file.iso" \
  > download.log 2>&1 &
```

**Explanation:**
- `nohup`: Keeps running even if terminal closes
- `&`: Runs in background
- `> download.log 2>&1`: Saves all output to log
- Limit speed to avoid detection/throttling

---

## Advanced Configuration Explained

### Creating Optimal Config File

```bash
# Create config directory
mkdir -p ~/.aria2

# Create config file
cat > ~/.aria2/aria2.conf << 'EOF'
# === BASIC ===
dir=/sdcard/Download
continue=true
input-file=/sdcard/Download/aria2-session.txt
save-session=/sdcard/Download/aria2-session.txt

# === CONNECTION ===
max-concurrent-downloads=5
max-connection-per-server=16
split=16
min-split-size=1M

# === SPEED ===
max-download-limit=0
max-overall-download-limit=0

# === BITTORRENT ===
bt-max-peers=50
bt-request-peer-speed-limit=100K
seed-time=0
max-upload-limit=50K

# === ADVANCED ===
file-allocation=none
disk-cache=16M
piece-length=1M
stream-piece-selector=inorder

# === LOGGING ===
log-level=notice
console-log-level=notice
EOF
```

**Now just use:**
```bash
aria2c URL  # Automatically uses config file
```

---

## Common Use Cases

### Use Case 1: Student Downloading Course Materials
```bash
# Create dedicated folder
mkdir -p /sdcard/Download/Courses

# Download course materials list
aria2c -i course-materials.txt \
  -j 3 \
  -x 8 \
  -d /sdcard/Download/Courses \
  --max-download-limit=1M  # Don't hog school wifi
```

### Use Case 2: Downloading Linux ISOs
```bash
# Ubuntu ISO with verification
aria2c -x 16 -s 16 \
  --checksum=sha-256=YOUR_HASH_HERE \
  -d /sdcard/Download \
  https://releases.ubuntu.com/22.04/ubuntu-22.04-desktop-amd64.iso
```

### Use Case 3: Archiving Websites
```bash
# Use with wget to get URLs, then download with aria2
wget --spider -r --no-parent https://example.com 2>&1 | \
  grep 'https://' | \
  awk '{print $3}' > site-files.txt

aria2c -i site-files.txt -j 5 -x 4 -d /sdcard/Archive
```

---

## Performance Tuning

### For Slow Connections (< 1 Mbps)
```bash
aria2c -x 4 -s 4 --max-connection-per-server=4 URL
```

### For Fast Connections (> 50 Mbps)
```bash
aria2c -x 16 -s 16 --min-split-size=1M --disk-cache=32M URL
```

### For Unreliable Connections
```bash
aria2c -x 8 -s 8 \
  --retry-wait=30 \
  --max-tries=10 \
  --timeout=60 \
  -c URL
```

---

## Integration with Other Tools

### With yt-dlp (YouTube)
```bash
# Install yt-dlp first
pkg install yt-dlp

# Download using aria2 as external downloader
yt-dlp --external-downloader aria2c \
  --external-downloader-args "-x 16 -s 16" \
  "YOUTUBE_URL"
```

### With Termux:API (Notifications)
```bash
# Install termux-api first
pkg install termux-api

# Download with notification on complete
aria2c -x 16 URL && \
  termux-notification \
    --title "Download Complete" \
    --content "$(basename URL) finished" \
    --sound
```

---

## Monitoring & Management

### Check Current Downloads
```bash
# If running with RPC
# Install curl first
pkg install curl

curl http://localhost:6800/jsonrpc \
  -d '{"jsonrpc":"2.0","id":"1","method":"aria2.tellActive"}'
```

### View Download Statistics
```bash
# Real-time monitoring
watch -n 1 'ps aux | grep aria2'

# Check logs
tail -f download.log
```

---

## Sources & Citations

This guide was compiled from:

1. **Official aria2 Documentation**
   - https://aria2.github.io/manual/en/html/
   - Command reference and options

2. **aria2 GitHub Repository**
   - https://github.com/aria2/aria2
   - Latest version information and features

3. **Termux Wiki**
   - https://wiki.termux.com/
   - Termux-specific usage and setup

4. **aria2 Man Pages**
   - Command line options and examples
   - Configuration file format

5. **Community Best Practices**
   - Various user guides and tutorials from 2024-2025
   - Performance optimization techniques

---

This guide covers everything you need to use aria2 effectively in Termux as of 2025. Save this markdown file and refer to it whenever you need to download files efficiently!