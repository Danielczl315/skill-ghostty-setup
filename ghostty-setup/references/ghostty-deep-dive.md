# Ghostty Deep Dive

## What makes Ghostty different

Ghostty is a GPU-accelerated terminal emulator written in Zig. Unlike Alacritty (also GPU) or iTerm2 (CPU), it aims for native macOS integration while keeping rendering performance at the GPU level.

Key differences vs iTerm2:
- Much faster rendering (GPU vs CPU compositing)
- Simpler config format (no GUI settings panel)
- Written in Zig — small binary, fast startup
- Native macOS tabs and pane splitting

---

## Full config reference

`~/.config/ghostty/config`

```
# Font
font-family = JetBrainsMono Nerd Font
font-size = 14
font-thicken = true              # slightly bolder, better on retina

# Window
background-opacity = 0.95
window-padding-x = 12
window-padding-y = 10
window-decoration = false        # removes titlebar (minimal look)

# Scrollback
scrollback-limit = 100000        # lines (increase for log-heavy work)

# Theme
theme = tokyonight               # see available themes below

# macOS
macos-option-as-alt = true       # lets Alt key work in vim, etc.
macos-titlebar-style = hidden    # cleaner look
quit-after-last-window-closed = true

# Cursor
cursor-style = bar               # bar | block | underline
cursor-style-blink = false

# Bell
bell-feature = false             # disable audible bell
```

---

## Available themes

Ghostty includes many built-in themes. Preview them:
```bash
ghostty +list-themes
```

Popular picks:
- `tokyonight` — blue/purple, very popular for coding
- `catppuccin-mocha` — warm purple tones
- `nord` — cool blues
- `gruvbox-dark` — warm earthy tones
- `one-dark-pro` — Atom-style

---

## Pane splitting & navigation

| Action | Shortcut |
|--------|----------|
| Split right | `Cmd + D` |
| Split down | `Cmd + Shift + D` |
| Focus pane left | `Cmd + Option + ←` |
| Focus pane right | `Cmd + Option + →` |
| Focus pane up | `Cmd + Option + ↑` |
| Focus pane down | `Cmd + Option + ↓` |
| Close pane | `Cmd + W` |
| New tab | `Cmd + T` |
| Next tab | `Cmd + Shift + ]` |
| Prev tab | `Cmd + Shift + [` |

---

## Custom keybindings

```
# In ~/.config/ghostty/config
keybind = cmd+k=clear_screen
keybind = ctrl+shift+t=new_tab
```

---

## Performance tip

For log-heavy work (watching Docker logs, kubectl logs, etc.), increase scrollback:
```
scrollback-limit = 1000000
```

Ghostty handles this efficiently because it's GPU-rendered — iTerm2 would get sluggish at this scale.
