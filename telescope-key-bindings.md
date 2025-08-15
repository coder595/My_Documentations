# 🔭 Telescope Keybindings for Neovim

**Plugin:** [`nvim-telescope/telescope.nvim`](https://github.com/nvim-telescope/telescope.nvim)  
**Leader Key:** `<Space>`  
**Telescope Version:** `0.1.8`

---

## 📁 Normal Mode Keybindings (Global)

These key mappings are defined in your `init.lua` and are available in **normal mode**.

| Keybinding      | Function                  | Description                         |
|------------------|----------------------------|-------------------------------------|
| `<Space>ff`     | `builtin.find_files`       | Find files in the current directory |
| `<Space>fg`     | `builtin.live_grep`        | Live text search using ripgrep      |
| `<Space>fb`     | `builtin.buffers`          | List open buffers                   |
| `<Space>fh`     | `builtin.help_tags`        | Search help tags in Neovim docs     |

---

## 🧭 Insert Mode Keybindings (Inside Telescope Prompt)

These keybindings work **inside Telescope’s interactive prompt** (e.g. after launching `<Space>ff`).

| Keybinding | Action                    | Description                     |
|------------|---------------------------|---------------------------------|
| `<C-k>`    | `move_selection_previous` | Move selection **up**           |
| `<C-j>`    | `move_selection_next`     | Move selection **down**         |
| `<C-q>`    | `close`                   | **Close** Telescope prompt      |

---

## 🎨 UI Configuration Summary

These are general settings affecting Telescope's appearance and behavior.

- **Prompt Prefix:** `󰍉`
- **Selection Caret:** ``
- **Entry Prefix:** *(single space)*
- **Path Display Mode:** `smart`
- **Sorting Strategy:** `ascending`
- **Layout Config:**
  - Prompt Position: `top`
  - Width: `0.9`
  - Height: `0.85`

---
