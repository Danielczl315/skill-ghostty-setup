# Zsh Plugins Deep Dive

## zsh-autosuggestions

**How it works:** Listens to every keypress and searches your zsh history for the most recent command that starts with what you've typed so far. Renders the suggestion in a dim color ahead of your cursor.

**Accept options:**
- `→` (right arrow) — accept full suggestion
- `Ctrl+F` — same as right arrow
- `Alt+F` — accept next word only
- `End` — accept to end of line

**Configuration options (add to .zshrc before source line):**

```bash
# Change suggestion color (default is dim white)
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=#888888"

# Suggestion strategy (default: history)
# 'completion' uses tab-completion engine instead of history
# 'match_prev_cmd' matches previous command's context
ZSH_AUTOSUGGEST_STRATEGY=(history completion)

# Max size of history to search (default: unlimited)
ZSH_AUTOSUGGEST_BUFFER_MAX_SIZE=20

# Async mode (faster, default in newer versions)
ZSH_AUTOSUGGEST_USE_ASYNC=1
```

---

## zsh-syntax-highlighting

**How it works:** Hooks into zsh's line editor (ZLE) and rerenders the command with colors on each keystroke. Uses a parsing engine that understands shell syntax.

**Color scheme:**
- Valid command → green
- Invalid/unknown command → red
- Flags/options (`-la`, `--verbose`) → yellow  
- Strings (`"hello"`) → yellow
- Variables (`$HOME`) → cyan
- Pipes, redirects → bold
- Comments (`# this`) → grey

**Customization (add before source line):**
```bash
# Customize specific highlighters
ZSH_HIGHLIGHT_STYLES[command]='fg=green,bold'
ZSH_HIGHLIGHT_STYLES[unknown-token]='fg=red,bold'
ZSH_HIGHLIGHT_STYLES[path]='fg=cyan,underline'
ZSH_HIGHLIGHT_STYLES[string]='fg=yellow'
```

**Available highlighters:**
```bash
ZSH_HIGHLIGHT_HIGHLIGHTERS=(main brackets pattern cursor)
```
- `main` — commands, args, strings (default)
- `brackets` — matching bracket pairs
- `pattern` — custom regex patterns
- `cursor` — cursor position

---

## .zshrc order matters

Plugin sources must be in the right order:

```bash
# 1. zoxide init
eval "$(zoxide init zsh)"

# 2. starship init  
eval "$(starship init zsh)"

# 3. fzf
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

# 4. autosuggestions BEFORE syntax-highlighting
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh

# 5. syntax-highlighting MUST be last
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

If syntax highlighting is not last, it can interfere with other plugins.

---

## Other useful zsh options

```bash
# Better history
HISTSIZE=100000
SAVEHIST=100000
HISTFILE=~/.zsh_history
setopt SHARE_HISTORY          # share history across sessions
setopt HIST_IGNORE_DUPS       # don't record duplicates
setopt HIST_IGNORE_SPACE      # don't record lines starting with space

# Better globbing
setopt EXTENDED_GLOB

# cd without typing cd
setopt AUTO_CD

# Autocorrect
setopt CORRECT
```
