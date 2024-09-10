
Created by [hbu_m](https://www.reddit.com/r/ObsidianMD/comments/18yc9ee/quickly_adding_to_a_daily_note_in_terminal/)

Add notes to your daily note on obsidian (or any md file) through one line terminal commands.


![](daily_note_demo.mp4)


Add this to your `.bashrc` (or `.zshrc`) file to 
```
### created by hbu_m -https://www.reddit.com/r/ObsidianMD/comments/18yc9ee/quickly_adding_to_a_daily_note_in_terminal/

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
```