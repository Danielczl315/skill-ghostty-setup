---
name: ghostty-setup
description: >
  Guides users through setting up a modern developer terminal environment on macOS.
  Use this skill whenever a user asks to set up their terminal, improve their shell,
  install a better terminal emulator, configure zsh, set up a prompt, or asks about
  tools like Ghostty, Starship, zsh-autosuggestions, zoxide, fzf, eza, or bat.
  Also trigger when a user says "my terminal is boring/plain", "how do I make my terminal
  look better", "set up my dev environment", or "configure my shell". Works especially
  well as a Claude Code task — the model can run brew commands directly.
---

# Terminal Setup Skill

## Overview

This skill installs a modern, professional macOS terminal stack. **Always explain what will be installed and get the user's consent before running any commands.** This applies to all user types — beginners especially deserve to know what's happening on their machine.

**What gets installed:**
```
Ghostty            — new terminal app (replaces Terminal.app)
JetBrainsMono NF   — font with icons
Starship           — colorful smart prompt
zsh-autosuggestions — grey history suggestions as you type
zsh-syntax-highlighting — colors commands green/red as you type
```

**Power users also get:**
```
zoxide  — smart directory jumping
fzf     — fuzzy search for files and history
eza     — modern ls with icons and colors
bat     — modern cat with syntax highlighting
```

**Files that will be created or modified:**
- `~/.config/ghostty/config` — created (new file)
- `~/.config/starship.toml` — created (new file)
- `~/.zshrc` — **appended to** (existing content preserved)

---

## Security Notes

Share these with the user before starting, regardless of their experience level:

1. **Homebrew install script** — The command `curl | bash` downloads and runs a shell script directly from GitHub. This is the official Homebrew install method and is widely trusted in the macOS developer community, but users should know it's happening. Source: https://github.com/Homebrew/install
2. **All packages install via Homebrew** — Every tool in this setup (Ghostty, Starship, fonts, plugins) is installed through Homebrew, which verifies package checksums and uses its own sandboxed cellar at `/opt/homebrew`. Nothing installs directly to system directories.
3. **`~/.zshrc` is appended, never overwritten** — The setup uses `cat >>` (append), not `cat >` (overwrite). Existing shell config is preserved. Show the user the exact lines that will be added before writing them.
4. **No sudo required** — This entire setup runs without elevated privileges. If any step asks for a sudo password unexpectedly, stop and investigate.
5. **`eval "$(starship init zsh)"` and `eval "$(zoxide init zsh)"` run code at shell startup** — This is standard practice for shell tools that need to hook into zsh, but users should know these lines run every time they open a terminal.
6. **No network calls after install** — Once installed, none of these tools phone home or collect data. Starship, zoxide, fzf, eza, and bat are all offline CLI tools.

---

## Step 0: Assess the User

Before doing anything, ask ONE question:

> "Quick question — how comfortable are you with the terminal?
> - **A)** Not very — just want it to look good and work
> - **B)** I use it regularly but never customized it
> - **C)** Pretty comfortable — I want to understand every tool"

- **Option A** → [Beginner Path](#beginner-path)
- **Option B** → [Standard Path](#standard-path)
- **Option C** → [Power User Path](#power-user-path)

Infer from context if obvious (e.g. "I barely use the terminal" → A, comfortable with dotfiles/brew → B or C).

---

## Beginner Path

The user doesn't know what `.zshrc` is, may never have used brew, and might not know what a "shell" means. **Go slow. Define every term. Check in after each major step. Celebrate progress.**

### Pre-flight briefing

Before running anything, explain what's about to happen like you're talking to someone who's never done this before:

> "Before we start — here's exactly what I'm going to install, in plain English:
>
> - **Ghostty** — a new terminal app. Think of it as replacing the plain white box you open now. It looks way better and works faster.
> - **A font** — a special coding font that has tiny icons built in. Without it, some parts of the prompt would show as little squares.
> - **Starship** — this changes your prompt (the bit at the start of every line) to show you useful info like which folder you're in and what git branch you're on, all in color.
> - **Two plugins** — one makes commands appear in grey as suggestions while you type (like autocomplete). The other turns commands green when they're valid and red when you've mistyped something.
>
> I'll also need to add a few lines to a file called `~/.zshrc`. That's your shell's config file — it runs automatically every time you open a terminal. I'll show you exactly what I'm adding before I do it, and I'll only be adding to the end of it. Nothing existing gets changed.
>
> Everything installs through something called Homebrew — it's the standard way to install developer tools on a Mac, like an App Store but for the terminal. No admin/sudo password needed for any of this.
>
> Any questions before we start? If not, just say go and I'll walk you through it step by step."

Wait for confirmation. Answer any questions in plain language before proceeding.

---

### 1. Check / Install Homebrew

```bash
which brew
```

If not found:

> "Homebrew isn't on your machine yet — that's totally normal. It's a free tool made by the open source community that lets you install developer software safely. The installer lives on GitHub (that's where developers share code). Here's the command to install it:"

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> "This downloads the installer and runs it. It'll ask for your Mac password — that's expected just for this one step."

After it finishes:
> "Homebrew is installed! ✓ That's the hardest part done — everything else uses Homebrew so it'll be much simpler from here."

On Apple Silicon, if brew isn't found after install:
> "One more small step — run this line to activate Homebrew in your current session:"
```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

---

### 2. Install packages

> "Now I'll install the tools one by one. This might take a minute or two — Homebrew is downloading them from the internet."

```bash
brew install --cask ghostty
```
> "Ghostty installed ✓ — your new terminal app is now in your Applications folder."

```bash
brew install --cask font-jetbrains-mono-nerd-font
```
> "Font installed ✓ — this is what gives the prompt its icons."

```bash
brew install starship
```
> "Starship installed ✓ — this is the prompt engine."

```bash
brew install zsh-autosuggestions
brew install zsh-syntax-highlighting
```
> "Plugins installed ✓ — history suggestions and live syntax coloring."

---

### 3. Write Ghostty config

> "Now I'll create a config file for Ghostty. A config file is just a text file that tells an app how to behave — font size, colors, that kind of thing. I'm creating it at `~/.config/ghostty/config`. The `~` just means your home folder."

```bash
mkdir -p ~/.config/ghostty
cat > ~/.config/ghostty/config << 'EOF'
font-family = JetBrainsMono Nerd Font
font-size = 14
background-opacity = 0.95
window-padding-x = 12
window-padding-y = 10
scrollback-limit = 10000
macos-option-as-alt = true
theme = tokyonight
EOF
```
> "Config written ✓ — font, size, a little transparency, and the Tokyo Night color theme."

---

### 4. Apply Starship Tokyo Night preset

```bash
starship preset tokyo-night -o ~/.config/starship.toml
```
> "Starship configured ✓ — Tokyo Night preset applied."

---

### 5. Update ~/.zshrc

> "Almost done. I need to add a few lines to `~/.zshrc` — that's the file your shell reads every time it starts. Here's exactly what I'm adding:"
>
> ```
> # === Terminal Setup ===
> source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
> source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
> eval "$(starship init zsh)"
> ```
>
> "The two `source` lines load the plugins. The `eval` line starts Starship. Your existing `.zshrc` content stays exactly as it is — I'm only adding to the bottom of the file. OK to proceed?"

Wait for confirmation, then:

```bash
cat >> ~/.zshrc << 'EOF'

# === Terminal Setup ===
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
eval "$(starship init zsh)"
EOF
```
> "Done ✓ — `.zshrc` updated."

---

### 6. Apply changes

```bash
source ~/.zshrc
```
> "Changes applied ✓"

---

### 7. Summary

> "You're all set! 🎉 Here's what's different now:
>
> - **Open Ghostty** from your Applications folder or Spotlight (Cmd+Space → type Ghostty). That's your new terminal.
> - **Your prompt** now shows your current folder and git info in color
> - **Grey suggestions** appear as you type — press `→` (right arrow) to accept them
> - **Commands turn green** when valid, **red** when mistyped
>
> You now own three config files you can customize any time:
> - `~/.config/ghostty/config` — terminal appearance
> - `~/.config/starship.toml` — prompt appearance
> - `~/.zshrc` — shell behavior
>
> Want to see the keyboard shortcuts for splitting your terminal into panes?"

---

## Standard Path

The user knows their way around the terminal, uses brew regularly, just never got around to customizing their setup. **Skip basic definitions. Brief one-liners per tool. Run through fluidly and summarize at the end.**

### Pre-flight briefing

> "Here's what's getting installed — all via Homebrew, no sudo, `.zshrc` appended only:
>
> - **Ghostty** — GPU-accelerated terminal, replaces Terminal.app
> - **JetBrains Mono Nerd Font** — coding font with icon glyphs
> - **Starship** (Tokyo Night) — smart prompt with git/env context
> - **zsh-autosuggestions** — history suggestions, accept with `→`
> - **zsh-syntax-highlighting** — live green/red command validation
>
> Ready?"

Wait for confirmation, then run everything in one flow without pausing between steps.

---

### 1. Homebrew check
```bash
which brew || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

### 2. Install packages
```bash
brew install --cask ghostty
brew install --cask font-jetbrains-mono-nerd-font
brew install starship
brew install zsh-autosuggestions
brew install zsh-syntax-highlighting
```

---

### 3. Ghostty config
```bash
mkdir -p ~/.config/ghostty
cat > ~/.config/ghostty/config << 'EOF'
font-family = JetBrainsMono Nerd Font
font-size = 14
background-opacity = 0.95
window-padding-x = 12
window-padding-y = 10
scrollback-limit = 10000
macos-option-as-alt = true
theme = tokyonight
EOF
```

---

### 4. Starship preset
```bash
starship preset tokyo-night -o ~/.config/starship.toml
```

---

### 5. Update ~/.zshrc

Show the block before writing:
```
# === Terminal Setup ===
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
eval "$(starship init zsh)"
```

```bash
cat >> ~/.zshrc << 'EOF'

# === Terminal Setup ===
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
eval "$(starship init zsh)"
EOF
```

---

### 6. Apply & done
```bash
source ~/.zshrc
```

> "All done. Open Ghostty from Applications — prompt is live, plugins are active.
>
> Config files:
> - `~/.config/ghostty/config`
> - `~/.config/starship.toml`
> - `~/.zshrc`
>
> Want the Ghostty pane shortcuts or anything else?"

---

## Power User Path

Full install including productivity CLI tools. Walk through each step with explanation.

### Pre-flight

Give the full security briefing from the [Security Notes](#security-notes) section above. Power users will want to know the mechanics.

### 1. Homebrew
```bash
which brew || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
Official installer from https://github.com/Homebrew/install — uses curl to fetch and execute. All packages go to `/opt/homebrew` on Apple Silicon, `/usr/local` on Intel.

### 2. Ghostty
```bash
brew install --cask ghostty
```
GPU-accelerated terminal written in Zig. Faster rendering than iTerm2, native macOS tabs/splits, plain-text config.
```bash
mkdir -p ~/.config/ghostty
cat > ~/.config/ghostty/config << 'EOF'
font-family = JetBrainsMono Nerd Font
font-size = 14
background-opacity = 0.95
window-padding-x = 12
window-padding-y = 10
scrollback-limit = 10000
macos-option-as-alt = true
theme = tokyonight
EOF
```
→ See [references/ghostty-deep-dive.md] for all config options, theme list, GPU rendering details.

### 3. Font
```bash
brew install --cask font-jetbrains-mono-nerd-font
```
Patches ~3000 icons into JetBrains Mono via Nerd Fonts. Required for Starship glyphs and eza icons to render correctly.

### 4. Starship
```bash
brew install starship
starship preset tokyo-night -o ~/.config/starship.toml
```
Rust-based prompt, reads project context (git, node, python, rust, etc.) in <1ms.
→ See [references/starship-customization.md] for custom modules, conditional display, performance tuning.

### 5. Autocomplete & Syntax Highlighting
```bash
brew install zsh-autosuggestions zsh-syntax-highlighting
```
- `zsh-autosuggestions` — frecency-based history suggestions, accept with `→`
- `zsh-syntax-highlighting` — hooks into ZLE, rerenders on each keystroke. **Must be sourced last in `.zshrc`**
→ See [references/zsh-plugins-deep-dive.md] for suggestion strategies, highlight colors, ordering rules.

### 6. Productivity CLI Tools
```bash
brew install zoxide fzf eza bat fastfetch btop
```

| Tool | Replaces | What it does |
|------|----------|--------------|
| `zoxide` | `cd` | Frecency-scored directory jumps — `z project` |
| `fzf` | Ctrl+R | Fuzzy finder for history, files, any stdin |
| `eza` | `ls` | Icons, colors, git status, tree view |
| `bat` | `cat` | Syntax highlighting, line numbers, git diff |
| `fastfetch` | neofetch | System info splash (CPU, RAM, OS) on terminal open |
| `btop` | Activity Monitor | Beautiful real-time resource monitor in the terminal |

→ See [references/tools-deep-dive.md] for fzf+zoxide combos, fastfetch config, btop navigation, and advanced flags.

### 7. Write ~/.zshrc

Show the user the full block before writing:

```bash
cat >> ~/.zshrc << 'EOF'

# === Terminal Setup ===
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
eval "$(zoxide init zsh)"
eval "$(starship init zsh)"

# fzf — fuzzy history search (Ctrl+R) and file search (Ctrl+T)
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

# eza — modern ls
alias ls='eza'
alias ll='eza -la --icons --git'
alias lt='eza --tree --level=2 --icons'

# bat — modern cat
alias cat='bat'

# fastfetch — system info on terminal open
fastfetch
EOF
```

Run fzf keybinding installer (writes `~/.fzf.zsh`):
```bash
$(brew --prefix)/opt/fzf/install --key-bindings --completion --no-update-rc
```

### 8. Apply & Verify
```bash
source ~/.zshrc
starship --version && zoxide --version && fzf --version && eza --version && bat --version && fastfetch --version && btop --version
```

---

## Ghostty Navigation (all users)

Share after setup:

| Action | Shortcut |
|--------|----------|
| Split pane vertical | `Cmd + D` |
| Split pane horizontal | `Cmd + Shift + D` |
| Move between panes | `Cmd + Option + Arrow` |
| Close pane | `Cmd + W` |
| New tab | `Cmd + T` |

---

## Troubleshooting

**Icons show as squares/question marks:**
→ Font not applied. Confirm `font-family = JetBrainsMono Nerd Font` in Ghostty config, restart Ghostty.

**Prompt looks wrong / no color:**
→ Starship must be initialized after plugin sources in `.zshrc`. Check ordering.

**Suggestions not appearing:**
→ Run `source ~/.zshrc`. Confirm the autosuggestions source line is present.

**`bat`/`ll` not found:**
→ Aliases only apply in new shells. Run `source ~/.zshrc` or open a new Ghostty tab.

**Brew not found after install on Apple Silicon:**
→ Run `eval "$(/opt/homebrew/bin/brew shellenv)"` and add it to the top of `~/.zshrc`.
