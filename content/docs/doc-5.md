---
title: "Document 5"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Document 5

This is content for document 5 - Modified at 2025-10-27T11:35:00Z

```bash
# Example code
echo "Document 5"
```

## Section 1

Content here...

## Section 2

More content...

## Led.sh

```bash
#!/bin/bash

# File organizer script using fd
# Organizes files from SRC_DIR into categorized subdirectories in DEST_BASE
# Creates unique timestamped logs (text and JSON) for each run to avoid interference
# Skip_Dir contains relative paths (e.g., Android/data) to exclude from SRC_DIR
# Handles duplicate files by renaming with numbered suffixes (e.g., file(1).mkv, file(2).mkv)

# Configuration
SRC_DIR="/storage/emulated/0"
DEST_BASE="/storage/emulated/0/00/sort/"
LOG_DIR="/storage/emulated/0/0l"
PHOTO_DIR="$DEST_BASE/pho"
VIDEO_DIR="$DEST_BASE/vid"
APK_DIR="$DEST_BASE/apk"
AUDIO_DIR="$DEST_BASE/mus"
DOC_DIR="$DEST_BASE/doc"

# Size filters (configurable)
MIN_SIZE="+10k"  # Minimum file size (e.g., 10KB)
MAX_SIZE="-5G"   # Maximum file size (e.g., 5GB)

# Directories to skip (relative to SRC_DIR)
Skip_Dir=(
    "Android"
    "arch"
    "00"
    "Ringtones"
    "0b"
    "00/sort"  # Exclude the destination base directory
)

# Extensions to skip (e.g., system or temporary files)
Skip_Extensions=(
    "nomedia"
    "cache"
    "rd"
    "tmp"
    "bak"
    "log"
)

# File type definitions
declare -A FILE_TYPES=(
    ["jpg"]="pho" ["jpeg"]="pho" ["png"]="pho" ["gif"]="pho" ["bmp"]="pho" ["webp"]="pho" ["heic"]="pho" ["tiff"]="pho"
    ["mp4"]="vid" ["mkv"]="vid" ["mov"]="vid" ["avi"]="vid" ["flv"]="vid" ["webm"]="vid" ["wmv"]="vid" ["3gp"]="vid"
    ["apk"]="apk" ["apkm"]="apk" ["xapk"]="apk"
    ["mp3"]="mus" ["wav"]="mus" ["flac"]="mus" ["ogg"]="mus" ["opus"]="mus" ["m4a"]="mus" ["aac"]="mus" ["wma"]="mus"
    ["html"]="doc" ["pdf"]="doc" ["doc"]="doc" ["docx"]="doc" ["epub"]="doc" ["odt"]="doc" ["rtf"]="doc" ["pages"]="doc"
    ["zip"]="doc" ["rar"]="doc" ["7z"]="doc" ["tar"]="doc"
    ["tar.gz"]="doc" ["tar.bz2"]="doc" ["tar.xz"]="doc"
)

# Check dependencies
if ! command -v fd >/dev/null 2>&1; then
    echo "Error: fd is not installed. Please install it first."
    exit 1
fi

if ! command -v jq >/dev/null 2>&1; then
    echo "Error: jq is not installed. Please install it for JSON logging."
    exit 1
fi

# Validate SRC_DIR
if [[ ! -d "$SRC_DIR" || ! -r "$SRC_DIR" ]]; then
    echo "Error: Source directory '$SRC_DIR' does not exist or is not readable."
    exit 1
fi

# Create directories
mkdir -p "$PHOTO_DIR" "$VIDEO_DIR" "$APK_DIR" "$AUDIO_DIR" "$DOC_DIR" "$LOG_DIR" || {
    echo "Error: Failed to create directories. Check permissions."
    exit 1
}

# Initialize logs with unique timestamp
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
LOG_FILE="$LOG_DIR/organize_${TIMESTAMP}.log"
JSON_LOG="$LOG_DIR/organize_${TIMESTAMP}.json"
SKIPPED_LOG="$LOG_DIR/skipped_${TIMESTAMP}.log"

# Initialize logs
echo '{"moved": [], "skipped": [], "errors": []}' > "$JSON_LOG"
touch "$LOG_FILE" "$SKIPPED_LOG"

# Cleanup temporary files on exit
trap 'rm -f "${JSON_LOG}.tmp.moved" "${JSON_LOG}.tmp.skipped" "${JSON_LOG}.tmp.errors" "${JSON_LOG}.tmp.array" "${JSON_LOG}.tmp2"' EXIT INT TERM

# Build fd command as an array
FD_CMD=(fd -t f -0 "$SRC_DIR")
for ext in "${!FILE_TYPES[@]}"; do
    FD_CMD+=(-e "$ext")
done
FD_CMD+=(--size "$MIN_SIZE" --size "$MAX_SIZE")
for dir in "${Skip_Dir[@]}"; do
    FD_CMD+=(--exclude "${dir}/**")
done

# Initialize counters
MOVED=0
SKIPPED=0
ERRORS=0

# Function to generate a unique destination path
get_unique_dest() {
    local dest_dir="$1"
    local filename="$2"
    local base_name="${filename%.*}"
    local extension="${filename##*.}"
    local dest_file="$dest_dir/$filename"
    local counter=1

    # If file exists, append numbered suffix
    while [ -f "$dest_file" ]; do
        dest_file="$dest_dir/${base_name}($counter).${extension}"
        ((counter++))
    done
    echo "$dest_file"
}

# Function to get effective extension
get_effective_extension() {
    local filename="$1"
    local simple_ext="${filename##*.}"
    simple_ext="${simple_ext,,}"  # Convert to lowercase
    local effective_ext="$simple_ext"

    # Handle multi-part extensions
    case "$simple_ext" in
        "gz"|"bz2"|"xz")
            local before="${filename%.$simple_ext}"
            local before_ext="${before##*.}"
            before_ext="${before_ext,,}"
            if [[ "$before_ext" == "tar" ]]; then
                effective_ext="tar.$simple_ext"
            fi
            ;;
    esac
    echo "$effective_ext"
}

# Process files function
process_file() {
    local file="$1"
    local filename=$(basename "$file")
    local extension
    extension=$(get_effective_extension "$filename")
    local simple_ext="${filename##*.}"
    simple_ext="${simple_ext,,}"

    # Skip files with no extension
    if [[ -z "$simple_ext" || "$simple_ext" =~ [^a-z0-9] ]]; then
        echo "$(date): SKIPPED (no valid extension) - $file" >> "$LOG_FILE"
        echo "$file -> no valid extension" >> "$SKIPPED_LOG"
        jq -n --arg src "$file" --arg reason "no valid extension" '{source:$src, reason:$reason}' >> "${JSON_LOG}.tmp.skipped"
        ((SKIPPED++))
        return
    fi

    echo "Processing file: $file" >> "$LOG_FILE"

    # Check if simple extension is in skip list
    for skip_ext in "${Skip_Extensions[@]}"; do
        if [[ "$simple_ext" == "${skip_ext,,}" ]]; then
            echo "$(date): SKIPPED (extension in skip list: $simple_ext) - $file" >> "$LOG_FILE"
            echo "$file -> skipped extension: $simple_ext" >> "$SKIPPED_LOG"
            jq -n --arg src "$file" --arg reason "skipped extension: $simple_ext" '{source:$src, reason:$reason}' >> "${JSON_LOG}.tmp.skipped"
            ((SKIPPED++))
            return
        fi
    done

    # Check if effective extension is defined in FILE_TYPES
    if [[ -n "${FILE_TYPES[$extension]}" ]]; then
        local dest_dir
        case "${FILE_TYPES[$extension]}" in
            pho) dest_dir="$PHOTO_DIR" ;;
            vid) dest_dir="$VIDEO_DIR" ;;
            apk) dest_dir="$APK_DIR" ;;
            mus) dest_dir="$AUDIO_DIR" ;;
            doc) dest_dir="$DOC_DIR" ;;
        esac
        local dest_file
        dest_file=$(get_unique_dest "$dest_dir" "$filename")

        if mv "$file" "$dest_file" 2>>"$LOG_FILE"; then
            echo "$(date): MOVED - $file -> $dest_file" >> "$LOG_FILE"
            jq -n --arg src "$file" --arg dst "$dest_file" '{source:$src, destination:$dst, method:"move"}' >> "${JSON_LOG}.tmp.moved"
            ((MOVED++))
        else
            if cp "$file" "$dest_file" 2>>"$LOG_FILE" && rm "$file" 2>>"$LOG_FILE"; then
                echo "$(date): COPIED+REMOVED (cross-device) - $file -> $dest_file" >> "$LOG_FILE"
                jq -n --arg src "$file" --arg dst "$dest_file" '{source:$src, destination:$dst, method:"copy+remove"}' >> "${JSON_LOG}.tmp.moved"
                ((MOVED++))
            else
                local error_msg="Failed to move/copy: $?"
                echo "$(date): ERROR - $error_msg - $file" >> "$LOG_FILE"
                jq -n --arg src "$file" --arg dst "$dest_file" --arg err "$error_msg" '{source:$src, destination:$dst, error:$err}' >> "${JSON_LOG}.tmp.errors"
                ((ERRORS++))
            fi
        fi
    else
        echo "$(date): SKIPPED (unknown extension: $extension) - $file" >> "$LOG_FILE"
        echo "$file -> unknown extension: $extension" >> "$SKIPPED_LOG"
        jq -n --arg src "$file" --arg reason "unknown extension: $extension" '{source:$src, reason:$reason}' >> "${JSON_LOG}.tmp.skipped"
        ((SKIPPED++))
    fi
}

# Process files using null-delimited input
while IFS= read -r -d '' file; do
    process_file "$file"
done < <("${FD_CMD[@]}")

# Write batched JSON updates
for type in moved skipped errors; do
    if [ -f "${JSON_LOG}.tmp.$type" ]; then
        jq -s '.' "${JSON_LOG}.tmp.$type" > "${JSON_LOG}.tmp.array"
        jq --argjson array "$(cat "${JSON_LOG}.tmp.array")" ".$type = \$array" "$JSON_LOG" > "${JSON_LOG}.tmp2"
        mv "${JSON_LOG}.tmp2" "$JSON_LOG"
        rm -f "${JSON_LOG}.tmp.$type" "${JSON_LOG}.tmp.array"
    fi
done

# Finalize
echo -e "\nFile organization complete! Moved $MOVED files, skipped $SKIPPED files, errors $ERRORS."
echo "Logs saved to: $LOG_DIR"
echo "JSON log: $JSON_LOG"
```
