# ncdu Disk Usage Guide

This guide summarizes the official ncdu man page, upstream documentation from [https://dev.yorhel.nl/ncdu](https://dev.yorhel.nl/ncdu), and typical best practices used on modern Linux systems. It assumes a minimal Arch Linux environment with DWM, but the commands apply to most Unix-like hosts.

## Installation & Version Check
- **Arch Linux / Manjaro**  
  ```bash
  sudo pacman -S ncdu
  ```
- **Debian / Ubuntu**  
  ```bash
  sudo apt install ncdu
  ```
- **RHEL / Fedora**  
  ```bash
  sudo dnf install ncdu
  ```
Verify the installed release and build options:
```bash
ncdu --version
```
The version string lists enabled features (UTF-8, LZO support, etc.).

## Basic Usage
Scan the current directory and enter the TUI:
```bash
ncdu
```
Scan a specific path:
```bash
ncdu /home/muhammad/Github
```
By default ncdu traverses the directory tree, stores results in memory, and shows the interactive interface once the scan finishes.

## Navigation & Key Bindings (TUI)
- `↑` / `↓` or `k` / `j`: move selection.
- `↵` or `→`: enter directory.
- `←` or `u`: go up one level.
- `n`: sort by name (toggle ascending/descending).
- `s`: sort by size (default).
- `d`: delete selected file/directory (prompts for confirmation).
- `a`: toggle between apparent size and disk usage.
- `g`: quick search by typing a substring.
- `?`: help screen with all key bindings.
- `q`: quit (or `Ctrl+C`).

## Important Command-Line Arguments

| Flag | Description | When to Use |
|------|-------------|-------------|
| `-q, --quiet` | Reduce output while scanning; suppress progress. | Running ncdu in scripts for logging. |
| `-x, --one-file-system` | Stay on one filesystem (no crossing mount points). | Avoid scanning mounted backups or network shares. |
| `-X <pattern>` | Exclude paths (shell-style glob). | Skip caches (`-X '**/.cache/**'`). |
| `-d <depth>` | Limit directory depth (0 shows only total). | Quick high-level overview. |
| `--si` | Display sizes using powers of 1000 (KB = 1000). | Match storage vendor units. |
| `-0, --null` | Output names separated by NUL in export mode. | Safe scripting with special characters. |
| `-o <file>` | Export scan results to file (JSON-like format). | For reuse with `-f`. |
| `-f <file>` | Load previous scan from file. | Inspect historical snapshots offline. |
| `--exclude-from <file>` | Read exclusion patterns from file. | Maintain reusable ignore list (`~/.config/ncdu/excludes`). |
| `--confirm-quit` | Require confirmation before exit. | Prevent accidental closures during audits. |
| `--delete-after <size>` | Delete files during scan after they are counted. | Emergency cleanup on near-full disks. |

Consult `man ncdu` for the full option list, including `--follow-symlinks`, `--exclude-caches`, and `--disable-delete`.

## Working with Export/Import
1. **Create a database file**  
   ```bash
   ncdu -o /tmp/ncdu-root.db /
   ```
2. **Review later without rescanning**  
   ```bash
   ncdu -f /tmp/ncdu-root.db
   ```
3. **Parse programmatically**  
   The export format is a simple newline-delimited tree with size and path tokens. Use `ncdu -o- /path | gzip > report.ncdu.gz` to store compressed snapshots.

## Remote & Large-Scale Workflows
- **SSH + TUI**  
  ```bash
  ssh user@server "ncdu -o- /var" | ncdu -f-
  ```
  Streams the remote scan to your local viewer (requires ncdu on both ends).
- **When disk is almost full**  
  Run with `--exclude-from` to avoid known large directories, and consider `--delete-after` carefully to reclaim space during the scan.
- **Cron-based reports**  
  ```bash
  ncdu -0xo- /data | gzip > /var/reports/ncdu-$(date +%F).db.gz
  ```
  Later, inspect with `zcat file.db.gz | ncdu -f-`.

## Best Practices
- Run scans with sufficient privileges (`sudo`) to avoid permission-denied gaps, especially on `/var`, `/srv`, or root filesystems.
- Use `--one-file-system` on multi-mount setups to prevent unexpected traversals (e.g., network shares, removable drives).
- Maintain an exclusion file (`~/.config/ncdu/excludes`) for directories like `node_modules`, build caches, or VM images to speed up daily checks.
- Enable deletion (`d` key) only after double-checking the path; ncdu deletes immediately using `unlink(2)` and does not send files to trash.
- Combine ncdu with traditional tools (`du`, `df`, `find`) when verifying free space reclaimed, since files held open by processes may keep disk usage high until the process exits.

## Quick Recipes & Common Flags
- **Inspect home directory, stay on same filesystem**  
  ```bash
  ncdu -x ~
  ```
- **Scan root with elevated privileges, excluding caches**  
  ```bash
  sudo ncdu -x --exclude-caches /
  ```
- **Generate a snapshot for later review**  
  ```bash
  ncdu -o ~/reports/home.ncdu.db ~
  ```
- **Load a stored snapshot**  
  ```bash
  ncdu -f ~/reports/home.ncdu.db
  ```
- **Focus on large directories only (depth 1)**  
  ```bash
  ncdu -d 1 /srv
  ```
- **Skip patterns listed in a file**  
  ```bash
  ncdu --exclude-from ~/.config/ncdu/excludes /var
  ```
- **Batch cleanup and delete files over 10 GB while scanning**  
  ```bash
  sudo ncdu --delete-after 10G /var/log
  ```
- **Stream remote scan to local viewer**  
  ```bash
  ssh web01 "ncdu -o- /var/www" | ncdu -f-
  ```

## References
- Official project site: https://dev.yorhel.nl/ncdu  
- Manual page (online): https://manpages.debian.org/latest/ncdu/ncdu.1.en.html  
- Source repository: https://code.blicky.net/yorhel/ncdu  
- Arch package info: https://archlinux.org/packages/community/x86_64/ncdu/
