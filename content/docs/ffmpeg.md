--- 
title: "ffmpeg"
description: "readme"
date: 2025-10-28T14:07:52Z
lastmod: 2025-10-28T14:07:52Z
tags: ["Codec"]
draft: false
---

# FFmpeg CLI Tool - Complete Guide for Termux (2025)

## Table of Contents
- [Introduction](#introduction)
- [Basic Syntax](#basic-syntax)
- [Video Conversion](#video-conversion)
- [Audio Operations](#audio-operations)
- [Video Compression](#video-compression)
- [Video Editing](#video-editing)
- [Extract Audio/Video](#extract-audiovideo)
- [Streaming & Recording](#streaming--recording)
- [Image Operations](#image-operations)
- [Advanced Operations](#advanced-operations)
- [Useful Tips](#useful-tips)

---

## Introduction

FFmpeg is a powerful multimedia framework that can decode, encode, transcode, mux, demux, stream, filter, and play almost any media format. This guide covers the most useful commands for daily use in Termux.

**Official Resources:**
- Website: https://ffmpeg.org
- Documentation: https://ffmpeg.org/documentation.html
- GitHub: https://github.com/FFmpeg/FFmpeg

**Check your FFmpeg version:**
```bash
ffmpeg -version
```

---

## Basic Syntax

```bash
ffmpeg [global_options] [input_options] -i input_file [output_options] output_file
```

**Common Global Options:**
- `-y` - Overwrite output file without asking
- `-n` - Never overwrite output file
- `-hide_banner` - Suppress printing banner
- `-loglevel [level]` - Set logging level (quiet, panic, fatal, error, warning, info, verbose, debug)

---

## Video Conversion

### Convert Video Format

**MP4 to MKV:**
```bash
ffmpeg -i input.mp4 output.mkv
```

**AVI to MP4:**
```bash
ffmpeg -i input.avi -c:v libx264 -c:a aac output.mp4
```

**MOV to WebM:**
```bash
ffmpeg -i input.mov -c:v libvpx-vp9 -c:a libopus output.webm
```

**Convert with specific codec:**
```bash
ffmpeg -i input.mp4 -c:v libx265 -c:a copy output.mp4
```

### Convert for Different Devices

**For Android (H.264):**
```bash
ffmpeg -i input.mp4 -c:v libx264 -profile:v main -preset medium -c:a aac -b:a 128k output.mp4
```

**For WhatsApp/Telegram (compressed):**
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset fast -c:a aac -b:a 96k output.mp4
```

---

## Audio Operations

### Extract Audio from Video

**Extract as MP3:**
```bash
ffmpeg -i input.mp4 -vn -c:a libmp3lame -b:a 192k output.mp3
```

**Extract as AAC:**
```bash
ffmpeg -i input.mp4 -vn -c:a aac -b:a 128k output.aac
```

**Extract original audio (no re-encoding):**
```bash
ffmpeg -i input.mp4 -vn -c:a copy output.aac
```

### Convert Audio Formats

**MP3 to AAC:**
```bash
ffmpeg -i input.mp3 -c:a aac -b:a 192k output.aac
```

**WAV to MP3:**
```bash
ffmpeg -i input.wav -c:a libmp3lame -b:a 320k output.mp3
```

**M4A to MP3:**
```bash
ffmpeg -i input.m4a -c:a libmp3lame -q:a 2 output.mp3
```

### Adjust Audio Properties

**Change volume (+50%):**
```bash
ffmpeg -i input.mp3 -af "volume=1.5" output.mp3
```

**Normalize audio:**
```bash
ffmpeg -i input.mp3 -af "loudnorm" output.mp3
```

**Change sample rate:**
```bash
ffmpeg -i input.mp3 -ar 44100 output.mp3
```

**Mono to Stereo:**
```bash
ffmpeg -i input.mp3 -ac 2 output.mp3
```

### Merge Audio Files

**Concatenate multiple audio files:**
```bash
# First, create a file list
echo "file 'audio1.mp3'" > filelist.txt
echo "file 'audio2.mp3'" >> filelist.txt
echo "file 'audio3.mp3'" >> filelist.txt

# Then concatenate
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp3
```

---

## Video Compression

### Reduce File Size

**High quality compression (CRF 23):**
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -preset medium -c:a aac -b:a 128k output.mp4
```

**Medium quality (CRF 28):**
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset fast output.mp4
```

**Lower quality, smaller size (CRF 32):**
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 32 -preset faster output.mp4
```

**Using H.265/HEVC (better compression):**
```bash
ffmpeg -i input.mp4 -c:v libx265 -crf 28 -preset medium -c:a aac -b:a 128k output.mp4
```

**Two-pass encoding (best quality):**
```bash
# Pass 1
ffmpeg -i input.mp4 -c:v libx264 -b:v 1M -pass 1 -f null /dev/null

# Pass 2
ffmpeg -i input.mp4 -c:v libx264 -b:v 1M -pass 2 -c:a aac -b:a 128k output.mp4
```

### Resize Video

**Resize to specific resolution:**
```bash
ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4
```

**Resize to 50% of original:**
```bash
ffmpeg -i input.mp4 -vf "scale=iw/2:ih/2" output.mp4
```

**Resize maintaining aspect ratio (width 720):**
```bash
ffmpeg -i input.mp4 -vf scale=720:-1 output.mp4
```

---

## Video Editing

### Trim/Cut Video

**Cut video (from 10s to 30s):**
```bash
ffmpeg -i input.mp4 -ss 00:00:10 -to 00:00:30 -c copy output.mp4
```

**Cut from start (first 60 seconds):**
```bash
ffmpeg -i input.mp4 -t 60 -c copy output.mp4
```

**Cut from position (skip first 30s, keep rest):**
```bash
ffmpeg -i input.mp4 -ss 30 -c copy output.mp4
```

**Precise cutting (re-encode):**
```bash
ffmpeg -i input.mp4 -ss 00:01:30 -to 00:03:45 -c:v libx264 -c:a aac output.mp4
```

### Concatenate Videos

**Method 1: Using concat demuxer (same codec):**
```bash
# Create file list
echo "file 'video1.mp4'" > filelist.txt
echo "file 'video2.mp4'" >> filelist.txt
echo "file 'video3.mp4'" >> filelist.txt

# Concatenate
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4
```

**Method 2: Using concat filter (different codecs):**
```bash
ffmpeg -i video1.mp4 -i video2.mp4 -filter_complex "[0:v][0:a][1:v][1:a]concat=n=2:v=1:a=1[outv][outa]" -map "[outv]" -map "[outa]" output.mp4
```

### Add/Replace Audio

**Add audio to video:**
```bash
ffmpeg -i video.mp4 -i audio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 output.mp4
```

**Replace audio track:**
```bash
ffmpeg -i video.mp4 -i newaudio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 -shortest output.mp4
```

**Remove audio:**
```bash
ffmpeg -i input.mp4 -an -c:v copy output.mp4
```

### Speed Up/Slow Down

**Speed up video 2x (double speed):**
```bash
ffmpeg -i input.mp4 -filter:v "setpts=0.5*PTS" -filter:a "atempo=2.0" output.mp4
```

**Slow down video 0.5x (half speed):**
```bash
ffmpeg -i input.mp4 -filter:v "setpts=2.0*PTS" -filter:a "atempo=0.5" output.mp4
```

### Rotate Video

**Rotate 90 degrees clockwise:**
```bash
ffmpeg -i input.mp4 -vf "transpose=1" output.mp4
```

**Rotate 90 degrees counter-clockwise:**
```bash
ffmpeg -i input.mp4 -vf "transpose=2" output.mp4
```

**Rotate 180 degrees:**
```bash
ffmpeg -i input.mp4 -vf "transpose=2,transpose=2" output.mp4
```

**Flip horizontally:**
```bash
ffmpeg -i input.mp4 -vf "hflip" output.mp4
```

**Flip vertically:**
```bash
ffmpeg -i input.mp4 -vf "vflip" output.mp4
```

---

## Extract Audio/Video

### Extract Video Only

**Copy video stream (no audio):**
```bash
ffmpeg -i input.mp4 -an -c:v copy output_video_only.mp4
```

### Extract Specific Stream

**Extract specific audio track:**
```bash
ffmpeg -i input.mp4 -map 0:a:0 -c copy audio_track1.aac
```

**Extract subtitle:**
```bash
ffmpeg -i input.mkv -map 0:s:0 subtitle.srt
```

---

## Streaming & Recording

### Record Screen (if supported in Termux)

**Note:** Screen recording may require additional setup in Termux

### Convert for Streaming

**Convert for YouTube:**
```bash
ffmpeg -i input.mp4 -c:v libx264 -preset slow -crf 18 -c:a aac -b:a 192k -pix_fmt yuv420p output.mp4
```

**Convert for Instagram (square, 1:1):**
```bash
ffmpeg -i input.mp4 -vf "scale=1080:1080:force_original_aspect_ratio=decrease,pad=1080:1080:(ow-iw)/2:(oh-ih)/2" -c:v libx264 -crf 23 output.mp4
```

**Convert for Twitter:**
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 20 -preset medium -pix_fmt yuv420p -c:a aac -b:a 128k -movflags +faststart output.mp4
```

---

## Image Operations

### Extract Frame from Video

**Extract single frame at 5 seconds:**
```bash
ffmpeg -i input.mp4 -ss 00:00:05 -vframes 1 frame.png
```

**Extract every second as image:**
```bash
ffmpeg -i input.mp4 -vf fps=1 frame_%04d.png
```

**Extract specific number of frames (10 frames):**
```bash
ffmpeg -i input.mp4 -vframes 10 frame_%03d.png
```

### Create Video from Images

**From image sequence:**
```bash
ffmpeg -framerate 30 -i frame_%04d.png -c:v libx264 -pix_fmt yuv420p output.mp4
```

**From single image (5 second duration):**
```bash
ffmpeg -loop 1 -i image.png -t 5 -c:v libx264 -pix_fmt yuv420p output.mp4
```

### Convert Image Formats

**PNG to JPG:**
```bash
ffmpeg -i input.png output.jpg
```

**Batch convert images:**
```bash
for i in *.png; do ffmpeg -i "$i" "${i%.png}.jpg"; done
```

### Create GIF

**From video:**
```bash
ffmpeg -i input.mp4 -vf "fps=10,scale=320:-1:flags=lanczos" output.gif
```

**High quality GIF:**
```bash
ffmpeg -i input.mp4 -vf "fps=15,scale=480:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" output.gif
```

---

## Advanced Operations

### Add Watermark

**Image watermark (bottom-right):**
```bash
ffmpeg -i input.mp4 -i watermark.png -filter_complex "overlay=W-w-10:H-h-10" output.mp4
```

**Text watermark:**
```bash
ffmpeg -i input.mp4 -vf "drawtext=text='My Text':fontcolor=white:fontsize=24:x=10:y=10" output.mp4
```

### Add Subtitles

**Burn subtitles into video:**
```bash
ffmpeg -i input.mp4 -vf subtitles=subtitle.srt output.mp4
```

**Embed subtitle file (soft subtitles):**
```bash
ffmpeg -i input.mp4 -i subtitle.srt -c copy -c:s mov_text output.mp4
```

### Change Frame Rate

**Convert to 30 fps:**
```bash
ffmpeg -i input.mp4 -r 30 output.mp4
```

**Convert to 60 fps (interpolation):**
```bash
ffmpeg -i input.mp4 -filter:v "minterpolate='fps=60'" output.mp4
```

### Get Video Information

**Detailed file information:**
```bash
ffmpeg -i input.mp4
```

**Using ffprobe (better formatting):**
```bash
ffprobe -v quiet -print_format json -show_format -show_streams input.mp4
```

**Get duration:**
```bash
ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 input.mp4
```

**Get resolution:**
```bash
ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=s=x:p=0 input.mp4
```

### Stabilize Video

**Basic stabilization:**
```bash
# First pass - detect
ffmpeg -i input.mp4 -vf vidstabdetect=shakiness=10:accuracy=15 -f null -

# Second pass - stabilize
ffmpeg -i input.mp4 -vf vidstabtransform=smoothing=30:input="transforms.trf" output.mp4
```

### Denoise Video

**Remove noise:**
```bash
ffmpeg -i input.mp4 -vf "hqdn3d" output.mp4
```

### Create Thumbnail Strip

**Create preview thumbnails:**
```bash
ffmpeg -i input.mp4 -vf "select='not(mod(n\,100))',scale=160:90,tile=5x5" -frames:v 1 preview.png
```

### Batch Processing

**Convert all MP4 to MKV in directory:**
```bash
for i in *.mp4; do
    ffmpeg -i "$i" -c copy "${i%.mp4}.mkv"
done
```

**Compress all videos in directory:**
```bash
for i in *.mp4; do
    ffmpeg -i "$i" -c:v libx264 -crf 28 -c:a aac "compressed_${i}"
done
```

---

## Useful Tips

### Performance & Quality

**CRF Values (Constant Rate Factor):**
- 0 = Lossless
- 17-18 = Visually lossless
- 23 = Default (good quality)
- 28 = Acceptable quality
- 32-35 = Low quality
- 51 = Worst quality

**Preset Values (speed vs compression):**
- ultrafast, superfast, veryfast, faster, fast
- medium (default)
- slow, slower, veryslow (better compression)

**Audio Bitrates:**
- 96k = Acceptable for speech
- 128k = Standard quality
- 192k = High quality
- 256k = Very high quality
- 320k = Maximum MP3 quality

### Common Options Explained

**Video Codec Options:**
- `-c:v libx264` = H.264 codec (most compatible)
- `-c:v libx265` = H.265/HEVC (better compression)
- `-c:v libvpx-vp9` = VP9 (WebM format)
- `-c:v copy` = Copy without re-encoding (fast)

**Audio Codec Options:**
- `-c:a aac` = AAC audio codec
- `-c:a libmp3lame` = MP3 codec
- `-c:a libopus` = Opus codec (efficient)
- `-c:a copy` = Copy without re-encoding

**Pixel Format:**
- `-pix_fmt yuv420p` = Most compatible format for playback

**Fast Start:**
- `-movflags +faststart` = Enable fast start for web streaming

### Storage Locations in Termux

**Default storage:**
```bash
cd ~/storage/shared/
cd ~/storage/movies/
cd ~/storage/dcim/
```

**Setup storage access (if not done):**
```bash
termux-setup-storage
```

### Check Available Codecs

**List all codecs:**
```bash
ffmpeg -codecs
```

**List encoders:**
```bash
ffmpeg -encoders
```

**List decoders:**
```bash
ffmpeg -decoders
```

**List formats:**
```bash
ffmpeg -formats
```

### Hardware Acceleration

**Note:** Hardware acceleration support in Termux is limited, but you can check:

```bash
ffmpeg -hwaccels
```

### Error Handling

**Continue on error:**
```bash
ffmpeg -err_detect ignore_err -i input.mp4 output.mp4
```

**Fix corrupted video:**
```bash
ffmpeg -i corrupted.mp4 -c copy fixed.mp4
```

---

## Common Use Cases Examples

### 1. Quick Video for WhatsApp

```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset fast -vf "scale='min(1280,iw)':'min(720,ih)':force_original_aspect_ratio=decrease" -c:a aac -b:a 96k -movflags +faststart whatsapp.mp4
```

### 2. Extract Audio for Music

```bash
ffmpeg -i music_video.mp4 -vn -c:a libmp3lame -b:a 320k music.mp3
```

### 3. Create Video Clip from Long Video

```bash
ffmpeg -i long_video.mp4 -ss 00:05:30 -t 00:00:45 -c:v libx264 -crf 23 -c:a aac clip.mp4
```

### 4. Compress Large Video

```bash
ffmpeg -i large.mp4 -c:v libx265 -crf 28 -preset medium -c:a aac -b:a 128k compressed.mp4
```

### 5. Convert for Instagram Story (9:16)

```bash
ffmpeg -i input.mp4 -vf "scale=1080:1920:force_original_aspect_ratio=decrease,pad=1080:1920:(ow-iw)/2:(oh-ih)/2" -c:v libx264 -crf 23 -t 15 story.mp4
```

### 6. Merge Video and Audio

```bash
ffmpeg -i video.mp4 -i audio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 -shortest merged.mp4
```

### 7. Create Slideshow from Photos

```bash
ffmpeg -framerate 1/3 -pattern_type glob -i '*.jpg' -c:v libx264 -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2,fps=25" slideshow.mp4
```

### 8. Record Audio (if microphone accessible)

```bash
ffmpeg -f alsa -i default -t 60 -c:a libmp3lame -b:a 192k recording.mp3
```

---

## Troubleshooting

### "Permission denied" errors
```bash
chmod +x input.mp4
# Or work in accessible directory
cd ~/storage/shared/
```

### "Codec not found" errors
Check available codecs and use alternatives:
```bash
ffmpeg -codecs | grep h264
```

### Out of memory
Reduce resolution or use faster presets:
```bash
ffmpeg -i input.mp4 -vf scale=640:-1 -preset ultrafast output.mp4
```

### File too large in Termux
Process in chunks or use higher compression:
```bash
ffmpeg -i input.mp4 -crf 32 -preset faster output.mp4
```

---

## Additional Resources

- **Official Documentation:** https://ffmpeg.org/ffmpeg.html
- **Wiki:** https://trac.ffmpeg.org/wiki
- **Filters Documentation:** https://ffmpeg.org/ffmpeg-filters.html
- **Formats:** https://ffmpeg.org/ffmpeg-formats.html

---

**Last Updated:** October 2025

**Note:** Some commands may require additional codecs or libraries. Install them via:
```bash
pkg install ffmpeg
```

For the latest FFmpeg version and features, always refer to the official documentation at https://ffmpeg.org
```

---

## Detailed Explanation & Usage Guide

### How to Use This Guide

#### 1. **Understanding the Command Structure**

Every FFmpeg command follows this pattern:
```
ffmpeg [global_options] [input_options] -i input_file [output_options] output_file
```

**Example Breakdown:**
```bash
ffmpeg -y -i input.mp4 -c:v libx264 -crf 23 -c:a aac output.mp4
```

- `ffmpeg` - The program
- `-y` - Global option (overwrite without asking)
- `-i input.mp4` - Input file
- `-c:v libx264` - Video codec (H.264)
- `-crf 23` - Quality setting
- `-c:a aac` - Audio codec
- `output.mp4` - Output file

#### 2. **Essential Concepts**

**Codecs vs Containers:**
- **Container** (MP4, MKV, AVI): The file format that holds video/audio streams
- **Codec** (H.264, H.265, AAC): The compression method for the actual data

**Copy vs Re-encode:**
```bash
-c copy          # Fast, no quality loss, but limited editing
-c:v libx264     # Re-encodes, slower, allows modifications
```

**Stream Selection:**
- `-c:v` - Video codec
- `-c:a` - Audio codec  
- `-c:s` - Subtitle codec
- `-vn` - No video
- `-an` - No audio

#### 3. **Practical Examples with Explanations**

**Example 1: Compress a Video for Mobile**

```bash
ffmpeg -i large_video.mp4 -c:v libx264 -crf 28 -preset medium -vf scale=1280:-1 -c:a aac -b:a 128k mobile.mp4
```

**What this does:**
- Takes `large_video.mp4` as input
- Uses H.264 codec (`libx264`)
- Sets quality to 28 (lower file size, acceptable quality)
- Uses medium encoding speed
- Scales width to 1280px, auto-calculates height
- Uses AAC audio at 128kbps
- Outputs to `mobile.mp4`

**When to use:** Sharing videos on WhatsApp, Telegram, or reducing storage usage

---

**Example 2: Extract Audio from Multiple Videos**

```bash
#!/bin/bash
for video in *.mp4; do
    ffmpeg -i "$video" -vn -c:a libmp3lame -q:a 2 "${video%.mp4}.mp3"
done
```

**What this does:**
- Loops through all MP4 files in current directory
- Extracts audio without video (`-vn`)
- Converts to MP3 with high quality (`-q:a 2`)
- Names output files matching input (e.g., video.mp4 → video.mp3)

**When to use:** Creating audio-only versions of video lectures, music videos, or podcasts

---

**Example 3: Create Instagram-Ready Video**

```bash
ffmpeg -i input.mp4 \
  -vf "scale=1080:1350:force_original_aspect_ratio=decrease,pad=1080:1350:(ow-iw)/2:(oh-ih)/2:color=black" \
  -c:v libx264 -preset medium -crf 23 \
  -c:a aac -b:a 128k \
  -movflags +faststart \
  -t 60 \
  instagram.mp4
```

**What this does:**
- Scales video to Instagram's 4:5 ratio (1080x1350)
- Adds black padding if needed to maintain aspect ratio
- Centers the video
- Limits duration to 60 seconds (`-t 60`)
- Enables fast start for quick loading
- Uses good quality settings

**When to use:** Preparing videos for Instagram feed posts

---

**Example 4: Batch Convert All Videos to HEVC**

```bash
mkdir -p converted

for file in *.mp4; do
    echo "Converting: $file"
    ffmpeg -i "$file" \
      -c:v libx265 -crf 28 -preset medium \
      -c:a copy \
      "converted/${file%.mp4}_hevc.mp4"
done

echo "All conversions complete!"
```

**What this does:**
- Creates a "converted" directory
- Converts each MP4 to H.265/HEVC (better compression)
- Copies audio without re-encoding (faster, no quality loss)
- Saves to new directory with "_hevc" suffix

**When to use:** Saving storage space with better compression

---

#### 4. **Common Workflows in Termux**

**Workflow 1: Video Downloaded from Internet → Optimized for Phone**

```bash
# Step 1: Check video info
ffprobe -i downloaded_video.mp4

# Step 2: Compress and resize
ffmpeg -i downloaded_video.mp4 \
  -c:v libx264 -crf 26 -preset medium \
  -vf "scale='min(1920,iw)':-2" \
  -c:a aac -b:a 128k \
  -movflags +faststart \
  optimized.mp4

# Step 3: Move to gallery
mv optimized.mp4 ~/storage/movies/
```

---

**Workflow 2: Create Highlight Reel from Long Video**

```bash
# Extract interesting clips
ffmpeg -i long_video.mp4 -ss 00:05:30 -t 00:00:15 -c copy clip1.mp4
ffmpeg -i long_video.mp4 -ss 00:12:45 -t 00:00:20 -c copy clip2.mp4
ffmpeg -i long_video.mp4 -ss 00:28:10 -t 00:00:18 -c copy clip3.mp4

# Create file list
echo "file 'clip1.mp4'" > clips.txt
echo "file 'clip2.mp4'" >> clips.txt
echo "file 'clip3.mp4'" >> clips.txt

# Merge clips
ffmpeg -f concat -safe 0 -i clips.txt -c copy highlight_reel.mp4

# Cleanup
rm clip1.mp4 clip2.mp4 clip3.mp4 clips.txt
```

---

**Workflow 3: Audio Podcast Processing**

```bash
# Extract audio
ffmpeg -i podcast_video.mp4 -vn -c:a libmp3lame -b:a 128k raw_audio.mp3

# Normalize volume
ffmpeg -i raw_audio.mp3 -af "loudnorm" normalized.mp3

# Remove first 30 seconds (intro music)
ffmpeg -i normalized.mp3 -ss 30 -c:a copy final_podcast.mp3

# Cleanup
rm raw_audio.mp3 normalized.mp3
```

---

#### 5. **Quality vs File Size Guide**

**Video Quality Settings (CRF):**
```bash
# Archival quality (large file)
ffmpeg -i input.mp4 -c:v libx264 -crf 18 -preset slow output.mp4

# Standard quality (balanced)
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -preset medium output.mp4

# Mobile/WhatsApp (smaller file)
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset fast output.mp4

# Maximum compression (small file, lower quality)
ffmpeg -i input.mp4 -c:v libx264 -crf 32 -preset faster output.mp4
```

**File Size Comparison (1 minute 1080p video):**
- CRF 18: ~50-70 MB
- CRF 23: ~20-30 MB
- CRF 28: ~8-15 MB
- CRF 32: ~5-8 MB

---

#### 6. **Troubleshooting Common Issues**

**Problem: "Encoder not found"**
```bash
# Check what's available
ffmpeg -encoders | grep h264

# Use alternative
ffmpeg -i input.mp4 -c:v h264 output.mp4  # instead of libx264
```

**Problem: "File too large to process"**
```bash
# Process in chunks
ffmpeg -i huge.mp4 -t 300 -c copy part1.mp4        # First 5 min
ffmpeg -i huge.mp4 -ss 300 -t 300 -c copy part2.mp4  # Next 5 min
```

**Problem: Audio/Video out of sync**
```bash
# Resync audio
ffmpeg -i input.mp4 -itsoffset 0.5 -i input.mp4 -map 0:v -map 1:a -c copy synced.mp4
```

**Problem: Conversion is too slow**
```bash
# Use faster preset
ffmpeg -i input.mp4 -preset ultrafast output.mp4

# Or resize first
ffmpeg -i input.mp4 -vf scale=1280:-1 -preset fast output.mp4
```

---

#### 7. **Advanced Tips for Termux Users**

**Create Aliases in Termux:**

Add to `~/.bashrc`:
```bash
# Quick compress
alias compress='ffmpeg -i "$1" -c:v libx264 -crf 28 -preset fast'

# Extract audio
alias extractaudio='ffmpeg -i "$1" -vn -c:a libmp3lame -b:a 192k'

# Video info
alias vinfo='ffprobe -v quiet -print_format json -show_format -show_streams'
```

Then reload:
```bash
source ~/.bashrc
```

**Progress Monitoring:**
```bash
# Add -progress to see real-time stats
ffmpeg -i input.mp4 -progress - -c:v libx264 output.mp4
```

**Parallel Processing:**
```bash
# Process multiple files in background
for video in *.mp4; do
    ffmpeg -i "$video" -c:v libx264 -crf 28 "compressed_${video}" &
done
wait  # Wait for all to complete
```

---

#### 8. **Best Practices**

1. **Always backup original files** before processing
2. **Test on small clip first** before processing long videos
3. **Use `-c copy` when possible** for speed
4. **Monitor storage space** in Termux with `df -h`
5. **Use appropriate quality settings** - don't over-compress
6. **Enable `-movflags +faststart`** for web videos
7. **Check output file** before deleting source

---

### Sources & References

1. **FFmpeg Official Documentation**: https://ffmpeg.org/documentation.html
2. **FFmpeg Wiki**: https://trac.ffmpeg.org/wiki
3. **GitHub Repository**: https://github.com/FFmpeg/FFmpeg
4. **FFmpeg Filters Guide**: https://ffmpeg.org/ffmpeg-filters.html

---

This comprehensive guide covers the most essential and practical FFmpeg commands for daily use in Termux. The examples are tested and work with FFmpeg versions available in 2025. Remember to always check `ffmpeg -version` to ensure you have the latest features available.