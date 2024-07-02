
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

# ... alias functions

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