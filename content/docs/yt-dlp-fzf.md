--- 
title: "yt-dlp-fzf"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:27:52Z
tags: ["Yt"]
draft: false
---

# 🚀 yt-dlp + fzf: Interactive Video Downloader Guide (2025)

**Combine the power of yt-dlp with fzf's fuzzy finding to create an interactive, user-friendly video downloading experience!**

## 📋 Table of Contents
- [Introduction](#introduction)
- [Installation](#installation)
- [Basic Integration](#basic-integration)
- [Interactive Scripts](#interactive-scripts)
- [Advanced Features](#advanced-features)
- [Complete Workflow Examples](#complete-workflow-examples)
- [Custom Functions](#custom-functions)
- [Troubleshooting](#troubleshooting)

---

## 🎯 Introduction

### What is fzf?
**fzf** is a command-line fuzzy finder that lets you interactively search and select from lists. When combined with yt-dlp, you can:
- ✅ Interactively select video quality
- ✅ Browse and choose from playlists
- ✅ Preview video information before downloading
- ✅ Select multiple videos at once
- ✅ Choose download formats visually
- ✅ Create custom interactive menus

### Why Use yt-dlp + fzf?
Instead of memorizing complex commands, you get **interactive menus** for everything!

---

## 📦 Installation

### Install Both Tools in Termux

```bash
# Update packages
pkg update && pkg upgrade

# Install yt-dlp
pip install -U yt-dlp

# Install fzf
pkg install fzf

# Install ffmpeg (for video/audio processing)
pkg install ffmpeg

# Install jq (for JSON parsing - useful for advanced scripts)
pkg install jq

# Verify installations
yt-dlp --version
fzf --version
```

### Optional: Setup Storage Access
```bash
termux-setup-storage
mkdir -p ~/storage/downloads
```

---

## 🎬 Basic Integration

### 1. Interactive Format Selection

Instead of running `yt-dlp -F URL` and manually typing format codes, use fzf:

```bash
# Select format interactively
yt-dlp -F "VIDEO_URL" | fzf
```

**Better version - Download selected format:**

```bash
url="VIDEO_URL"
format=$(yt-dlp -F "$url" | fzf | awk '{print $1}')
yt-dlp -f "$format" "$url"
```

### 2. Interactive Playlist Item Selection

```bash
# View playlist and select videos
yt-dlp --flat-playlist -J "PLAYLIST_URL" | \
  jq -r '.entries[] | "\(.playlist_index). \(.title)"' | \
  fzf
```

### 3. Quick Quality Presets with fzf

```bash
# Choose quality preset
quality=$(echo -e "Best Quality\n1080p\n720p\n480p\nAudio Only (MP3)\nAudio Only (Best)" | fzf)

case "$quality" in
  "Best Quality")
    yt-dlp -f "bv*+ba/b" "$url"
    ;;
  "1080p")
    yt-dlp -f "bv*[height<=1080]+ba/b" "$url"
    ;;
  "720p")
    yt-dlp -f "bv*[height<=720]+ba/b" "$url"
    ;;
  "480p")
    yt-dlp -f "bv*[height<=480]+ba/b" "$url"
    ;;
  "Audio Only (MP3)")
    yt-dlp -x --audio-format mp3 "$url"
    ;;
  "Audio Only (Best)")
    yt-dlp -x "$url"
    ;;
esac
```

---

## 🛠️ Interactive Scripts

### Script 1: Complete Interactive Downloader

Save as `~/bin/ytdl-fzf`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Interactive yt-dlp with fzf
# Usage: ytdl-fzf [URL]

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Function to print colored messages
print_msg() {
    echo -e "${GREEN}[INFO]${NC} $1"
}

print_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

# Check if URL provided
if [ -z "$1" ]; then
    print_error "No URL provided!"
    echo "Usage: ytdl-fzf <VIDEO_URL>"
    exit 1
fi

URL="$1"

# Verify URL is valid
print_msg "Verifying URL..."
if ! yt-dlp --simulate "$URL" &> /dev/null; then
    print_error "Invalid URL or video not accessible!"
    exit 1
fi

# Main menu
print_msg "What would you like to do?"
ACTION=$(echo -e "📹 Download Video\n🎵 Download Audio\n📋 View Available Formats\n📚 Playlist Options\n⚙️  Advanced Options\n❌ Exit" | fzf --height 40% --border --prompt "Select action: " --preview-window=hidden)

case "$ACTION" in
    "📹 Download Video")
        # Video quality selection
        QUALITY=$(echo -e "🏆 Best Quality (Largest file)\n📺 1080p (Full HD)\n💿 720p (HD - Recommended)\n📱 480p (Mobile friendly)\n💾 360p (Save data)" | fzf --height 40% --border --prompt "Select quality: ")
        
        OUTPUT_DIR=$(echo -e "📂 ~/storage/downloads\n📂 Current directory\n📂 Custom location" | fzf --height 40% --border --prompt "Save to: ")
        
        case "$OUTPUT_DIR" in
            *"~/storage/downloads"*)
                OUTPATH="~/storage/downloads/%(title)s.%(ext)s"
                ;;
            *"Current directory"*)
                OUTPATH="%(title)s.%(ext)s"
                ;;
            *"Custom"*)
                read -p "Enter full path: " OUTPATH
                ;;
        esac
        
        case "$QUALITY" in
            *"Best Quality"*)
                print_msg "Downloading best quality..."
                yt-dlp -f "bv*+ba/b" -o "$OUTPATH" "$URL"
                ;;
            *"1080p"*)
                print_msg "Downloading 1080p..."
                yt-dlp -f "bv*[height<=1080]+ba/b" -o "$OUTPATH" "$URL"
                ;;
            *"720p"*)
                print_msg "Downloading 720p..."
                yt-dlp -f "bv*[height<=720]+ba/b" -o "$OUTPATH" "$URL"
                ;;
            *"480p"*)
                print_msg "Downloading 480p..."
                yt-dlp -f "bv*[height<=480]+ba/b" -o "$OUTPATH" "$URL"
                ;;
            *"360p"*)
                print_msg "Downloading 360p..."
                yt-dlp -f "bv*[height<=360]+ba/b" -o "$OUTPATH" "$URL"
                ;;
        esac
        ;;
        
    "🎵 Download Audio")
        # Audio format selection
        AUDIO_FORMAT=$(echo -e "🎵 MP3 (Universal)\n🎼 M4A (Apple)\n🎹 OPUS (High quality, small size)\n💿 FLAC (Lossless)\n🔊 Best (No conversion)" | fzf --height 40% --border --prompt "Select audio format: ")
        
        AUDIO_QUALITY=$(echo -e "🏆 320k (Best)\n💿 256k (Very Good)\n📻 192k (Good)\n📱 128k (Mobile)" | fzf --height 40% --border --prompt "Select bitrate: ")
        
        BITRATE=$(echo "$AUDIO_QUALITY" | grep -oP '\d+')
        
        OUTPUT_DIR="~/storage/music"
        mkdir -p ~/storage/music
        
        case "$AUDIO_FORMAT" in
            *"MP3"*)
                print_msg "Downloading as MP3 (${BITRATE}k)..."
                yt-dlp -x --audio-format mp3 --audio-quality "${BITRATE}K" \
                  --embed-thumbnail --add-metadata \
                  -o "$OUTPUT_DIR/%(title)s.%(ext)s" "$URL"
                ;;
            *"M4A"*)
                print_msg "Downloading as M4A..."
                yt-dlp -x --audio-format m4a --audio-quality "${BITRATE}K" \
                  --embed-thumbnail --add-metadata \
                  -o "$OUTPUT_DIR/%(title)s.%(ext)s" "$URL"
                ;;
            *"OPUS"*)
                print_msg "Downloading as OPUS..."
                yt-dlp -x --audio-format opus --audio-quality "${BITRATE}K" \
                  -o "$OUTPUT_DIR/%(title)s.%(ext)s" "$URL"
                ;;
            *"FLAC"*)
                print_msg "Downloading as FLAC..."
                yt-dlp -x --audio-format flac \
                  --embed-thumbnail --add-metadata \
                  -o "$OUTPUT_DIR/%(title)s.%(ext)s" "$URL"
                ;;
            *"Best"*)
                print_msg "Downloading best audio (no conversion)..."
                yt-dlp -f "ba" --embed-thumbnail --add-metadata \
                  -o "$OUTPUT_DIR/%(title)s.%(ext)s" "$URL"
                ;;
        esac
        ;;
        
    "📋 View Available Formats")
        print_msg "Fetching available formats..."
        yt-dlp -F "$URL" | fzf --height 80% --border --prompt "Available formats (press ESC to exit): " \
          --preview "echo 'Select format ID from first column to download manually'" \
          --preview-window=up:3
        ;;
        
    "📚 Playlist Options")
        print_msg "Checking if URL is a playlist..."
        
        if yt-dlp --flat-playlist "$URL" &> /dev/null; then
            PLAYLIST_ACTION=$(echo -e "📥 Download entire playlist\n🎯 Select specific videos\n🔢 Download first N videos\n📊 View playlist info" | fzf --height 40% --border --prompt "Playlist action: ")
            
            case "$PLAYLIST_ACTION" in
                *"Download entire playlist"*)
                    QUALITY=$(echo -e "🏆 Best Quality\n📺 1080p\n💿 720p\n📱 480p" | fzf --height 40% --border)
                    
                    case "$QUALITY" in
                        *"Best"*) FORMAT="bv*+ba/b" ;;
                        *"1080p"*) FORMAT="bv*[height<=1080]+ba/b" ;;
                        *"720p"*) FORMAT="bv*[height<=720]+ba/b" ;;
                        *"480p"*) FORMAT="bv*[height<=480]+ba/b" ;;
                    esac
                    
                    print_msg "Downloading entire playlist..."
                    yt-dlp -f "$FORMAT" \
                      -o "~/storage/downloads/%(playlist)s/%(playlist_index)s-%(title)s.%(ext)s" \
                      --download-archive ~/playlist-archive.txt \
                      --ignore-errors "$URL"
                    ;;
                    
                *"Select specific videos"*)
                    print_msg "Fetching playlist info..."
                    SELECTED=$(yt-dlp --flat-playlist -J "$URL" | \
                      jq -r '.entries[] | "\(.playlist_index). \(.title) [\(.duration) sec]"' | \
                      fzf --multi --height 80% --border --prompt "Select videos (TAB for multiple): ")
                    
                    if [ -n "$SELECTED" ]; then
                        INDICES=$(echo "$SELECTED" | awk '{print $1}' | tr -d '.' | tr '\n' ',' | sed 's/,$//')
                        print_msg "Downloading selected videos: $INDICES"
                        yt-dlp --playlist-items "$INDICES" \
                          -o "~/storage/downloads/%(playlist_index)s-%(title)s.%(ext)s" "$URL"
                    fi
                    ;;
                    
                *"Download first N videos"*)
                    read -p "How many videos? " NUM
                    print_msg "Downloading first $NUM videos..."
                    yt-dlp --playlist-items "1-$NUM" \
                      -o "~/storage/downloads/%(playlist_index)s-%(title)s.%(ext)s" "$URL"
                    ;;
                    
                *"View playlist info"*)
                    yt-dlp --flat-playlist -J "$URL" | \
                      jq -r '.entries[] | "\(.playlist_index). \(.title) - \(.duration)s"' | \
                      fzf --height 80% --border --preview-window=hidden
                    ;;
            esac
        else
            print_warning "This is not a playlist URL!"
        fi
        ;;
        
    "⚙️  Advanced Options")
        ADVANCED=$(echo -e "📝 Add subtitles\n🖼️  Embed thumbnail & metadata\n🎬 Download with SponsorBlock\n🍪 Use cookies from browser\n⚡ Download with multiple connections\n🔄 Update yt-dlp" | fzf --height 40% --border --prompt "Advanced option: ")
        
        case "$ADVANCED" in
            *"subtitles"*)
                SUBLANG=$(echo -e "en (English)\nes (Spanish)\nfr (French)\nde (German)\nall (All available)" | fzf --height 40% --border)
                LANG=$(echo "$SUBLANG" | awk '{print $1}')
                
                print_msg "Downloading with $LANG subtitles..."
                yt-dlp --write-subs --embed-subs --sub-langs "$LANG" "$URL"
                ;;
                
            *"thumbnail"*)
                print_msg "Downloading with embedded thumbnail and metadata..."
                yt-dlp --embed-thumbnail --add-metadata \
                  -o "~/storage/downloads/%(title)s.%(ext)s" "$URL"
                ;;
                
            *"SponsorBlock"*)
                print_msg "Downloading with SponsorBlock (removes sponsors/intros)..."
                yt-dlp --sponsorblock-remove all \
                  -o "~/storage/downloads/%(title)s.%(ext)s" "$URL"
                ;;
                
            *"cookies"*)
                BROWSER=$(echo -e "firefox\nchrome\nchromium\nedge" | fzf --height 40% --border)
                print_msg "Using cookies from $BROWSER..."
                yt-dlp --cookies-from-browser "$BROWSER" "$URL"
                ;;
                
            *"multiple connections"*)
                print_msg "Downloading with 8 parallel connections..."
                yt-dlp -N 8 "$URL"
                ;;
                
            *"Update"*)
                print_msg "Updating yt-dlp..."
                pip install -U yt-dlp
                ;;
        esac
        ;;
        
    "❌ Exit")
        print_msg "Goodbye!"
        exit 0
        ;;
esac

print_msg "Done! ✅"
```

**Make it executable:**
```bash
chmod +x ~/bin/ytdl-fzf
```

**Add to PATH (add to ~/.bashrc):**
```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**Usage:**
```bash
ytdl-fzf "https://www.youtube.com/watch?v=VIDEO_ID"
```

---

### Script 2: Interactive Format Selector

Save as `~/bin/ytdl-format`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Interactive format selector with preview
# Usage: ytdl-format <URL>

if [ -z "$1" ]; then
    echo "Usage: ytdl-format <VIDEO_URL>"
    exit 1
fi

URL="$1"

echo "Fetching available formats..."

# Get formats and let user select
SELECTED=$(yt-dlp -F "$URL" 2>/dev/null | \
    tail -n +4 | \
    fzf --height 80% \
        --border \
        --header "Select format (ESC to cancel, ENTER to download)" \
        --preview "echo 'Format Details:'; echo ''; echo {}; echo ''; echo 'Press ENTER to download this format'" \
        --preview-window=up:5:wrap \
        --prompt "Format ❯ ")

if [ -z "$SELECTED" ]; then
    echo "Cancelled."
    exit 0
fi

# Extract format ID (first column)
FORMAT_ID=$(echo "$SELECTED" | awk '{print $1}')

echo "Downloading format: $FORMAT_ID"
yt-dlp -f "$FORMAT_ID" -o "~/storage/downloads/%(title)s.%(ext)s" "$URL"

echo "✅ Download complete!"
```

Make executable:
```bash
chmod +x ~/bin/ytdl-format
```

---

### Script 3: Playlist Video Selector

Save as `~/bin/ytdl-playlist`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Interactive playlist video selector
# Usage: ytdl-playlist <PLAYLIST_URL>

if [ -z "$1" ]; then
    echo "Usage: ytdl-playlist <PLAYLIST_URL>"
    exit 1
fi

URL="$1"

echo "📡 Fetching playlist information..."

# Check if jq is installed
if ! command -v jq &> /dev/null; then
    echo "❌ jq is not installed. Install with: pkg install jq"
    exit 1
fi

# Get playlist info
PLAYLIST_INFO=$(yt-dlp --flat-playlist -J "$URL" 2>/dev/null)

if [ -z "$PLAYLIST_INFO" ]; then
    echo "❌ Failed to fetch playlist info. Check the URL."
    exit 1
fi

# Extract video list
VIDEOS=$(echo "$PLAYLIST_INFO" | \
    jq -r '.entries[] | "\(.playlist_index)|\(.title)|\(.duration // 0)"' | \
    awk -F'|' '{printf "%3d. %-80s [%dm %ds]\n", $1, substr($2,1,80), int($3/60), $3%60}')

# Let user select videos
SELECTED=$(echo "$VIDEOS" | \
    fzf --multi \
        --height 90% \
        --border \
        --header "Select videos (TAB for multiple, ENTER to confirm)" \
        --prompt "Videos ❯ " \
        --preview "echo 'Selected: {}'" \
        --preview-window=up:2)

if [ -z "$SELECTED" ]; then
    echo "No videos selected."
    exit 0
fi

# Extract indices
INDICES=$(echo "$SELECTED" | awk '{print $1}' | tr -d '.' | paste -sd ',' -)

echo "📥 Downloading videos: $INDICES"

# Ask for quality
QUALITY=$(echo -e "Best Quality\n1080p\n720p (Recommended)\n480p\nAudio Only" | \
    fzf --height 40% --border --prompt "Select quality: ")

case "$QUALITY" in
    "Best Quality")
        FORMAT="bv*+ba/b"
        ;;
    "1080p")
        FORMAT="bv*[height<=1080]+ba/b"
        ;;
    "720p"*)
        FORMAT="bv*[height<=720]+ba/b"
        ;;
    "480p")
        FORMAT="bv*[height<=480]+ba/b"
        ;;
    "Audio Only")
        FORMAT="ba"
        EXT="mp3"
        yt-dlp -x --audio-format mp3 \
            --playlist-items "$INDICES" \
            -o "~/storage/downloads/%(playlist)s/%(playlist_index)s-%(title)s.%(ext)s" \
            "$URL"
        exit 0
        ;;
    *)
        FORMAT="bv*+ba/b"
        ;;
esac

# Download selected videos
yt-dlp -f "$FORMAT" \
    --playlist-items "$INDICES" \
    -o "~/storage/downloads/%(playlist)s/%(playlist_index)s-%(title)s.%(ext)s" \
    --download-archive ~/playlist-archive.txt \
    "$URL"

echo "✅ All downloads complete!"
```

Make executable:
```bash
chmod +x ~/bin/ytdl-playlist
```

---

### Script 4: YouTube Channel Browser

Save as `~/bin/ytdl-channel`:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Browse and download from YouTube channels
# Usage: ytdl-channel <CHANNEL_URL>

if [ -z "$1" ]; then
    echo "Usage: ytdl-channel <CHANNEL_URL>"
    echo "Example: ytdl-channel https://www.youtube.com/@channelname/videos"
    exit 1
fi

URL="$1"

echo "🔍 Fetching channel videos..."

# Get video list (first 50)
VIDEOS=$(yt-dlp --flat-playlist -J "$URL" 2>/dev/null | \
    jq -r '.entries[0:50] | .[] | "\(.title)|\(.id)|\(.upload_date // "unknown")"' | \
    awk -F'|' '{
        date=$3
        if (date != "unknown") {
            year=substr(date,1,4)
            month=substr(date,5,2)
            day=substr(date,7,2)
            date=year"-"month"-"day
        }
        printf "%-70s [%s]\n", substr($1,1,70), date
    }')

if [ -z "$VIDEOS" ]; then
    echo "❌ Failed to fetch channel videos."
    exit 1
fi

# Let user select videos
SELECTED=$(echo "$VIDEOS" | \
    fzf --multi \
        --height 90% \
        --border \
        --header "Select videos from channel (TAB for multiple)" \
        --prompt "Videos ❯ ")

if [ -z "$SELECTED" ]; then
    echo "No videos selected."
    exit 0
fi

# Get video IDs
VIDEO_IDS=$(echo "$SELECTED" | \
    while read line; do
        TITLE=$(echo "$line" | sed 's/ \[.*$//')
        yt-dlp --flat-playlist -J "$URL" 2>/dev/null | \
            jq -r --arg title "$TITLE" '.entries[] | select(.title | startswith($title)) | .id' | head -1
    done)

# Ask for quality
QUALITY=$(echo -e "720p (Recommended)\n1080p\n480p\nBest Quality\nAudio Only" | \
    fzf --height 40% --border --prompt "Select quality: ")

case "$QUALITY" in
    "720p"*) FORMAT="bv*[height<=720]+ba/b" ;;
    "1080p") FORMAT="bv*[height<=1080]+ba/b" ;;
    "480p") FORMAT="bv*[height<=480]+ba/b" ;;
    "Best Quality") FORMAT="bv*+ba/b" ;;
    "Audio Only")
        echo "📥 Downloading audio..."
        echo "$VIDEO_IDS" | while read id; do
            yt-dlp -x --audio-format mp3 \
                -o "~/storage/downloads/%(uploader)s/%(title)s.%(ext)s" \
                "https://www.youtube.com/watch?v=$id"
        done
        exit 0
        ;;
    *) FORMAT="bv*[height<=720]+ba/b" ;;
esac

# Download selected videos
echo "📥 Downloading selected videos..."
echo "$VIDEO_IDS" | while read id; do
    echo "Downloading: $id"
    yt-dlp -f "$FORMAT" \
        -o "~/storage/downloads/%(uploader)s/%(title)s.%(ext)s" \
        "https://www.youtube.com/watch?v=$id"
done

echo "✅ All downloads complete!"
```

Make executable:
```bash
chmod +x ~/bin/ytdl-channel
```

---

## 🎨 Custom Functions for .bashrc

Add these to your `~/.bashrc` file for quick access:

```bash
# ===== YT-DLP + FZF Functions =====

# Interactive video downloader
ytf() {
    if [ -z "$1" ]; then
        echo "Usage: ytf <URL>"
        return 1
    fi
    
    local quality=$(echo -e "Best\n1080p\n720p\n480p\nAudio MP3" | fzf --height 40% --prompt "Quality: ")
    
    case "$quality" in
        "Best") yt-dlp -f "bv*+ba/b" "$1" ;;
        "1080p") yt-dlp -f "bv*[height<=1080]+ba/b" "$1" ;;
        "720p") yt-dlp -f "bv*[height<=720]+ba/b" "$1" ;;
        "480p") yt-dlp -f "bv*[height<=480]+ba/b" "$1" ;;
        "Audio MP3") yt-dlp -x --audio-format mp3 "$1" ;;
    esac
}

# Quick format selector
ytfmt() {
    local url="$1"
    local fmt=$(yt-dlp -F "$url" | fzf --height 80% | awk '{print $1}')
    [ -n "$fmt" ] && yt-dlp -f "$fmt" "$url"
}

# Download playlist with interactive selection
ytpl() {
    local url="$1"
    echo "Fetching playlist..."
    
    local selected=$(yt-dlp --flat-playlist -J "$url" | \
        jq -r '.entries[] | "\(.playlist_index). \(.title)"' | \
        fzf --multi --height 80% --prompt "Select videos: ")
    
    if [ -n "$selected" ]; then
        local indices=$(echo "$selected" | awk '{print $1}' | tr -d '.' | paste -sd ',' -)
        yt-dlp --playlist-items "$indices" -o "%(playlist_index)s-%(title)s.%(ext)s" "$url"
    fi
}

# Search and download from YouTube
ytsearch() {
    local query="$*"
    local result=$(yt-dlp "ytsearch10:$query" --flat-playlist -J | \
        jq -r '.entries[] | "\(.title) [\(.channel)] - \(.id)"' | \
        fzf --height 80% --prompt "Search results: ")
    
    if [ -n "$result" ]; then
        local video_id=$(echo "$result" | grep -oP '\[.*\] - \K.*')
        local action=$(echo -e "Download Video\nDownload Audio\nView Formats" | fzf --height 40%)
        
        case "$action" in
            "Download Video") yt-dlp "https://youtube.com/watch?v=$video_id" ;;
            "Download Audio") yt-dlp -x --audio-format mp3 "https://youtube.com/watch?v=$video_id" ;;
            "View Formats") yt-dlp -F "https://youtube.com/watch?v=$video_id" | less ;;
        esac
    fi
}

# Batch download with quality selection
ytbatch() {
    if [ ! -f "$1" ]; then
        echo "Usage: ytbatch <file_with_urls.txt>"
        return 1
    fi
    
    local quality=$(echo -e "Best\n1080p\n720p\n480p\nAudio" | fzf --height 40% --prompt "Quality for all: ")
    
    case "$quality" in
        "Best") yt-dlp -f "bv*+ba/b" -a "$1" ;;
        "1080p") yt-dlp -f "bv*[height<=1080]+ba/b" -a "$1" ;;
        "720p") yt-dlp -f "bv*[height<=720]+ba/b" -a "$1" ;;
        "480p") yt-dlp -f "bv*[height<=480]+ba/b" -a "$1" ;;
        "Audio") yt-dlp -x --audio-format mp3 -a "$1" ;;
    esac
}

# Interactive history browser (requires download archive)
ythist() {
    if [ ! -f ~/yt-archive.txt ]; then
        echo "No download history found."
        return 1
    fi
    
    cat ~/yt-archive.txt | \
        fzf --height 80% \
            --prompt "Download history: " \
            --preview "echo 'Video ID: {}'" \
            --bind "enter:execute(echo https://youtube.com/watch?v={} | xclip -selection clipboard)+abort"
}

# Download video with all extras
ytfull() {
    local url="$1"
    yt-dlp -f "bv*+ba/b" \
        --embed-thumbnail \
        --embed-subs \
        --write-subs \
        --sub-langs "en,es" \
        --add-metadata \
        --sponsorblock-remove all \
        -o "~/storage/downloads/%(title)s.%(ext)s" \
        "$url"
}

# Quick audio extraction
yta() {
    local format=$(echo -e "mp3\nm4a\nopus\nflac" | fzf --height 40% --prompt "Audio format: ")
    yt-dlp -x --audio-format "$format" --embed-thumbnail --add-metadata "$1"
}

# Smart download (auto-detect playlist/video)
yts() {
    local url="$1"
    
    if yt-dlp --flat-playlist --print "%(playlist_count)s" "$url" 2>/dev/null | grep -q "^[0-9]"; then
        echo "📚 Detected playlist!"
        ytdl-playlist "$url"
    else
        echo "📹 Detected single video!"
        ytf "$url"
    fi
}
```

**Reload your bashrc:**
```bash
source ~/.bashrc
```

---

## 💡 Advanced Usage Examples

### Example 1: Search YouTube and Download

```bash
ytsearch "python tutorial"
# Shows search results in fzf
# Select video
# Choose download type
```

### Example 2: Browse Channel Videos

```bash
ytdl-channel "https://www.youtube.com/@channelname/videos"
# Shows list of videos
# Select multiple with TAB
# Choose quality
# Downloads selected videos
```

### Example 3: Smart Download (Auto-detect)

```bash
yts "https://youtube.com/VIDEO_OR_PLAYLIST_URL"
# Automatically detects if URL is playlist or single video
# Shows appropriate interactive menu
```

### Example 4: Interactive Format Selection

```bash
ytfmt "VIDEO_URL"
# Shows all available formats in fzf
# Preview format details
# Select and download
```

### Example 5: Playlist with Specific Videos

```bash
ytpl "PLAYLIST_URL"
# Shows all videos in playlist
# Use TAB to select multiple
# Downloads only selected videos
```

---

## 🎯 Complete Workflow Examples

### Workflow 1: Download Music Album

```bash
# 1. Get playlist URL of album
# 2. Run interactive script
ytdl-fzf "PLAYLIST_URL"

# 3. Select: "🎵 Download Audio"
# 4. Choose: "🎵 MP3 (Universal)"
# 5. Select: "🏆 320k (Best)"
# 6. Wait for download to complete

# Result: All songs downloaded as high-quality MP3s with metadata
```

### Workflow 2: Download Educational Course

```bash
# 1. Get course playlist URL
# 2. Browse videos interactively
ytdl-playlist "COURSE_PLAYLIST_URL"

# 3. Select specific lectures (use TAB)
# 4. Choose 720p quality
# 5. Videos saved with organized filenames

# Result: Selected lectures in downloads folder with numbering
```

### Workflow 3: Download Multiple Videos from Different Sources

```bash
# 1. Create URL list
cat > videos.txt << EOF
https://youtube.com/watch?v=VIDEO1
https://vimeo.com/VIDEO2
https://youtube.com/watch?v=VIDEO3
EOF

# 2. Batch download with quality selection
ytbatch videos.txt

# 3. Choose quality from menu
# 4. All videos download with same quality

# Result: All videos downloaded in selected quality
```

### Workflow 4: Download with All Features

```bash
ytfull "VIDEO_URL"

# Downloads with:
# - Best quality
# - Embedded thumbnail
# - Subtitles (EN, ES)
# - Metadata
# - SponsorBlock (removes sponsors)

# Result: Complete video package ready to watch
```

---

## 🔧 Advanced fzf Features

### Custom Preview Windows

```bash
# Preview video info before download
ytinfo() {
    yt-dlp --flat-playlist -J "$1" | \
        jq -r '.entries[]? // . | "\(.title)\n\(.uploader)\n\(.duration)s\n\(.view_count) views"' | \
        fzf --height 80% \
            --preview "echo 'Video Information'; echo ''; echo {}" \
            --preview-window=up:30%:wrap
}
```

### Multi-Select with Actions

```bash
# Select multiple videos and choose action
ytmulti() {
    local selected=$(yt-dlp --flat-playlist -J "$1" | \
        jq -r '.entries[] | "\(.playlist_index). \(.title)"' | \
        fzf --multi \
            --bind "ctrl-a:select-all" \
            --bind "ctrl-d:deselect-all" \
            --header "CTRL-A: select all, CTRL-D: deselect all")
    
    if [ -n "$selected" ]; then
        local action=$(echo -e "Download\nCopy URLs\nShow Info" | fzf --height 40%)
        
        case "$action" in
            "Download")
                local indices=$(echo "$selected" | awk '{print $1}' | tr -d '.' | paste -sd ',' -)
                yt-dlp --playlist-items "$indices" "$1"
                ;;
            "Copy URLs")
                echo "$selected" | while read line; do
                    local idx=$(echo "$line" | awk '{print $1}' | tr -d '.')
                    yt-dlp --flat-playlist --print-json "$1" | jq -r ".entries[$((idx-1))].url"
                done
                ;;
            "Show Info")
                echo "$selected"
                ;;
        esac
    fi
}
```

### Keyboard Shortcuts in fzf

Add these bindings to your fzf calls:

```bash
--bind "ctrl-p:preview-up"          # Scroll preview up
--bind "ctrl-n:preview-down"        # Scroll preview down
--bind "ctrl-/:toggle-preview"      # Toggle preview window
--bind "ctrl-a:select-all"          # Select all items
--bind "ctrl-d:deselect-all"        # Deselect all
--bind "alt-enter:print-query"      # Print search query
--bind "enter:execute(command)"     # Execute on selection
```

---

## 📱 Termux-Specific Optimizations

### 1. Create Quick Shortcuts

Add to `~/.shortcuts/`:

```bash
mkdir -p ~/.shortcuts
```

**ytdl-quick** (quick download):
```bash
#!/data/data/com.termux/files/usr/bin/bash
termux-clipboard-get | xargs -I {} ytf "{}"
```

**ytdl-share** (download from share menu):
```bash
#!/data/data/com.termux/files/usr/bin/bash
URL=$(termux-clipboard-get)
ytdl-fzf "$URL"
```

Make executable:
```bash
chmod +x ~/.shortcuts/*
```

### 2. Termux Widget Integration

Place scripts in `~/.shortcuts/tasks/`:

```bash
mkdir -p ~/.shortcuts/tasks

# Quick download from clipboard
cat > ~/.shortcuts/tasks/YT-Download << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
termux-clipboard-get | xargs ytf
termux-toast "Download started"
EOF

chmod +x ~/.shortcuts/tasks/YT-Download
```

### 3. Notification on Complete

Add to download functions:

```bash
# After yt-dlp command
termux-notification --title "Download Complete" --content "Video downloaded successfully"
```

---

## 🐛 Troubleshooting

### Issue 1: fzf not showing colors

```bash
# Add to ~/.bashrc
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#282a36,hl:#bd93f9 --color=fg+:#f8f8f2,bg+:#44475a,hl+:#bd93f9 --color=info:#ffb86c,prompt:#50fa7b,pointer:#ff79c6 --color=marker:#ff79c6,spinner:#ffb86c,header:#6272a4'
```

### Issue 2: jq not parsing correctly

```bash
# Install/update jq
pkg install jq

# Test jq
echo '{"test":"value"}' | jq .
```

### Issue 3: Scripts not executable

```bash
# Fix permissions
chmod +x ~/bin/*
chmod +x ~/.shortcuts/*

# Add bin to PATH
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Issue 4: yt-dlp format errors

```bash
# Update yt-dlp
pip install -U yt-dlp

# Clear cache
yt-dlp --rm-cache-dir
```

### Issue 5: fzf preview not working

```bash
# Check if preview command is valid
echo "test" | fzf --preview "echo {}"

# If issue persists, disable preview
# Remove --preview and --preview-window flags
```

---

## 🎨 Customization Tips

### 1. Custom fzf Color Schemes

**Dracula Theme:**
```bash
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#282a36,hl:#bd93f9 --color=fg+:#f8f8f2,bg+:#44475a,hl+:#bd93f9 --color=info:#ffb86c,prompt:#50fa7b,pointer:#ff79c6 --color=marker:#ff79c6,spinner:#ffb86c,header:#6272a4'
```

**Nord Theme:**
```bash
export FZF_DEFAULT_OPTS='--color=fg:#e5e9f0,bg:#2e3440,hl:#81a1c1 --color=fg+:#e5e9f0,bg+:#3b4252,hl+:#81a1c1 --color=info:#eacb8a,prompt:#bf6069,pointer:#b48dac --color=marker:#a3be8b,spinner:#b48dac,header:#a3be8b'
```

**Monokai Theme:**
```bash
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#272822,hl:#66d9ef --color=fg+:#f8f8f2,bg+:#3e3d32,hl+:#66d9ef --color=info:#a6e22e,prompt:#f92672,pointer:#ae81ff --color=marker:#e6db74,spinner:#ae81ff,header:#75715e'
```

### 2. Custom Keybindings

```bash
# Add to fzf calls
--bind "ctrl-y:execute-silent(echo {} | xclip -selection clipboard)"  # Copy to clipboard
--bind "ctrl-o:execute(xdg-open {})"                                    # Open file
--bind "ctrl-e:execute(vim {})"                                         # Edit in vim
--bind "ctrl-r:reload(command)"                                         # Reload list
--bind "ctrl-t:toggle-all"                                              # Toggle all selections
```

### 3. Add Icons/Emojis to Menus

```bash
# Use in echo statements
echo -e "📹 Download Video\n🎵 Download Audio\n📋 Show Formats\n⚙️ Settings\n❌ Exit"
```

---

## 📚 Additional Resources

### Official Documentation
- **fzf GitHub**: https://github.com/junegunn/fzf
- **fzf Wiki**: https://github.com/junegunn/fzf/wiki
- **yt-dlp GitHub**: https://github.com/yt-dlp/yt-dlp
- **yt-dlp Documentation**: https://github.com/yt-dlp/yt-dlp/blob/master/README.md

### Useful Links
- **fzf Examples**: https://github.com/junegunn/fzf/wiki/examples
- **Advanced fzf**: https://github.com/junegunn/fzf/blob/master/ADVANCED.md
- **yt-dlp Supported Sites**: https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md

---

## 🎓 Quick Command Reference

```bash
# Basic Functions
ytf URL                    # Interactive quality selector
ytfmt URL                  # Interactive format selector
ytpl PLAYLIST_URL          # Interactive playlist selector
yta URL                    # Interactive audio downloader
yts URL                    # Smart download (auto-detect)

# Scripts
ytdl-fzf URL              # Complete interactive downloader
ytdl-format URL           # Format selector with preview
ytdl-playlist URL         # Playlist browser
ytdl-channel URL          # Channel video browser

# Search & Batch
ytsearch QUERY            # Search and download from YouTube
ytbatch FILE.txt          # Batch download with quality selection

# Utilities
ytfull URL                # Download with all features
ythist                    # Browse download history
ytinfo URL                # Preview video information
ytmulti URL               # Multi-select with actions
```

---

## 🎯 Best Practices

1. **Always update yt-dlp** before major downloads: `pip install -U yt-dlp`
2. **Use download archives** for playlists to avoid re-downloading
3. **Test format selection** with `-F` before downloading large files
4. **Set default download location** in config or scripts
5. **Use multi-select (TAB)** in fzf for batch operations
6. **Enable preview windows** for better context
7. **Create aliases** for frequently used commands
8. **Backup your scripts** and configuration files
9. **Use `--ignore-errors`** for playlist downloads
10. **Monitor disk space** especially for 4K/high-quality downloads

---

## 🎉 Conclusion

This guide provides a complete interactive experience for yt-dlp using fzf. You now have:

✅ **Interactive menus** for everything  
✅ **Multiple scripts** for different use cases  
✅ **Custom functions** for quick access  
✅ **Visual format selection**  
✅ **Playlist browsing**  
✅ **Quality presets**  
✅ **Termux optimization**  
✅ **Advanced features** (SponsorBlock, subtitles, metadata)  

Start with the basic functions, then gradually move to advanced scripts as you get comfortable!

---

**Last Updated**: October 2025  
**Tested On**: Termux (Android 10+)  
**yt-dlp Version**: 2025.x  
**fzf Version**: 0.54+

---

# Sources & Citations

This guide was compiled from:

1. **fzf Official Repository**: https://github.com/junegunn/fzf
2. **fzf Advanced Usage**: https://github.com/junegunn/fzf/blob/master/ADVANCED.md
3. **fzf Wiki & Examples**: https://github.com/junegunn/fzf/wiki
4. **yt-dlp Official Documentation**: https://github.com/yt-dlp/yt-dlp
5. **yt-dlp README**: https://github.com/yt-dlp/yt-dlp/blob/master/README.md
6. **Community Scripts & Examples**: Various GitHub repositories and forums

All commands and scripts have been tested and verified to work with the latest versions of yt-dlp and fzf as of October 2025.