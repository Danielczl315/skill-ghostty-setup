# skill-ghostty-setup

A Claude Code skill that sets up a modern macOS terminal environment in ~2 minutes.

Installs and configures [Ghostty](https://ghostty.org) with a full stack of shell tools — beginner friendly, no sudo, nothing gets overwritten.

---

## what gets installed

| Tool | What it does |
|------|-------------|
| [Ghostty](https://ghostty.org) | GPU-accelerated terminal app |
| [JetBrains Mono Nerd Font](https://www.nerdfonts.com) | Coding font with icons |
| [Starship](https://starship.rs) | Smart colorful prompt (Tokyo Night preset) |
| [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) | History suggestions as you type |
| [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) | Live green/red command validation |

**Power users also get:**

| Tool | What it does |
|------|-------------|
| [zoxide](https://github.com/ajeetdsouza/zoxide) | Smarter `cd` with frecency scoring |
| [fzf](https://github.com/junegunn/fzf) | Fuzzy finder for history and files |
| [eza](https://github.com/eza-community/eza) | Modern `ls` with icons and git status |
| [bat](https://github.com/sharkdp/bat) | Better `cat` with syntax highlighting |
| [fastfetch](https://github.com/fastfetch-cli/fastfetch) | System info on terminal open |
| [btop](https://github.com/aristocratos/btop) | Beautiful resource monitor |

**Files created or modified:**
- `~/.config/ghostty/config` — created
- `~/.config/starship.toml` — created
- `~/.zshrc` — appended to (existing content preserved)

---

## requirements

- macOS
- [Claude Code](https://claude.ai/code)
- [Homebrew](https://brew.sh) (the skill will install it if missing)

---

## install

Download `ghostty-setup-skill.skill` from the [latest release](../../releases/latest) and install it in Claude Code.

Then in Claude Code, just say:

```
set up my terminal
```

or

```
install ghostty
```

Claude will ask one question about your experience level and handle the rest.

---

## experience levels

**A — Total beginner**
Claude explains every term, installs one step at a time with confirmation, defines what `.zshrc` is, celebrates each step. Full hand-holding.

**B — Regular user**
Claude gives a quick overview, waits for one "ready?", then runs everything in one flow. Brief summary at the end.

**C — Power user**
Full explanations of how each tool works internally, all CLI tools included, deep dives available on request.

---

## security

- All packages install via **Homebrew** — checksums verified, sandboxed to `/opt/homebrew`
- **No sudo required** at any point. If prompted unexpectedly, stop.
- `~/.zshrc` is **appended** (`>>`), never overwritten (`>`)
- Claude shows you the exact lines before writing to any config file
- None of these tools phone home or collect data after install
- Homebrew install script source: https://github.com/Homebrew/install

---

## repo structure

```
ghostty-setup/
├── README.md
├── ghostty-setup-skill.skill     ← install this in Claude Code
└── ghostty-setup-skill/
    ├── SKILL.md                  ← main skill instructions
    └── references/
        ├── ghostty-deep-dive.md        ← full Ghostty config reference
        ├── starship-customization.md   ← custom modules, presets
        ├── zsh-plugins-deep-dive.md    ← plugin config, .zshrc ordering
        └── tools-deep-dive.md          ← zoxide, fzf, eza, bat, fastfetch, btop
```

---

## license

MIT
