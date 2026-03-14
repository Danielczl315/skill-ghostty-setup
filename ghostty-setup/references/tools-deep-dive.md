# Tools Deep Dive

For power users who want to understand the full stack.

---

## zoxide

**What it is:** A smarter `cd` written in Rust. Tracks your directory visits with a frecency score (frequency × recency) and lets you jump anywhere with partial matches.

**How it works:**
- Every `cd` call is logged to `~/.local/share/zoxide/db.zo`
- Scores decay over time (recency matters more than old visits)
- `z foo` resolves to the highest-scoring directory containing "foo"

**Non-obvious features:**
```bash
z foo bar       # match path containing BOTH "foo" and "bar"
z foo/          # trailing slash = must be a subdirectory
zi              # interactive picker with fzf (!)
zoxide query -l # list all scored directories
```

**The killer combo:** `zi` opens fzf with all your visited directories, fuzzy-filterable. Fastest directory navigation possible.

---

## fzf

**What it is:** A general-purpose fuzzy finder. Takes any list on stdin, lets you filter it interactively, outputs the selection.

**How it works:** Reads lines, scores them with a fuzzy-match algorithm (Smith-Waterman variant), rerenders on each keystroke. Very fast because it's written in Go.

**Default keybindings (after install):**
| Keybinding | Action |
|------------|--------|
| `Ctrl+R` | Fuzzy search command history |
| `Ctrl+T` | Fuzzy search files in current dir |
| `Alt+C` | Fuzzy search and cd into directory |

**Non-obvious features:**
```bash
# Multi-select with Tab
fzf -m

# Preview window (game changer)
fzf --preview 'bat --color=always {}'

# Pipe anything into it
git branch | fzf | xargs git checkout

# Kill processes interactively
ps aux | fzf | awk '{print $2}' | xargs kill
```

**The fzf + zoxide integration:**
```bash
# Add to .zshrc for interactive directory jump with preview
function zf() {
  local dir
  dir=$(zoxide query -l | fzf --height 40% --reverse --preview 'eza --tree --level=1 --icons {}')
  [ -n "$dir" ] && cd "$dir"
}
```
Now `zf` gives you a fuzzy picker of all visited directories with a tree preview.

---

## eza

**What it is:** A modern rewrite of `ls` in Rust. Adds colors, icons, git status, tree view, and better defaults.

**Useful flags:**
```bash
eza -la                    # long list + hidden files
eza -la --icons            # with Nerd Font icons
eza --tree --level=2       # tree view, 2 levels deep
eza -la --git              # show git status per file
eza -la --sort=modified    # sort by modified time
eza -la --total-size       # show directory sizes
```

**Recommended aliases:**
```bash
alias ls='eza'
alias ll='eza -la --icons --git'
alias lt='eza --tree --level=2 --icons'
alias llt='eza -la --tree --level=2 --icons --git'
```

---

## bat

**What it is:** A `cat` clone with syntax highlighting, line numbers, git diff, and paging.

**How it works:** Uses the same syntax definitions as Sublime Text. Calls `less` for paging automatically.

**Useful flags:**
```bash
bat file.py                 # syntax highlighted
bat -n file.py              # line numbers only (no decoration)
bat -A file.py              # show non-printable characters
bat --diff file.py          # only show changed lines
bat -l json file.txt        # force language detection
```

**Themes:**
```bash
bat --list-themes           # see all available themes
bat --theme=TwoDark file.py # use specific theme
```

Add to `~/.zshrc` to set default theme:
```bash
export BAT_THEME="TwoDark"
```

**Integration with other tools:**
```bash
# fzf with bat preview
fzf --preview 'bat --color=always --line-range :50 {}'

# Use bat as man page viewer
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
```

---

## fastfetch

**What it is:** A system info tool that prints a pretty summary of your machine — OS, CPU, RAM, GPU, terminal, shell, uptime — with an ASCII logo. Successor to neofetch (which is unmaintained).

**Install:**
```bash
brew install fastfetch
```

**Basic usage:**
```bash
fastfetch
```

**The classic move — run it on terminal open:**
Add to the top of `~/.zshrc`:
```bash
fastfetch
```
Now every new terminal window greets you with your system info. Looks great in screenshots.

**Config file:** `~/.config/fastfetch/config.jsonc`

Generate a default config to customize:
```bash
fastfetch --gen-config
```

**Useful flags:**
```bash
fastfetch --logo small          # smaller ASCII art
fastfetch --logo none           # just the info, no logo
fastfetch -c neofetch           # neofetch-style layout
fastfetch --list-logos          # see all available logos
```

**Custom logo:**
```bash
fastfetch --file ~/mylogo.txt   # use a custom ASCII art file
```

---

## btop

**What it is:** A beautiful terminal resource monitor — CPU, memory, disk, network, and process list. Spiritual successor to htop, written in C++.

**Install:**
```bash
brew install btop
```

**Basic usage:**
```bash
btop
```

**Navigation:**
| Key | Action |
|-----|--------|
| `F2` or `o` | Options/settings |
| `F9` or `k` | Kill selected process |
| `/` | Filter processes |
| `q` | Quit |
| `m` | Cycle memory display |
| `e` | Toggle expanded CPU |

**Why it beats Activity Monitor:**
- Stays in your terminal — no context switch to GUI
- Shows per-core CPU usage as a graph
- Network bandwidth in real time
- Much faster to filter and kill processes

**Config file:** `~/.config/btop/btop.conf` — auto-generated on first run. You can change color themes, update intervals, and which panels are visible.

**Themes:**
```bash
# Inside btop, press F2 → Color theme
# Popular: dracula, gruvbox, nord, tokyo-night
```

---

## The Full Power Stack: Combined Workflows

### Workflow 1: Project Jump with Preview
```bash
# In .zshrc
function jp() {
  local dir
  dir=$(zoxide query -l | fzf \
    --height 60% \
    --reverse \
    --preview 'eza --tree --level=2 --icons --color=always {}')
  [ -n "$dir" ] && cd "$dir"
}
```

### Workflow 2: Interactive Git Branch Switch
```bash
function gbf() {
  git branch | fzf | tr -d '* ' | xargs git checkout
}
```

### Workflow 3: Fuzzy File Open in Editor
```bash
function vf() {
  local file
  file=$(fzf --preview 'bat --color=always {}')
  [ -n "$file" ] && ${EDITOR:-vim} "$file"
}
```

### Workflow 4: Kill Process Interactively
```bash
function kp() {
  local pid
  pid=$(ps aux | fzf | awk '{print $2}')
  [ -n "$pid" ] && kill -9 "$pid"
}
```
