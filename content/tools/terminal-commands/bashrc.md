> [!info] For mac
>
> edit `~/.zshrc`

Basic setup for:

- `rc` to quickly access this alias file
- replace `vi` to run [[tools/lunarvim/index|index]] instead
- `oops` to reset terminal
- `p` because writing `pnpm` is the bane of many typos

```bash
alias rc='vi ~/.zshrc'
alias confignvim='cd ~/.config/nvim/lua/ && vi .'
alias oops='source ~/.zshrc'
alias vi="nvim"
alias p="pnpm"

alias ccmo="ccusage blocks --live"  # Real-time claude-code usage dashboard // https://github.com/ryoppippi/ccusage
alias claude-yolo="claude --dangerously-skip-permissions"


b() {
  if [ -f "bun.lock" ]; then
    echo "using bun:"
    bun "$@"
  elif [ -f "pnpm-lock.yaml" ]; then
    echo "using pnpm:"
    pnpm "$@"
  elif [ -f "yarn.lock" ]; then
    echo "using yarn:"
    yarn "$@"
  elif [ -f "package-lock.json" ]; then
    echo "using npm:"
    npm "$@"
  else
    echo "No package manager lock file found. Defaulting to npm."
    npm "$@"
  fi
}

# alias pip="python3.11 -m pip"
export PATH="$PATH:$HOME/.local/bin"


alias dc="docker compose"
alias gogo="go run main.go"
alias syncNotes='cd ~/git/quartz/ && npx quartz sync'


killports() {
    # -----------------------------------------------------------------
    # 1. If arguments were given → use them.
    # 2. If no arguments → use the default list.
    # -----------------------------------------------------------------
    local ports=("${@}")                     # copy the arguments
    (( ${#ports[@]} == 0 )) && ports=(3000 3001 3002)   # default fallback

    for port in "${ports[@]}"; do
        # Find PIDs, suppress errors (e.g. port not in use)
        local pids
        pids=$(lsof -ti :"$port" 2>/dev/null)

        if [[ -n $pids ]]; then
            echo "$pids" | xargs kill -9
            echo "Killed processes on port $port (PIDs: $pids)"
        else
            echo "No process listening on port $port"
        fi
    done
}

killapi() {
lsof -ti :8080 | xargs kill -9
}

speedramp() {
  # Speed ramps a video and saves the output with a timestamped filename
  input=$1
  timestamp=$(date +%Y%m%d%H%M%S)
  output="output_${timestamp}.mp4"
  ffmpeg -i "$input" -filter:v "setpts=0.0333*PTS" -an "$output"

  echo "Output saved as $output"
}
cropmp3() {
  # crops an audio file
  ffmpeg -ss 00:00:00 -t 00:00:$2 -i $1 -c:a copy $3
}
cropAtMp3() {
  # crops an audio file
  ffmpeg -ss 00:$2 -t 00:00:20 -i $1 -c:a copy $3
}
cropVideo() {
  # crops an video file
 ffmpeg -i $1 -ss "00:00:00.000" -to "00:00:$2.000" -codec:v libx264 -crf 23 -pix_fmt yuv420p -codec:a aac -f mp4 -movflags faststart $3
  # ffmpeg -i $1 -ss 00:00:00 -to 00:00:$2 -c copy -copyts $3
  # ffmpeg -ss 00:00:00 -t 00:00:$2 -i $1 -c:v copy -c:a copy $3
}
videoToWebp() {
    if [ -z "$1" ]; then
        echo "Usage: videoToWebp <input_video> [output_name]"
        return 1
    fi
    input="$1"
    # If no output name provided, use input name with .webp extension
    output="${2:-${input%.*}.webp}"
    ffmpeg -i "$input" \
        -vf "fps=15,scale=800:-1:flags=lanczos" \
        -vcodec libwebp \
        -lossless 0 \
        -compression_level 6 \
        -q:v 70 \
        -loop 0 \
        -preset picture \
        -an \
        -vsync 0 \
        "$output"
    echo "Converted $input to $output"
}
compressVideo() {
  # compresses a video
  # $1 input
  # $2 compress (less 0 -> 40 more compression)
  # $3 output
 ffmpeg -i $1 -an -vcodec libx264 -crf $2 $3
}
compressQuick(){
# compresses a video
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
alias cq='compressQuick'


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

compressWebm(){
# compresses a video
  # $1 input
  # $2 compress (less 0 -> 40 more compression), optional, default is 38
  # Extract base name without extension
  base_name=$(basename "$1" | sed 's/\(.*\)\..*/\1/')

  # Get current timestamp
  timestamp=$(date +%Y%m%d%H%M%S)

  # Form output filename
  output="${base_name}_${timestamp}.webm"

  # Set default compression level if not provided
  crf=${2:-38}

  # Compress video
  ffmpeg -i "$1" -c:v libvpx-vp9 -b:v 1M -c:a libopus "$output"
}

loopmp4() {
  # $1 video
  # $2 music
  # combines an audio and video file
 ffmpeg -y -stream_loop -1 -i $1 -i $2 -map 0:v -map 1:a -c:a aac -shortest -c:v copy $3
}
multiplyDuration(){
  # Gets the duration of a video file and multiplies the duration by 4
  ffprobe -v quiet -show_format -print_format json $1 | jq '.format.duration | tonumber * 4 | (. + 0.5) | floor'
}
makeAnimation(){
  # $1 video.mp4
  # $2 music.mp3
  # #3 thumbnail.png

  if [[ ! -d "edit" ]]; then
    mkdir edit
  fi
  DURATION=$(multiplyDuration $1)
  cropmp3 $2 $DURATION edit/cropped.mp3
  loopmp4 $1 edit/cropped.mp3 edit/animation.mp4
  if [[ ! -d "deploy" ]]; then
    mkdir deploy
  fi
  cp $2 deploy/music.mp3
  cp $3 deploy/thumbnail.png
  cropVideo edit/animation.mp4 20 deploy/animation.mp4
}
# alias for new obsidian daily note
function daily {
    TODAY=$(date "+%Y-%m-%d")
 # /Users/grins/Library/CloudStorage/GoogleDrive-masley.dean@gmail.com/My\ Drive/
# /Users/grins/Library/CloudStorage/GoogleDrive-masley.dean@gmail.com/My\ Drive/Personal\ Records/Misc/deanbook/DailyNotes/2024-08-01.md

FOLDER="$HOME/Library/CloudStorage/GoogleDrive-masley.dean@gmail.com/My Drive/Personal Records/Misc/deanbook/DailyNotes/"
NOTE_PATH="$HOME/Library/CloudStorage/GoogleDrive-masley.dean@gmail.com/My Drive/Personal Records/Misc/deanbook/DailyNotes/${TODAY}.md"
    # NOTE_PATH="$HOME/Library/CloudStorage/GoogleDrive-masley.dean@gmail.com/My\ Drive/Personal\ Records/Misc/deanbook/DailyNotes/${TODAY}.md"
    [ -f "$NOTE_PATH" ] || touch "$NOTE_PATH"
    COMMAND="$1"
    shift
    if [ "$COMMAND" = "add" ] || [ "$COMMAND" = "todo" ]; then
        TIME=$(date "+%H:%M")
        # Extract the last timestamp, if present
        LAST_TIME=$(grep '####' "$NOTE_PATH" | tail -1 | awk '{print $2}')
        if [ "$LAST_TIME" != "$TIME" ]; then
            [ -s "$NOTE_PATH" ] && echo "" >> "$NOTE_PATH"
            echo "#### $TIME" >> "$NOTE_PATH"
        fi
        ENTRY_TEXT="$*"
        if [ "$COMMAND" = "todo" ]; then
            echo "- [ ] $ENTRY_TEXT" >> "$NOTE_PATH"
        else
            echo "- $ENTRY_TEXT" >> "$NOTE_PATH"
        fi
    else
        cd $FOLDER;
        vi "$NOTE_PATH"
    fi
}
alias daily=daily
deployAr(){
  #1 thumbnail.png
  echo "THUMBNAIL" >> deploy.md
  thumbnail=$(arweave deploy/thumbnail.png)
  echo $thumbnail | tail -n 1 | (sed "s/[\[]3[0-9]m//g; s/^\[|\]//g" >> deploy.md)
  echo "\n" >> deploy.md
  echo "VIDEO" >> deploy.md
  video=$(arweave deploy/animation.mp4)
  echo $video | tail -n 1 | (sed "s/[\[]3[0-9]m//g; s/^\[|\]//g" >> deploy.md)
  echo "\n" >> deploy.md
  echo "MUSIC" >> deploy.md
  music=$(arweave deploy/music.mp3)
  echo $music | tail -n 1 | (sed "s/[\[]3[0-9]m//g; s/^\[|\]//g" >> deploy.md)
}
convertmp3() { ffmpeg -i $1 -acodec libmp3lame $2; }
# Convert Video and to 24 fps
convertvideo() { ffmpeg -i $1 -vcodec libx264 -crf 24 $2; }
squareVideo() { ffmpeg -i $1 -vf "crop=min(iw\,ih):min(iw\,ih),scale=640:640:force_original_aspect_ratio=decrease,pad=640:640:(ow-iw)/2:(oh-ih)/2" $2; }
# pnpm
export PNPM_HOME="/Users/grins/.local/share/pnpm"
case ":$PATH:" in
  *":$PNPM_HOME:"*) ;;
  *) export PATH="$PNPM_HOME:$PATH" ;;
esac
# pnpm end
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
# bun completions
[ -s "/Users/dean/.bun/_bun" ] && source "/Users/dean/.bun/_bun"
# bun
export BUN_INSTALL="$HOME/.bun"
export PATH="$BUN_INSTALL/bin:$PATH"
export PATH="/opt/homebrew/opt/libpq/bin:$PATH"
# android sdk
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
# Added by Windsurf
export PATH="/Users/dean/.codeium/windsurf/bin:$PATH"
export PATH="/opt/homebrew/opt/node@22/bin:$PATH"
# export PATH="$PATH:$HOME/nvim-macos-arm64/bin"

export PATH="/opt/homebrew/bin:$PATH"

#THIS MUST BE AT THE END OF THE FILE FOR SDKMAN TO WORK!!!
export SDKMAN_DIR="$HOME/.sdkman"
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"
eval "$(direnv hook zsh)"

source <(fzf --zsh)
```
