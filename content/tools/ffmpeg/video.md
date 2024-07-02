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
```