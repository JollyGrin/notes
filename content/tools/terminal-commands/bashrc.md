
> [!info] For mac
>
> edit `~/.zshrc`

Basic setup for:
- `rc` to quickly access this alias file
- replace `vi` to run [[tools/lunarvim/index|index]] instead
- `oops` to reset terminal
- `p` because writing `pnpm` is the bane of many typos

```
alias rc='vi ~/.zshrc'
alias oops='source ~/.zshrc'
alias vi="lvim"
alias p="pnpm"
export PATH="$PATH:$HOME/.local/bin"

alias airdrop='cd ~/git/innkeeper/innkeeper-airdrop/ && pnpm start'

alias arweave='arkb --auto-confirm --wallet ~/git/innkeeper/decrypted.json deploy'

alias cardsarmy='cd ~/git/sorcerytcg/playtest/ && vi .'
alias runepunk='cd ~/git/spacemangg/lots/ && vi .'
alias monacum='cd ~/git/manocum/ && vi .'
alias teamplay='cd ~/git/spacemangg/teamplay-demo-frontend/ && vi .'

alias syncNotes='cd ~/git/quartz/ && npx quartz sync'

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
    NOTE_PATH="$HOME/Insync/masley.dean@gmail.com/Google Drive/Personal Records/Misc/deanbook/DailyNotes/${TODAY}.md"
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
        nvim "$NOTE_PATH"
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
export PNPM_HOME="/Users/dean/Library/pnpm"
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

```