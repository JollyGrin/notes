---
title: Audio Files
tags:
  - script
---

```zsh
cropMp3() {
  # crops an audio file
  # $1 input file
  # $2 end time (within 60 seconds)
  # $3 output file
  ffmpeg -ss 00:00:00 -t 00:00:$2 -i $1 -c:a copy $3
}
```

