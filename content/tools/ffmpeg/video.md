---
title: Video Files
tags:
  - script
---

```zsh
cropVideo() {
  # crops an video file

 ffmpeg -i $1 -ss "00:00:00.000" -to "00:00:$2.000" -codec:v libx264 -crf 23 -pix_fmt yuv420p -codec:a aac -f mp4 -movflags faststart $3
  # ffmpeg -i $1 -ss 00:00:00 -to 00:00:$2 -c copy -copyts $3
  # ffmpeg -ss 00:00:00 -t 00:00:$2 -i $1 -c:v copy -c:a copy $3
}

compressVideo() {
  # compresses a video
  # $1 input
  # $2 compress (less 0 -> 40 more compression)
  # $3 output

 ffmpeg -i $1 -an -vcodec libx264 -crf $2 $3
}

compressVideoQuick(){
# compresses a video. Only need input arg, will create output file name automatically
  # $1 input
  # $2 compress (less 0 -> 40 more compression), optional, default is 38

  # Extract base name without extension
  base_name=$(basename "$1" | sed 's/\(.*\)\..*/\1/')

  # Get current timestamp
  timestamp=$(date +%Y%m%d%H%M%S)

  # Form output filename
  output="${base_name}_${timestamp}.mp4"

  # Set default compression level if not provided
  crf=${2:-38}

  # Compress video
  ffmpeg -i "$1" -an -vcodec libx264 -crf "$crf" "$output"
}

compressQuickAudio(){
  # compresses a video and keeps audio
  # $1 input
  # $2 compress (less 0 -> 40 more compression), optional, default is 38

  # Extract base name without extension
  base_name=$(basename "$1" | sed 's/\(.*\)\..*/\1/')

  # Get current timestamp
  timestamp=$(date +%Y%m%d%H%M%S)

  # Form output filename
  output="${base_name}_${timestamp}.mp4"

  # Set default compression level if not provided
  crf=${2:-38}

  # Compress video with audio
  ffmpeg -i "$1" -c:v libx264 -crf "$crf" -c:a copy "$output"
}

```

I ran into the need to convert long videos produced by OBS while streaming into smaller clips. I create a folder and place 0.mkv (source) and 0.txt (timestamps) then run the script on that folder.

```bash
#!/bin/bash

# Usage: ./split_video.sh <folder>
# Folder requires children files:
# - 0.mkv (the video to be clipped from)
# - 0.txt (the list of timestamps)
#
# Example 0.txt
# 00:00:00 01:01:00 Gil
# 01:00:23 01:56:58 Denzo

if [ $# -ne 1 ]; then
    echo "Usage: $0 <folder>"
    exit 1
fi

folder="$1"

# Convert relative path to absolute path
if [[ ! "$folder" = /* ]]; then
    folder="$(pwd)/$folder"
fi

if [ ! -d "$folder" ]; then
    echo "Folder not found: $folder"
    exit 1
fi

input_video="$folder/0.mkv"
timestamps_file="$folder/0.txt"

if [ ! -f "$input_video" ]; then
    echo "Input video file not found: $input_video"
    exit 1
fi

if [ ! -f "$timestamps_file" ]; then
    echo "Timestamps file not found: $timestamps_file"
    exit 1
fi

while IFS=' ' read -r start end title; do
    if [ -z "$start" ] || [ -z "$end" ] || [ -z "$title" ]; then
        echo "Skipping invalid line: $start $end $title"
        continue
    fi

    output="$folder/${title}.mp4"

    if [ -f "$output" ]; then
        echo "Skipping $output (already exists)"
        continue
    fi

    echo "Creating clip: $output from $start to $end"

    ffmpeg -i "$input_video" -ss "$start" -to "$end" -c copy "$output"

    if [ $? -eq 0 ]; then
        echo "Successfully created $output"
    else
        echo "Failed to create $output"
    fi
done < "$timestamps_file"

echo "All clips processed."
```
