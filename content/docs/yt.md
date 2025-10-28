--- 
title: "yt-dlp"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:26:52Z
tags: ["Yt"]
draft: false
---

# 🎥 yt-dlp Complete Guide for Termux (2025)

**yt-dlp** is a feature-rich command-line tool to download videos from YouTube and 1000+ other sites. It's a fork of youtube-dl with additional features and fixes.

## 📋 Table of Contents
- [Basic Usage](#basic-usage)
- [Quality Selection](#quality-selection)
- [Audio Downloads](#audio-downloads)
- [Playlists & Channels](#playlists--channels)
- [Subtitles & Captions](#subtitles--captions)
- [Output Templates](#output-templates)
- [Speed & Performance](#speed--performance)
- [Advanced Features](#advanced-features)
- [Troubleshooting](#troubleshooting)

---

## 🚀 Basic Usage

### Simple Download
```bash
# Download a video (best quality)
yt-dlp "https://www.youtube.com/watch?v=VIDEO_ID"

# Download with custom filename
yt-dlp -o "my_video.mp4" "VIDEO_URL"

# Download to specific folder
yt-dlp -o "~/storage/downloads/%(title)s.%(ext)s" "VIDEO_URL"
```

### Check Available Formats
```bash
# List all available formats
yt-dlp -F "VIDEO_URL"

# Shows video quality, resolution, codec, file size
```

---

## 🎬 Quality Selection

### Video Quality

```bash
# Download best quality (video + audio)
yt-dlp -f "bv*+ba/b" "VIDEO_URL"

# Download specific resolution
yt-dlp -f "bv*[height<=720]+ba/b[height<=720]" "VIDEO_URL"

# Download 1080p (if available)
yt-dlp -f "bv*[height<=1080]+ba/b" "VIDEO_URL"

# Download 4K
yt-dlp -f "bv*[height<=2160]+ba/b" "VIDEO_URL"

# Download worst quality (save data)
yt-dlp -f "wv*+wa/w" "VIDEO_URL"

# Download specific format by ID (from -F list)
yt-dlp -f 137+140 "VIDEO_URL"
```

### Recommended Quality Commands

```bash
# Best quality under 500MB
yt-dlp -f "bv*[filesize<500M]+ba/b[filesize<500M]" "VIDEO_URL"

# Best quality MP4 format
yt-dlp -f "bv*[ext=mp4]+ba[ext=m4a]/b[ext=mp4]" "VIDEO_URL"

# 720p MP4 (most compatible)
yt-dlp -f "bv*[height=720][ext=mp4]+ba[ext=m4a]" "VIDEO_URL"
```

---

## 🎵 Audio Downloads

### Extract Audio Only

```bash
# Best audio quality
yt-dlp -x "VIDEO_URL"

# Convert to MP3
yt-dlp -x --audio-format mp3 "VIDEO_URL"

# MP3 with specific bitrate
yt-dlp -x --audio-format mp3 --audio-quality 320K "VIDEO_URL"

# Convert to M4A (AAC)
yt-dlp -x --audio-format m4a "VIDEO_URL"

# Convert to OPUS
yt-dlp -x --audio-format opus "VIDEO_URL"

# Best audio without conversion
yt-dlp -f "ba" "VIDEO_URL"
```

### Audio with Metadata

```bash
# Add metadata and thumbnail
yt-dlp -x --audio-format mp3 --embed-thumbnail --add-metadata "VIDEO_URL"

# Audio with thumbnail (for music players)
yt-dlp -x --audio-format mp3 --embed-thumbnail --embed-metadata "VIDEO_URL"
```

---

## 📚 Playlists & Channels

### Playlist Downloads

```bash
# Download entire playlist
yt-dlp "PLAYLIST_URL"

# Download playlist with numbering
yt-dlp -o "%(playlist_index)s-%(title)s.%(ext)s" "PLAYLIST_URL"

# Download specific playlist items
yt-dlp --playlist-items 1,5,10-15 "PLAYLIST_URL"

# Download first 10 videos
yt-dlp --playlist-items 1-10 "PLAYLIST_URL"

# Download playlist in reverse order
yt-dlp --playlist-reverse "PLAYLIST_URL"

# Skip unavailable videos
yt-dlp --ignore-errors "PLAYLIST_URL"
```

### Channel Downloads

```bash
# Download all videos from channel
yt-dlp "https://www.youtube.com/@CHANNEL_NAME/videos"

# Download only videos uploaded this year
yt-dlp --dateafter today-365days "CHANNEL_URL"

# Download videos from last month
yt-dlp --dateafter today-30days "CHANNEL_URL"

# Download channel's latest 20 videos
yt-dlp --playlist-end 20 "CHANNEL_URL"
```

### Archive Feature (Avoid Re-downloading)

```bash
# Create download archive
yt-dlp --download-archive downloaded.txt "PLAYLIST_URL"

# Skip already downloaded videos
yt-dlp --download-archive ~/yt-archive.txt "VIDEO_URL"
```

---

## 📝 Subtitles & Captions

### Subtitle Downloads

```bash
# Download with auto-generated subtitles
yt-dlp --write-auto-subs "VIDEO_URL"

# Download with manual subtitles (if available)
yt-dlp --write-subs "VIDEO_URL"

# Embed subtitles into video
yt-dlp --embed-subs "VIDEO_URL"

# Download specific subtitle language
yt-dlp --write-subs --sub-langs "en,es" "VIDEO_URL"

# List available subtitles
yt-dlp --list-subs "VIDEO_URL"

# Download all available subtitles
yt-dlp --write-subs --sub-langs "all" "VIDEO_URL"

# Convert subtitles to SRT format
yt-dlp --write-subs --sub-format srt --convert-subs srt "VIDEO_URL"
```

---

## 📁 Output Templates

### Custom Filenames

```bash
# Title only
yt-dlp -o "%(title)s.%(ext)s" "VIDEO_URL"

# Title with uploader
yt-dlp -o "%(uploader)s - %(title)s.%(ext)s" "VIDEO_URL"

# Title with date
yt-dlp -o "%(upload_date)s - %(title)s.%(ext)s" "VIDEO_URL"

# Organized by uploader folder
yt-dlp -o "~/storage/downloads/%(uploader)s/%(title)s.%(ext)s" "VIDEO_URL"

# Playlist organized
yt-dlp -o "~/storage/downloads/%(playlist)s/%(playlist_index)s-%(title)s.%(ext)s" "PLAYLIST_URL"

# Clean filename (remove special characters)
yt-dlp -o "%(title).200s.%(ext)s" --restrict-filenames "VIDEO_URL"
```

### Useful Template Variables

- `%(title)s` - Video title
- `%(uploader)s` - Channel name
- `%(upload_date)s` - Upload date (YYYYMMDD)
- `%(duration)s` - Video duration
- `%(resolution)s` - Video resolution
- `%(playlist)s` - Playlist name
- `%(playlist_index)s` - Video number in playlist
- `%(id)s` - Video ID
- `%(ext)s` - File extension

---

## ⚡ Speed & Performance

### Download Speed Optimization

```bash
# Limit download speed (to save bandwidth)
yt-dlp --limit-rate 1M "VIDEO_URL"

# Use multiple connections (faster downloads)
yt-dlp -N 4 "VIDEO_URL"

# Continue incomplete downloads
yt-dlp -c "VIDEO_URL"

# Show download progress
yt-dlp --progress "VIDEO_URL"

# Quiet mode (no output)
yt-dlp -q "VIDEO_URL"

# Verbose mode (detailed info)
yt-dlp -v "VIDEO_URL"
```

### Batch Downloads

```bash
# Download from a text file with URLs
yt-dlp -a urls.txt

# Format of urls.txt:
# https://youtube.com/watch?v=VIDEO1
# https://youtube.com/watch?v=VIDEO2
```

---

## 🔧 Advanced Features

### Cookies & Authentication

```bash
# Use cookies from browser (for age-restricted content)
yt-dlp --cookies-from-browser firefox "VIDEO_URL"
yt-dlp --cookies-from-browser chrome "VIDEO_URL"

# Use custom cookie file
yt-dlp --cookies cookies.txt "VIDEO_URL"
```

### Geo-Restriction Bypass

```bash
# Use a proxy
yt-dlp --proxy socks5://127.0.0.1:1080 "VIDEO_URL"

# Bypass geo-restriction
yt-dlp --geo-bypass "VIDEO_URL"

# Set custom geo-bypass country
yt-dlp --geo-bypass-country US "VIDEO_URL"
```

### Post-Processing

```bash
# Embed thumbnail in video file
yt-dlp --embed-thumbnail "VIDEO_URL"

# Add metadata
yt-dlp --add-metadata "VIDEO_URL"

# Merge video and audio (if downloaded separately)
yt-dlp --merge-output-format mp4 "VIDEO_URL"

# Re-encode video (fix compatibility issues)
yt-dlp --recode-video mp4 "VIDEO_URL"

# Remove sponsorblock segments
yt-dlp --sponsorblock-remove all "VIDEO_URL"
```

### Live Streams

```bash
# Download live stream (wait until finished)
yt-dlp --wait-for-video 30 "LIVE_STREAM_URL"

# Download ongoing live stream
yt-dlp "LIVE_STREAM_URL"

# Monitor and download when live
yt-dlp --live-from-start "CHANNEL_URL"
```

### Update yt-dlp

```bash
# Update to latest version
pip install -U yt-dlp

# Or using yt-dlp itself
yt-dlp -U
```

---

## 🎯 Practical Examples

### Example 1: Download Music Video as MP3
```bash
yt-dlp -x --audio-format mp3 --audio-quality 320K \
  --embed-thumbnail --add-metadata \
  -o "~/storage/music/%(artist)s - %(title)s.%(ext)s" \
  "https://www.youtube.com/watch?v=VIDEO_ID"
```

### Example 2: Download Course Playlist
```bash
yt-dlp -f "bv*[height<=720]+ba" \
  -o "~/storage/downloads/Course/%(playlist_index)s - %(title)s.%(ext)s" \
  --write-subs --embed-subs --sub-langs "en" \
  --download-archive course-archive.txt \
  "PLAYLIST_URL"
```

### Example 3: Download Channel's Latest Videos
```bash
yt-dlp -f "bv*[height<=1080]+ba" \
  --dateafter today-30days \
  -o "~/storage/downloads/%(uploader)s/%(upload_date)s - %(title)s.%(ext)s" \
  --download-archive channel-archive.txt \
  "https://www.youtube.com/@CHANNEL/videos"
```

### Example 4: Download 4K Video with Best Audio
```bash
yt-dlp -f "bv*[height=2160]+ba" \
  --merge-output-format mkv \
  --embed-thumbnail --add-metadata \
  -o "~/storage/movies/%(title)s.%(ext)s" \
  "VIDEO_URL"
```

### Example 5: Download Entire Playlist as MP3
```bash
yt-dlp -x --audio-format mp3 --audio-quality 192K \
  --embed-thumbnail --add-metadata \
  -o "~/storage/music/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" \
  --ignore-errors \
  "PLAYLIST_URL"
```

---

## 🛠️ Troubleshooting

### Common Issues & Solutions

#### 1. **Unable to extract video data**
```bash
# Update yt-dlp
pip install -U yt-dlp
```

#### 2. **403 Forbidden / 429 Too Many Requests**
```bash
# Wait a bit and retry with different user-agent
yt-dlp --user-agent "Mozilla/5.0" "VIDEO_URL"
```

#### 3. **No audio in downloaded video**
```bash
# Ensure audio is merged
yt-dlp -f "bv*+ba" --merge-output-format mp4 "VIDEO_URL"
```

#### 4. **Age-restricted content**
```bash
# Use cookies from your browser
yt-dlp --cookies-from-browser firefox "VIDEO_URL"
```

#### 5. **Slow downloads**
```bash
# Use multiple connections
yt-dlp -N 8 "VIDEO_URL"
```

#### 6. **Storage permission denied in Termux**
```bash
# Grant storage permission
termux-setup-storage
```

---

## 📱 Termux-Specific Tips

### 1. **Setup Storage Access**
```bash
termux-setup-storage
cd ~/storage/downloads
```

### 2. **Default Download Location**
```bash
# Create downloads folder
mkdir -p ~/storage/downloads

# Set as default
alias ytdl='yt-dlp -o "~/storage/downloads/%(title)s.%(ext)s"'
```

### 3. **Save Aliases (Add to ~/.bashrc)**
```bash
# Quick download shortcuts
echo 'alias ytdl="yt-dlp"' >> ~/.bashrc
echo 'alias ytmp3="yt-dlp -x --audio-format mp3"' >> ~/.bashrc
echo 'alias yt720="yt-dlp -f \"bv*[height<=720]+ba\""' >> ~/.bashrc
source ~/.bashrc
```

### 4. **Install FFmpeg (Required for merging/conversion)**
```bash
pkg install ffmpeg
```

---

## 📖 Useful Command Combinations

### Complete Download Command
```bash
yt-dlp \
  -f "bv*[height<=1080]+ba" \
  -o "~/storage/downloads/%(title)s.%(ext)s" \
  --embed-thumbnail \
  --add-metadata \
  --write-subs \
  --embed-subs \
  --sub-langs "en" \
  --ignore-errors \
  --continue \
  --no-overwrites \
  "VIDEO_URL"
```

### Configuration File (~/.config/yt-dlp/config)
```bash
# Create config directory
mkdir -p ~/.config/yt-dlp

# Create config file
cat > ~/.config/yt-dlp/config << 'EOF'
# Output location
-o ~/storage/downloads/%(title)s.%(ext)s

# Prefer 1080p or best
-f bv*[height<=1080]+ba/b

# Embed metadata
--embed-thumbnail
--add-metadata

# Continue downloads
--continue
--no-overwrites
--ignore-errors

# Subtitles
--write-subs
--sub-langs en
EOF
```

Now you can simply run:
```bash
yt-dlp "VIDEO_URL"
```

---

## 🔗 Official Resources

- **GitHub Repository**: https://github.com/yt-dlp/yt-dlp
- **Full Documentation**: https://github.com/yt-dlp/yt-dlp#readme
- **Supported Sites**: https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md
- **Wiki**: https://github.com/yt-dlp/yt-dlp/wiki

---

## 📌 Quick Reference Card

```bash
# Basic
yt-dlp URL                          # Download best quality
yt-dlp -F URL                       # List formats
yt-dlp -f ID URL                    # Download specific format

# Quality
yt-dlp -f "bv*+ba" URL              # Best video + audio
yt-dlp -f "bv*[height<=720]+ba" URL # 720p max

# Audio
yt-dlp -x URL                       # Extract audio
yt-dlp -x --audio-format mp3 URL    # Convert to MP3

# Playlist
yt-dlp --playlist-items 1-10 URL    # First 10 videos
yt-dlp --download-archive file.txt  # Skip downloaded

# Subtitles
yt-dlp --write-subs URL             # Download subs
yt-dlp --embed-subs URL             # Embed in video

# Output
yt-dlp -o "template" URL            # Custom filename
yt-dlp --restrict-filenames URL     # Safe filenames

# Speed
yt-dlp -N 4 URL                     # 4 connections
yt-dlp --limit-rate 1M URL          # Limit to 1MB/s

# Update
yt-dlp -U                           # Update yt-dlp
```

---

**Note**: This guide is updated as of October 2025. For the latest features, always check the official GitHub repository.

**Credits**: yt-dlp is maintained by the community and built upon youtube-dl.

---

## 🎓 How to Use This Guide

### Step-by-Step Tutorial for Beginners

#### **Step 1: Verify Installation**
```bash
# Check if yt-dlp is installed
yt-dlp --version

# If not installed
pip install yt-dlp
```

#### **Step 2: Test Basic Download**
```bash
# Download a YouTube video (replace with actual URL)
yt-dlp "https://www.youtube.com/watch?v=dQw4w9WgXcQ"

# This will download to your current directory
```

#### **Step 3: Choose Your Download Location**
```bash
# Setup Termux storage (first time only)
termux-setup-storage

# Change to downloads folder
cd ~/storage/downloads

# Now downloads will go here
yt-dlp "VIDEO_URL"
```

#### **Step 4: Select Quality**
```bash
# First, check available qualities
yt-dlp -F "VIDEO_URL"

# You'll see output like:
# ID  EXT   RESOLUTION  FPS  │ FILESIZE
# 18  mp4   640x360     30   │  50.00MiB
# 22  mp4   1280x720    30   │ 150.00MiB

# Download the one you want (e.g., 720p)
yt-dlp -f "bv*[height<=720]+ba" "VIDEO_URL"
```

#### **Step 5: Download Audio**
```bash
# Extract as MP3
yt-dlp -x --audio-format mp3 "VIDEO_URL"

# The file will be saved as .mp3 automatically
```

#### **Step 6: Download Playlists**
```bash
# Entire playlist
yt-dlp "PLAYLIST_URL"

# With organized filenames
yt-dlp -o "%(playlist_index)s-%(title)s.%(ext)s" "PLAYLIST_URL"
```

#### **Step 7: Create Your Config (Recommended)**
This makes your life easier - you won't need to type long commands every time!

```bash
# Create config directory
mkdir -p ~/.config/yt-dlp

# Create configuration file
nano ~/.config/yt-dlp/config
```

Add this to the file:
```
# Your default settings
-o ~/storage/downloads/%(title)s.%(ext)s
-f bv*[height<=1080]+ba/b
--embed-thumbnail
--add-metadata
--continue
--no-overwrites
```

Press `Ctrl + X`, then `Y`, then `Enter` to save.

Now simply run:
```bash
yt-dlp "VIDEO_URL"
```

All your preferences will be applied automatically!

---

## 💡 Pro Tips

1. **Always update yt-dlp** before reporting issues: `pip install -U yt-dlp`
2. **Use `-F` flag** to check formats before downloading
3. **Use download archive** (`--download-archive`) for playlists to avoid re-downloads
4. **Install ffmpeg** for best compatibility: `pkg install ffmpeg`
5. **Create aliases** for frequently used commands in `~/.bashrc`
6. **Use configuration file** to set default options
7. **Check supported sites**: Over 1800+ websites supported!

---

This comprehensive guide covers everything you need for daily use of yt-dlp in Termux. Bookmark this file and refer to it whenever needed!

---

# Sources & Citations

This guide was compiled from the following official sources:

1. **Official yt-dlp GitHub Repository**: https://github.com/yt-dlp/yt-dlp
2. **yt-dlp README Documentation**: https://github.com/yt-dlp/yt-dlp/blob/master/README.md
3. **Supported Sites List**: https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md
4. **yt-dlp Wiki**: https://github.com/yt-dlp/yt-dlp/wiki

All commands and features listed are current as of October 2025 and verified against the latest stable release of yt-dlp.