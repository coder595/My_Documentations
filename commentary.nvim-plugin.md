# 📝 Neovim Commenting Guide with `vim-commentary`

This guide explains how to use **[tpope/vim-commentary](https://github.com/tpope/vim-commentary)** for commenting code in Neovim.  
It uses the same logic as other Vim operators (`d` delete, `y` yank):  
- `gc` is the **operator** (comment)  
- `{motion}` is **what** you apply it to  

---

## 🔑 Basic Keybindings

| Command         | Action                                                      |
|-----------------|-------------------------------------------------------------|
| `gcc`           | Toggle comment on the **current line**                      |
| `3gcc`          | Toggle comment on the **current + next 2 lines**            |
| `gc{motion}`    | Comment/uncomment the text covered by the motion            |
| Visual + `gc`   | Toggle comment on the visually selected region              |
| `gb{motion}`    | Blockwise comment (if supported by `commentstring`)         |

---

## 📌 Motions Explained

A *motion* tells Vim how much text to act on. Examples:

| Motion | Meaning                              | Example Use            |
|--------|--------------------------------------|------------------------|
| `w`    | Word                                 | `gcw` → comment a word |
| `aw`   | A word (with space)                  | `gcaw`                 |
| `iw`   | Inner word                           | `gciw`                 |
| `$`    | To end of line                       | `gc$`                  |
| `0`    | To start of line                     | `gc0`                  |
| `}`    | To end of paragraph                  | `gc}`                  |
| `{`    | To beginning of paragraph            | `gc{`                  |
| `ap`   | A paragraph                          | `gcap`                 |
| `ip`   | Inner paragraph                      | `gcip`                 |
| `%`    | Matching bracket/brace/parenthesis   | `gc%`                  |
| `G`    | To end of file                       | `gcG`                  |
| `j`    | Down one line                        | `gcj`                  |
| `k`    | Up one line                          | `gck`                  |

---

## 🎯 Examples

### Comment current line
```vim
gcc

