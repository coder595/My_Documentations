# Tmux Key Bindings Reference

> **Leader Key:** `Ctrl+s`

## 🎯 Session Management

| Command | Description |
|---------|-------------|
| `tmux new -s <name>` | Create new named session |
| `tmux ls` | List all sessions |
| `tmux attach -t <name>` | Attach to session |
| `tmux kill-session -t <name>` | Kill specific session |
| `Ctrl+s d` | Detach from current session |
| `Ctrl+s s` | List and switch between sessions |
| `Ctrl+s $` | Rename current session |
| `Ctrl+s (` | Switch to previous session |
| `Ctrl+s )` | Switch to next session |

## 🪟 Window Management

| Command | Description |
|---------|-------------|
| `Ctrl+s c` | Create new window |
| `Ctrl+s ,` | Rename current window |
| `Ctrl+s &` | Kill current window |
| `Ctrl+s n` | Go to next window |
| `Ctrl+s p` | Go to previous window |
| `Ctrl+s l` | Go to last used window |
| `Ctrl+s 0-9` | Go to window number |
| `Ctrl+s w` | List all windows |
| `Ctrl+s f` | Find window by name |

## 📐 Pane Management

### Creating Panes

| Command | Description |
|---------|-------------|
| `Ctrl+s %` | Split window vertically (left/right) |
| `Ctrl+s "` | Split window horizontally (top/bottom) |

### Navigating Panes

| Command | Description |
|---------|-------------|
| `Ctrl+s o` | Cycle through panes |
| `Ctrl+s ;` | Go to last active pane |
| `Ctrl+s ←/↓/↑/→` | Move between panes (arrow keys) |
| `Ctrl+s h/j/k/l` | Move between panes (vim-style) |
| `Ctrl+s q` | Show pane numbers (then press number to select) |

### Resizing Panes

| Command | Description |
|---------|-------------|
| `Ctrl+s Ctrl+←/↓/↑/→` | Resize pane (hold Ctrl with arrow keys) |
| `Ctrl+s Alt+←/↓/↑/→` | Resize pane in larger increments |

### Pane Operations

| Command | Description |
|---------|-------------|
| `Ctrl+s x` | Kill current pane |
| `Ctrl+s z` | Toggle pane zoom (full screen) |
| `Ctrl+s !` | Break pane into new window |
| `Ctrl+s {` | Move current pane left |
| `Ctrl+s }` | Move current pane right |
| `Ctrl+s Ctrl+o` | Rotate panes clockwise |
| `Ctrl+s Alt+o` | Rotate panes counter-clockwise |
| `Ctrl+s Space` | Cycle through pane layouts |

## 📋 Copy Mode (Scrolling & Text Selection)

| Command | Description |
|---------|-------------|
| `Ctrl+s [` | Enter copy mode |
| `q` | Exit copy mode (while in copy mode) |
| `←/↓/↑/→` | Navigate (while in copy mode) |
| `Ctrl+u/Ctrl+d` | Page up/down (while in copy mode) |
| `g/G` | Go to top/bottom (while in copy mode) |
| `Space` | Start selection (while in copy mode) |
| `Enter` | Copy selection (while in copy mode) |
| `Ctrl+s ]` | Paste copied text |

## 🔧 Miscellaneous

| Command | Description |
|---------|-------------|
| `Ctrl+s ?` | Show all key bindings |
| `Ctrl+s :` | Enter command mode |
| `Ctrl+s t` | Show clock |
| `Ctrl+s r` | Reload tmux config (if configured) |
| `Ctrl+s Ctrl+s` | Send literal Ctrl+s to application |

## ⌨️ Command Mode Examples

After pressing `Ctrl+s :`, you can type:

```bash
new-window -n <name>              # Create window with specific name
rename-window <name>              # Rename current window
swap-window -s <src> -t <dst>     # Swap windows
move-window -t <position>         # Move window to position
split-window -h                   # Split horizontally
split-window -v                   # Split vertically
list-keys                         # List all key bindings
source-file ~/.tmux.conf          # Reload config file
```

## 💻 Useful Command Line Options

```bash
# Create detached session
tmux new-session -d -s <name>

# Send command to session
tmux send-keys -t <session> '<command>' Enter

# Capture pane content
tmux capture-pane -t <session> -p

# List windows in session
tmux list-windows -t <session>

# List panes in session
tmux list-panes -t <session>
```

## 📝 Notes

- Replace `Ctrl+s` with your actual prefix key combination
- Many commands can be customized in `~/.tmux.conf`
- Use `man tmux` for complete documentation
- Session names and window names are case-sensitive

---

*Reference optimized for terminal viewing with `glow` and FiraCode font*