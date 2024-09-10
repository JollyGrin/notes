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

```