# Starship Customization

## How Starship works

Starship reads your environment on each prompt render and builds a modular prompt. Each "module" is a segment (git status, directory, language version, etc.). Modules are:
- Only shown when relevant (python module only shows in Python projects)
- Cached for performance
- Configurable independently

Written in Rust — renders in <1ms typically.

---

## Available presets

```bash
starship preset --list
```

Popular ones:
- `tokyo-night` — what you're using
- `gruvbox-rainbow`
- `pastel-powerline`
- `nerd-font-symbols`
- `bracketed-segments`

Switch preset:
```bash
starship preset tokyo-night -o ~/.config/starship.toml
```

---

## Customizing modules

`~/.config/starship.toml` controls everything.

### Directory module
```toml
[directory]
truncation_length = 3       # how many path segments to show
truncate_to_repo = true     # truncate to git root
style = "bold cyan"
```

### Git modules
```toml
[git_branch]
symbol = " "
style = "bold purple"

[git_status]
# Show counts instead of just symbols
ahead = "⇡${count}"
behind = "⇣${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
modified = "!${count}"
untracked = "?${count}"
staged = "+${count}"
```

### Language versions (only show when in project)
```toml
[nodejs]
symbol = " "
detect_files = ["package.json", ".node-version"]

[python]
symbol = " "
detect_files = ["requirements.txt", "pyproject.toml", ".python-version"]

[rust]
symbol = " "
```

### Command duration (show slow commands)
```toml
[cmd_duration]
min_time = 2_000       # show if command took >2s
format = "took [$duration]($style) "
style = "yellow"
```

---

## Custom modules

You can add any shell command as a module:

```toml
[custom.kubectl_context]
command = "kubectl config current-context"
when = "kubectl config current-context"
symbol = "☸ "
style = "bold blue"
format = "[$symbol$output]($style) "
```

---

## Performance tuning

If prompt feels slow:
```toml
[git_status]
disabled = true         # git status check can be slow on large repos
```

Or use `command_timeout`:
```toml
command_timeout = 500   # ms before giving up on a module
```
