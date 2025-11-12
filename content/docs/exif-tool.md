---
title: "exif-tool"
description: "metadata"
date: 2025-11-12T14:16:30Z
lastmod: 2025-11-12T14:16:30Z
tags: ["metadata"]
draft: false
---

# 📱 ExifTool CLI Guide for Termux (2025 Edition)

## 📋 Table of Contents
- [About ExifTool](#about-exiftool)
- [Installation in Termux](#installation-in-termux)
- [Latest Version Info](#latest-version-info)
- [Complete Metadata Removal Guide](#complete-metadata-removal-guide)
- [Essential Daily Commands](#essential-daily-commands)
- [Advanced Usage & Examples](#advanced-usage--examples)
- [Privacy & Anti-Tracking Tips](#privacy--anti-tracking-tips)

---

## 🔍 About ExifTool

**ExifTool** is a powerful command-line application for reading, writing, and editing meta information in a wide variety of files. Created by Phil Harvey, it's the most comprehensive metadata tool available.

- **Official Website**: https://exiftool.org/
- **GitHub Mirror**: https://github.com/exiftool/exiftool
- **Developer**: Phil Harvey
- **License**: Perl Artistic License / GPL
- **Supported Formats**: 200+ file types

---

## 📥 Installation in Termux

### Method 1: Using pkg (Recommended - Fastest)

```bash
# Update package lists
pkg update && pkg upgrade

# Install ExifTool
pkg install exiftool

# Verify installation
exiftool -ver
```

### Method 2: Using Perl CPAN (Latest Development Version)

```bash
# Install dependencies
pkg install perl wget make clang

# Download latest version from official site
cd ~
wget https://exiftool.org/Image-ExifTool-12.76.tar.gz

# Extract
tar -xzf Image-ExifTool-12.76.tar.gz

# Navigate to directory
cd Image-ExifTool-12.76

# Install
perl Makefile.PL
make test
make install

# Verify
exiftool -ver
```

### Method 3: Direct Perl Script (Portable)

```bash
# Download the standalone version
cd ~
mkdir -p ~/bin
cd ~/bin
wget https://exiftool.org/exiftool-12.76.tar.gz
tar -xzf exiftool-12.76.tar.gz
cd exiftool-12.76
chmod +x exiftool

# Add to PATH (add this to ~/.bashrc)
echo 'export PATH="$HOME/bin/exiftool-12.76:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Test
exiftool -ver
```

---

## 📊 Latest Version Info

**As of November 2025:**
- **Latest Stable Version**: 12.76+ (Check official site for exact version)
- **Release Frequency**: Updated regularly (monthly/bi-monthly)
- **Official Source**: https://exiftool.org/

### Check Your Version

```bash
exiftool -ver
```

### Update ExifTool in Termux

```bash
# If installed via pkg
pkg upgrade exiftool

# If installed manually, re-download and install latest version
cd ~
wget https://exiftool.org/Image-ExifTool-12.76.tar.gz
# Then follow installation steps above
```

---

## 🗑️ Complete Metadata Removal Guide

### ⚠️ CRITICAL: Privacy & Anti-Tracking

Metadata can reveal:
- 📍 GPS location (where photo was taken)
- 📱 Device information (phone model, camera)
- 🕐 Timestamps (when file was created/modified)
- 👤 Author/owner information
- 🖥️ Software used to create/edit file
- 📝 Edit history and comments

---

## 🔒 Remove ALL Metadata from Images/Photos

### Single Image

```bash
# Remove ALL metadata (RECOMMENDED for privacy)
exiftool -all= image.jpg

# This creates a backup with _original suffix
# The cleaned file replaces the original

# To avoid backup files
exiftool -all= -overwrite_original image.jpg
```

### Multiple Images in Directory

```bash
# Remove metadata from all images in current directory
exiftool -all= -overwrite_original *.jpg

# All images recursively in subdirectories
exiftool -all= -overwrite_original -r /path/to/directory/

# Specific formats
exiftool -all= -overwrite_original *.jpg *.jpeg *.png *.gif *.webp
```

### Remove GPS Data Specifically

```bash
# Remove only GPS/location data
exiftool -gps:all= -overwrite_original image.jpg

# Remove GPS from all images in directory
exiftool -gps:all= -overwrite_original -r .
```

---

## 🎥 Remove ALL Metadata from Videos

### Single Video

```bash
# Remove all metadata from video
exiftool -all= -overwrite_original video.mp4

# For multiple formats
exiftool -all= -overwrite_original *.mp4 *.mov *.avi *.mkv *.webm
```

### Batch Process Videos

```bash
# All videos in directory and subdirectories
exiftool -all= -overwrite_original -r -ext mp4 -ext mov -ext avi /path/to/videos/
```

---

## 📄 Remove Metadata from Text/Document Files

### Markdown Files (.md)

```bash
# Note: MD files typically have minimal metadata
# But can contain YAML front matter

# Remove any embedded metadata
exiftool -all= -overwrite_original *.md

# Recursive for all MD files
find . -name "*.md" -exec exiftool -all= -overwrite_original {} \;
```

### Shell Scripts (.sh)

```bash
# Remove metadata from shell scripts
exiftool -all= -overwrite_original *.sh

# Note: This won't affect shebang or script content
# Only filesystem and extended metadata
```

### Python Files (.py)

```bash
# Remove metadata from Python files
exiftool -all= -overwrite_original *.py

# Batch process
exiftool -all= -overwrite_original -r -ext py /path/to/scripts/
```

### PDF Documents

```bash
# Remove ALL metadata from PDFs
exiftool -all= -overwrite_original document.pdf

# Remove author, title, subject, keywords
exiftool -Author= -Title= -Subject= -Keywords= -overwrite_original document.pdf

# Batch process PDFs
exiftool -all= -overwrite_original *.pdf
```

### Microsoft Office Documents

```bash
# Word, Excel, PowerPoint
exiftool -all= -overwrite_original *.docx *.xlsx *.pptx

# Older formats
exiftool -all= -overwrite_original *.doc *.xls *.ppt
```

---

## 🚀 Essential Daily Commands

### 1. View All Metadata

```bash
# View all metadata from a file
exiftool image.jpg

# View specific tags
exiftool -GPS* -Make -Model image.jpg

# Export to text file
exiftool image.jpg > metadata.txt

# View in organized groups
exiftool -G image.jpg
```

### 2. Check for GPS/Location Data

```bash
# Check if file contains GPS data
exiftool -gps:all image.jpg

# Quick check (returns nothing if no GPS data)
exiftool -n -GPS* image.jpg
```

### 3. Strip Metadata but Keep Orientation

```bash
# Remove all except orientation (important for photos)
exiftool -all= -tagsfromfile @ -orientation -overwrite_original image.jpg

# For all images in directory
exiftool -all= -tagsfromfile @ -orientation -overwrite_original *.jpg
```

### 4. Remove Metadata from Specific Tags Only

```bash
# Remove only author and copyright
exiftool -Author= -Copyright= -overwrite_original document.pdf

# Remove GPS and timestamps
exiftool -gps:all= -AllDates= -overwrite_original photo.jpg
```

### 5. Batch Process Entire Directory Tree

```bash
# Remove ALL metadata from ALL files recursively
exiftool -all= -overwrite_original -r /path/to/directory/

# Process only specific file types
exiftool -all= -overwrite_original -r -ext jpg -ext png -ext mp4 /path/to/directory/
```

### 6. Create Clean Copies (Keep Originals)

```bash
# Create cleaned copy in new directory
mkdir cleaned
exiftool -all= -o cleaned/ *.jpg

# This keeps originals untouched and creates clean versions
```

### 7. View Before & After

```bash
# Before removal
exiftool image.jpg > before.txt

# Remove metadata
exiftool -all= -overwrite_original image.jpg

# After removal
exiftool image.jpg > after.txt

# Compare
diff before.txt after.txt
```

### 8. Remove Thumbnails (Hidden Preview Images)

```bash
# Remove embedded thumbnail
exiftool -ThumbnailImage= -overwrite_original image.jpg

# Remove all previews
exiftool -all:all= -overwrite_original image.jpg
```

### 9. Verify Complete Removal

```bash
# Check if metadata was removed
exiftool -a -G1 image.jpg

# Should show minimal information only
```

### 10. Emergency Full Directory Clean

```bash
# NUCLEAR OPTION: Remove ALL metadata from EVERYTHING
cd /path/to/sensitive/files/
exiftool -all= -overwrite_original -r .

# This processes all files recursively
```

---

## 🎯 Advanced Usage & Examples

### Example 1: Preparing Photos for Anonymous Upload

```bash
# Scenario: Upload photos to public forum without revealing identity/location

# Step 1: Navigate to photo directory
cd ~/Pictures/to_upload/

# Step 2: Check what metadata exists
exiftool -a -G1 photo.jpg

# Step 3: Remove ALL metadata
exiftool -all= -overwrite_original *.jpg

# Step 4: Verify clean
exiftool photo.jpg  # Should show minimal info

# Step 5: Optionally, strip EXIF but keep image orientation
exiftool -all= -tagsfromfile @ -orientation -overwrite_original *.jpg
```

### Example 2: Clean Video Before Sharing

```bash
# Scenario: Share video without revealing camera info, location, timestamps

# Check current metadata
exiftool -a -G1 video.mp4

# Remove everything
exiftool -all= -overwrite_original video.mp4

# Verify
exiftool video.mp4
```

### Example 3: Anonymize Documents for Whistleblowing

```bash
# IMPORTANT: For sensitive documents

# Check metadata
exiftool -a -G1 document.pdf

# Remove all identifying information
exiftool -all= -overwrite_original document.pdf

# Double-check author info is gone
exiftool -Author -Creator -Producer document.pdf

# Additional: Remove timestamps
touch -t 202501010000 document.pdf  # Sets to Jan 1, 2025 00:00
```

### Example 4: Batch Clean Before Cloud Backup

```bash
# Clean all files before uploading to cloud

# Create script for automation
nano ~/bin/clean_before_backup.sh
```

Add this content:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Clean Before Backup Script

BACKUP_DIR="$HOME/storage/shared/Documents/to_backup"

echo "Cleaning metadata from all files in $BACKUP_DIR"

# Remove all metadata
exiftool -all= -overwrite_original -r "$BACKUP_DIR"

echo "Cleanup complete!"
echo "Files cleaned: "
find "$BACKUP_DIR" -type f | wc -l
```

Make executable and run:

```bash
chmod +x ~/bin/clean_before_backup.sh
~/bin/clean_before_backup.sh
```

### Example 5: Compare Metadata Between Files

```bash
# Compare two images
exiftool -s image1.jpg > meta1.txt
exiftool -s image2.jpg > meta2.txt
diff meta1.txt meta2.txt
```

### Example 6: Extract Specific Information

```bash
# Extract only camera information
exiftool -Make -Model -LensModel image.jpg

# Extract date/time
exiftool -CreateDate -ModifyDate image.jpg

# Extract GPS coordinates
exiftool -n -GPSLatitude -GPSLongitude image.jpg
```

### Example 7: Preserve Copyright but Remove Everything Else

```bash
# Keep copyright, remove everything else
exiftool -all= -tagsfromfile @ -copyright -overwrite_original image.jpg
```

---

## 🛡️ Privacy & Anti-Tracking Tips

### 🔴 Maximum Privacy Setup

```bash
# 1. Create alias for quick metadata removal
echo 'alias cleanmeta="exiftool -all= -overwrite_original"' >> ~/.bashrc
source ~/.bashrc

# Usage: cleanmeta image.jpg

# 2. Create function for recursive cleaning
echo 'cleanall() { exiftool -all= -overwrite_original -r "$1"; }' >> ~/.bashrc
source ~/.bashrc

# Usage: cleanall /path/to/directory
```

### 🔍 What Metadata Can Reveal

```bash
# Check what your phone camera saves
exiftool -a -G1 photo_from_phone.jpg | grep -i gps
exiftool -a -G1 photo_from_phone.jpg | grep -i make
exiftool -a -G1 photo_from_phone.jpg | grep -i software
```

Common tracking metadata:
- **GPS Coordinates**: Exact location
- **Device Make/Model**: Phone or camera brand
- **Software**: Apps used to edit
- **Serial Number**: Unique device identifier
- **User Comment**: Hidden notes
- **Timestamps**: When/where taken

### 🎯 Best Practices

1. **Always clean before sharing publicly**
   ```bash
   exiftool -all= -overwrite_original file.jpg
   ```

2. **Verify after cleaning**
   ```bash
   exiftool file.jpg  # Check results
   ```

3. **Use automation scripts** for routine cleaning

4. **Clean at upload time**, not storage time (keep originals for yourself)

5. **For maximum anonymity**: Re-encode images
   ```bash
   # Install imagemagick
   pkg install imagemagick
   
   # Re-encode (destroys all metadata)
   convert input.jpg -quality 90 output.jpg
   ```

---

## 📚 Additional File Types

### Audio Files (MP3, FLAC, OGG, M4A)

```bash
# Remove metadata from audio
exiftool -all= -overwrite_original *.mp3

# Remove ID3 tags specifically
exiftool -all:all= -overwrite_original song.mp3

# Batch process music library
exiftool -all= -overwrite_original -r ~/Music/
```

### RAW Camera Files

```bash
# DNG, CR2, NEF, ARW, etc.
exiftool -all= -overwrite_original *.dng *.cr2 *.nef *.arw

# Be careful: May affect image processing
```

### Archive Files (ZIP, RAR)

```bash
# ZIP files can contain metadata
exiftool -all= -overwrite_original archive.zip
```

---

## 🔧 Useful Command Combinations

### Find Files with GPS Data

```bash
# Search for files containing GPS metadata
exiftool -if '$gpslatitude' -filename -r /path/to/search/

# Count files with GPS
exiftool -if '$gpslatitude' -filename -r . | wc -l
```

### Create Metadata Report

```bash
# Generate HTML report of metadata
exiftool -h -r /path/to/directory/ > metadata_report.html

# CSV format for analysis
exiftool -csv -r /path/to/directory/ > metadata.csv
```

### Remove Specific Metadata Tags

```bash
# Remove only author-related tags
exiftool -Author= -Creator= -Artist= -overwrite_original file.jpg

# Remove only date information
exiftool -AllDates= -overwrite_original file.jpg

# Remove comments/descriptions
exiftool -Comment= -Description= -UserComment= -overwrite_original file.jpg
```

---

## 🚨 Important Notes & Warnings

### ⚠️ Backup Warning

```bash
# ExifTool creates backup files by default
# Files ending in _original are backups

# To delete all backup files after cleaning:
find . -name "*_original" -delete

# Or use -overwrite_original flag to avoid backups:
exiftool -all= -overwrite_original file.jpg
```

### ⚠️ File Integrity

- ExifTool is **non-destructive** to image data
- It only modifies metadata sections
- Original image quality is preserved
- However, always keep backups of important files

### ⚠️ Limitations

```bash
# Some metadata cannot be removed:
# - File system timestamps (use touch command)
# - Intrinsic image properties (dimensions, color space)
# - Embedded watermarks (not metadata)
```

### Change File Timestamps

```bash
# Randomize file modification time
touch -t 202501010000 file.jpg  # YYYYMMDDHHMM

# Set to current time
touch file.jpg

# Batch randomize timestamps
for f in *.jpg; do
  touch -t "$(date +%Y%m%d%H%M -d "$RANDOM seconds ago")" "$f"
done
```

---

## 📖 Quick Reference Card

```bash
# View metadata
exiftool file.jpg

# Remove ALL metadata (with backup)
exiftool -all= file.jpg

# Remove ALL metadata (no backup)
exiftool -all= -overwrite_original file.jpg

# Batch process directory
exiftool -all= -overwrite_original *.jpg

# Recursive (all subdirectories)
exiftool -all= -overwrite_original -r /path/

# Keep orientation only
exiftool -all= -tagsfromfile @ -orientation -overwrite_original file.jpg

# Check GPS data
exiftool -gps:all file.jpg

# Remove GPS only
exiftool -gps:all= -overwrite_original file.jpg

# Check version
exiftool -ver

# Get help
exiftool -h
man exiftool
```

---

## 🔗 Official Resources

- **Official Website**: https://exiftool.org/
- **Documentation**: https://exiftool.org/exiftool_pod.html
- **Forum**: https://exiftool.org/forum/
- **GitHub Mirror**: https://github.com/exiftool/exiftool
- **Tag Names Reference**: https://exiftool.org/TagNames/

---

## 📝 Automation Script Example

Create a comprehensive cleaning script:

```bash
nano ~/bin/metadata_cleaner.sh
```

Add this content:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Metadata Cleaner Script for Termux
# Usage: metadata_cleaner.sh /path/to/directory

if [ -z "$1" ]; then
    echo "Usage: $0 <directory>"
    exit 1
fi

TARGET_DIR="$1"

echo "╔════════════════════════════════════╗"
echo "║   Metadata Cleaner for Termux      ║"
echo "╚════════════════════════════════════╝"
echo ""
echo "Target Directory: $TARGET_DIR"
echo ""

# Count files
FILE_COUNT=$(find "$TARGET_DIR" -type f | wc -l)
echo "Found $FILE_COUNT files"
echo ""

# Confirm
read -p "Remove ALL metadata from these files? (yes/no): " CONFIRM

if [ "$CONFIRM" != "yes" ]; then
    echo "Cancelled."
    exit 0
fi

echo ""
echo "Cleaning metadata..."
echo ""

# Remove all metadata
exiftool -all= -overwrite_original -r -progress "$TARGET_DIR"

echo ""
echo "✓ Complete!"
echo ""

# Delete backup files if any
BACKUP_COUNT=$(find "$TARGET_DIR" -name "*_original" | wc -l)
if [ $BACKUP_COUNT -gt 0 ]; then
    echo "Found $BACKUP_COUNT backup files"
    read -p "Delete backup files? (yes/no): " DELETE_BACKUP
    if [ "$DELETE_BACKUP" = "yes" ]; then
        find "$TARGET_DIR" -name "*_original" -delete
        echo "✓ Backups deleted"
    fi
fi

echo ""
echo "All done! Your files are now metadata-free."
```

Make it executable:

```bash
chmod +x ~/bin/metadata_cleaner.sh
```

Use it:

```bash
~/bin/metadata_cleaner.sh ~/Pictures/to_clean/
```

---

## 🎓 Learning Resources

### Practice Commands

```bash
# 1. Create test directory
mkdir -p ~/exiftool_practice
cd ~/exiftool_practice

# 2. Take/copy some test photos here

# 3. View metadata
exiftool test_photo.jpg

# 4. Practice removal
exiftool -all= test_photo.jpg

# 5. Check results
exiftool test_photo.jpg

# 6. Clean up
rm *_original
```

---

## 🆘 Troubleshooting

### ExifTool Not Found

```bash
# Check if installed
which exiftool

# Reinstall
pkg reinstall exiftool

# Check PATH
echo $PATH
```

### Permission Denied

```bash
# Make sure you have write permissions
ls -la file.jpg
chmod 644 file.jpg  # Give write permission
```

### Files Not Cleaning

```bash
# Some formats may be read-only or corrupted
# Try with -overwrite_original_in_place
exiftool -all= -overwrite_original_in_place file.jpg
```

---

## 🏁 Final Privacy Checklist

Before sharing any file publicly:

- [ ] Remove all metadata: `exiftool -all= -overwrite_original file.jpg`
- [ ] Verify removal: `exiftool file.jpg`
- [ ] Check GPS data: `exiftool -gps:all file.jpg`
- [ ] Randomize timestamps if needed: `touch -t 202501010000 file.jpg`
- [ ] Consider re-encoding: `convert input.jpg output.jpg`
- [ ] Test upload with throwaway file first
- [ ] Never upload from original device camera roll

---

## 📌 Quick Setup for New Termux Install

```bash
# Complete setup script
pkg update && pkg upgrade -y
pkg install exiftool imagemagick -y

# Create cleaning alias
echo 'alias cleanmeta="exiftool -all= -overwrite_original"' >> ~/.bashrc
echo 'alias cleandir="exiftool -all= -overwrite_original -r"' >> ~/.bashrc
source ~/.bashrc

# Test
exiftool -ver

echo "ExifTool setup complete!"
echo "Usage: cleanmeta file.jpg"
echo "Usage: cleandir /path/to/directory/"
```

---

**Note**: I noticed you included a link to super-productivity (https://github.com/johannesjo/super-productivity), but that's a productivity/task management application, not related to ExifTool. If you need information about that tool as well, please let me know!

**Last Updated**: November 2025  
**ExifTool Version**: 12.76+ (Check https://exiftool.org for latest)

---

## 📥 Save This Guide

```bash
# Save this markdown file
nano ~/exiftool_guide.md
# Paste content and save with Ctrl+X, Y, Enter

# View anytime with:
cat ~/exiftool_guide.md
# Or with less for scrolling:
less ~/exiftool_guide.md
```

---

Stay safe and protect your privacy!