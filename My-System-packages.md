
---

# 🧰 My System Packages & Reinstall Guide (Arch Linux → DWM)

> Minimal Arch Linux → full DWM workstation with my dotfiles/tools.
> Target user: `muhammad` • Editor: **Neovim** • WM: **dwm** • Menu: **dmenu** • Status: **slstatus**
> ThinkPad home: `/home/muhammad` • Server home: `/home/homelab`

---

## ✨ What this document covers

* A clean, deterministic reinstall checklist 🧭
* Curated package sets (Core / Useful / Productivity / Flatpak) 📦
* My DWM stack (build from my forks) 🧱
* System services, fonts, audio, and startup pipeline 🎛️
* Single-shot installer scripts you can paste & run 🚀

---

## 🧭 Reinstall roadmap (from minimal Arch)

1. **Create user & base tools**
2. **Networking, audio (PipeWire), microcode, time/locale**
3. **Xorg + drivers + fonts**
4. **Build and install**: `dwm`, `dmenu`, `slstatus` (from my forks)
5. **Core utilities**: terminal, clipboard, wallpaper, compositor, etc.
6. **Quality of life**: useful CLI tools, file manager, thumbnails
7. **Productivity apps** (+ Flatpak apps)
8. **Autostart: `.xinitrc`** → `startx` to enter DWM
9. **Enable services** and reboot

---

## 📦 Package sets

> Install with `pacman -S --needed <packages...>` (AUR via `yay -S` after you install `yay` below).

### 🧱 Core Packages (Xorg + DWM stack + essentials)

**System base**

```
base-devel git curl wget neovim unzip zip p7zip tar
```

**Xorg + input**

```
xorg-server xorg-xinit xorg-xrandr xorg-xsetroot xorg-xinput xorg-xprop
```

**Video (Intel ThinkPad)**

```
mesa xf86-video-intel vulkan-intel
```

**Audio (PipeWire)**

```
pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber pavucontrol
```

**Networking & power**

```
networkmanager openssh ufw tlp powertop
```

**Terminal & shell**

```
alacritty fish
```

**Fonts (Latin + CJK + Emoji + Nerd)**

```
ttf-dejavu ttf-liberation noto-fonts noto-fonts-cjk noto-fonts-emoji
ttf-nerd-fonts-symbols ttf-nerd-fonts-symbols-common
```

**DWM stack build deps**

```
libx11 libxft libxinerama freetype2
```

**Wallpaper & compositor**

```
nitrogen
```

> `picom-jonaburg-git` (AUR) installed via `yay` below.

**Clipboard / screenshots / misc X**

```
xclip xsel maim flameshot
```

**File manager & mounts (nice on laptops)**

```
thunar thunar-volman tumbler gvfs udisks2
```

---

### 🧪 Useful Packages (CLI + GUI helpers)

**System info / monitoring**

```
fastfetch btop inxi lshw
```

**Modern CLI tools**

```
ripgrep fd eza bat fzf jq yq tmux neofetch
```

**Archives / fs utils**

```
rsync rclone ncdu trash-cli
```

**Bluetooth (if needed)**

```
bluez bluez-utils blueman
```

**Printing (optional)**

```
cups cups-pdf system-config-printer
```

**Browsers / viewers (lightweight extras)**

```
vimiv feh mpv vlc
```

---

### 💼 Productivity Packages (daily drivers)

```
chromium thunderbird okular keepassxc syncthing
```

> (Thunderbird aligns with your preference; Okular for PDFs; KeepassXC for passwords; Syncthing for backups.)

---

### 🟦 Flatpak Packages (desktop apps from Flathub)

> First-time setup:

```bash
sudo pacman -S --needed flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

**Install apps**

```
OnlyOffice        org.onlyoffice.desktopeditors
Discord           com.discordapp.Discord
RustDesk          com.rustdesk.RustDesk
ZapZap (WhatsApp) io.github.marcosrdz.zapzap
Obsidian          md.obsidian.Obsidian
Spotify           com.spotify.Client
```

Install all:

```bash
flatpak install -y flathub \
  org.onlyoffice.desktopeditors \
  com.discordapp.Discord \
  com.rustdesk.RustDesk \
  io.github.marcosrdz.zapzap \
  md.obsidian.Obsidian \
  com.spotify.Client
```

---

## ⚙️ One-shot bootstrap (from minimal Arch)

> Run as **root** (first section), then switch to your user `muhammad`.

```bash
# 0) create user, wheel sudo
useradd -m -G wheel -s /bin/fish muhammad
passwd muhammad
EDITOR=nvim visudo   # enable %wheel ALL=(ALL:ALL) ALL

# 1) base and everyday tools
pacman -Syu --noconfirm
pacman -S --needed --noconfirm \
  base-devel git curl wget neovim unzip zip p7zip tar \
  xorg-server xorg-xinit xorg-xrandr xorg-xsetroot xorg-xinput xorg-xprop \
  mesa xf86-video-intel vulkan-intel \
  pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber pavucontrol \
  networkmanager openssh ufw tlp powertop \
  alacritty fish \
  ttf-dejavu ttf-liberation noto-fonts noto-fonts-cjk noto-fonts-emoji \
  ttf-nerd-fonts-symbols ttf-nerd-fonts-symbols-common \
  libx11 libxft libxinerama freetype2 \
  nitrogen xclip xsel maim flameshot \
  thunar thunar-volman tumbler gvfs udisks2 \
  fastfetch btop inxi lshw ripgrep fd eza bat fzf jq yq tmux neofetch \
  rsync rclone ncdu trash-cli \
  bluez bluez-utils blueman \
  cups cups-pdf system-config-printer \
  mpv vlc feh chromium thunderbird okular keepassxc syncthing flatpak

# 2) services
systemctl enable --now NetworkManager
systemctl enable --now bluetooth        # if you use BT
systemctl enable --now cups             # printing
systemctl enable --now tlp              # laptop power saving
systemctl enable --now syncthing@"muhammad"
systemctl enable --now sshd

# 3) default shell
chsh -s /bin/fish muhammad
```

Log out and back in as `muhammad`:

```bash
# 4) Arch User Repository helper (yay)
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si --noconfirm

# 5) compositor (jonaburg) from AUR
yay -S --needed --noconfirm picom-jonaburg-git
```

---

## 🧱 Build my DWM stack (from my forks)

> Repos:
>
> * **dwm**: [https://github.com/coder595/git/tree/main/dwm](https://github.com/coder595/git/tree/main/dwm)
> * **dmenu**: [https://github.com/coder595/git/tree/main/dmenu](https://github.com/coder595/git/tree/main/dmenu)
> * **slstatus**: [https://github.com/coder595/git/tree/main/slstatus](https://github.com/coder595/git/tree/main/slstatus)

> I keep sources in `~/.local/src`:

```bash
mkdir -p ~/.local/src && cd ~/.local/src

# DWM
git clone https://github.com/coder595/git --depth 1 coder595-main
cp -r coder595-main/dwm ./dwm
cp -r coder595-main/dmenu ./dmenu
cp -r coder595-main/slstatus ./slstatus

# Build & install
cd ~/.local/src/dwm      && sudo make clean install
cd ~/.local/src/dmenu    && sudo make clean install
cd ~/.local/src/slstatus && sudo make clean install
```

> If patches or `config.h` exist in the repo, they’ll be compiled in automatically. Rebuild after edits with `sudo make install`.

---

## 🎨 Startup pipeline (`.xinitrc`)

Create `~/.xinitrc`:

```bash
cat > ~/.xinitrc << 'EOF'
#!/bin/sh
# Keyboard / cursor (optional)
# setxkbmap -layout us
xsetroot -cursor_name left_ptr

# Restore wallpaper
nitrogen --restore &

# Compositor (jonaburg fork)
picom --experimental-backends --config "$HOME/.config/picom/picom.conf" &

# Status bar
slstatus &

# Polkit agent (for GUI auth dialogs)
if command -v /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1 >/dev/null 2>&1; then
  /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1 &
fi

# Start DWM
exec dwm
EOF

chmod +x ~/.xinitrc
```

> Start with: `startx`
> Tip: Add `[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx` to your `~/.bash_profile` or Fish equivalent to auto-launch DWM on TTY1.

---

## 🔊 PipeWire sanity check

```bash
systemctl --user status pipewire wireplumber
pactl info | grep "Server Name"
pavucontrol   # manage default devices/levels
```

---

## 🖼️ Nitrogen (wallpapers)

* Place images in `~/Pictures/Wallpapers/`
* Run `nitrogen` → set, save; it will auto-restore via `.xinitrc`.

---

## 🌐 Networking + Firewall

```bash
nmcli device status
nm-connection-editor        # optional GUI
sudo ufw default deny
sudo ufw allow 22/tcp       # SSH
sudo ufw allow 80,443/tcp   # if you serve web
sudo ufw enable
```

---

## 🧩 Optional quality-of-life

**Fish shell goodies**

```bash
fish_config        # web UI
```

**Thunar file ops**

```bash
sudo pacman -S --needed gvfs-mtp gvfs-afc
```

**Power tuning**

```bash
sudo powertop --auto-tune
```

---

## 🧪 Verify everything

* `startx` → DWM loads, slstatus shows bar, wallpaper restored, picom effects present
* Audio works: `pavucontrol` → test with `mpv`/`vlc`
* Clipboard: `echo test | xclip -selection clipboard`
* Network: `nmcli`, browse via `chromium`
* Fonts: emoji and icons show correctly (Nerd symbols)

---

## 🔁 Rebuild/Update workflow

```bash
# Update system
sudo pacman -Syu
yay -Syu    # includes AUR

# Rebuild my forks if I changed configs
cd ~/.local/src/dwm      && sudo make clean install
cd ~/.local/src/dmenu    && sudo make clean install
cd ~/.local/src/slstatus && sudo make clean install
```

---

## 🧩 Quick install scripts

> Paste into a file, make executable, run. Adjust if you’re on the server (`/home/homelab`).

**`01-core.sh`**

```bash
#!/usr/bin/env bash
set -euo pipefail
sudo pacman -Syu --noconfirm
sudo pacman -S --needed --noconfirm \
  base-devel git curl wget neovim unzip zip p7zip tar \
  xorg-server xorg-xinit xorg-xrandr xorg-xsetroot xorg-xinput xorg-xprop \
  mesa xf86-video-intel vulkan-intel \
  pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber pavucontrol \
  networkmanager openssh ufw tlp powertop \
  alacritty fish \
  ttf-dejavu ttf-liberation noto-fonts noto-fonts-cjk noto-fonts-emoji \
  ttf-nerd-fonts-symbols ttf-nerd-fonts-symbols-common \
  libx11 libxft libxinerama freetype2 \
  nitrogen xclip xsel maim flameshot \
  thunar thunar-volman tumbler gvfs udisks2 \
  fastfetch btop inxi lshw ripgrep fd eza bat fzf jq yq tmux neofetch \
  rsync rclone ncdu trash-cli \
  bluez bluez-utils blueman \
  cups cups-pdf system-config-printer \
  mpv vlc feh chromium thunderbird okular keepassxc syncthing flatpak

sudo systemctl enable --now NetworkManager
sudo systemctl enable --now cups
sudo systemctl enable --now tlp
sudo systemctl enable --now sshd
sudo systemctl enable --now syncthing@"$USER" || true
```

**`02-aur-and-picom.sh`**

```bash
#!/usr/bin/env bash
set -euo pipefail
tmpdir="$(mktemp -d)"
pushd "$tmpdir"
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si --noconfirm
popd
yay -S --needed --noconfirm picom-jonaburg-git
```

**`03-dwm-stack.sh`**

```bash
#!/usr/bin/env bash
set -euo pipefail
mkdir -p ~/.local/src && cd ~/.local/src
if [ ! -d coder595-main ]; then
  git clone --depth 1 https://github.com/coder595/git coder595-main
fi
cp -r coder595-main/dwm      ./dwm      2>/dev/null || true
cp -r coder595-main/dmenu    ./dmenu    2>/dev/null || true
cp -r coder595-main/slstatus ./slstatus 2>/dev/null || true

cd ~/.local/src/dwm      && sudo make clean install
cd ~/.local/src/dmenu    && sudo make clean install
cd ~/.local/src/slstatus && sudo make clean install

# xinitrc
mkdir -p ~/.config/picom
[ -f ~/.xinitrc ] || cat > ~/.xinitrc << 'EOF'
#!/bin/sh
xsetroot -cursor_name left_ptr
nitrogen --restore &
picom --experimental-backends --config "$HOME/.config/picom/picom.conf" &
slstatus &
exec dwm
EOF
chmod +x ~/.xinitrc
```

**`04-flatpak.sh`**

```bash
#!/usr/bin/env bash
set -euo pipefail
sudo pacman -S --needed flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install -y flathub \
  org.onlyoffice.desktopeditors \
  com.discordapp.Discord \
  com.rustdesk.RustDesk \
  io.github.marcosrdz.zapzap \
  md.obsidian.Obsidian \
  com.spotify.Client
```

Run them:

```bash
bash 01-core.sh
bash 02-aur-and-picom.sh
bash 03-dwm-stack.sh
bash 04-flatpak.sh
```

---

## 🧯 Troubleshooting quickies

* **Picom doesn’t start** → run `picom -b --log-file /tmp/picom.log` and inspect output.
* **Fonts missing symbols** → ensure `ttf-nerd-fonts-symbols(_common)` and `noto-fonts-emoji` installed; relogin.
* **No sound** → `pavucontrol` → set correct default sink; confirm `wireplumber` active (`systemctl --user status wireplumber`).
* **Screen tearing** (Intel) → try `picom` vsync and/or `Option "TearFree" "true"` in `/etc/X11/xorg.conf.d/20-intel.conf`.
* **Startx black screen** → check `.xinitrc` exec line is **only** `exec dwm` (no trailing commands).
* **AUR build fails** → `yay -Syu` then rebuild; clean old `pkg/` and `src/`.

---

## 🧾 Notes

* Editor is **Neovim** everywhere; avoid `nano`.
* Terminal is **Alacritty** (Gruvbox theme per your setup).
* Shell is **Fish** (`chsh -s /bin/fish muhammad`).
* Compositor: **picom-jonaburg** (animations/effects you like).
* Wallpaper: **nitrogen** (auto-restore from `.xinitrc`).
* Status bar: **slstatus**.
* Reverse proxy, Pi-hole, etc. live on your homelab—out of scope for this desktop guide.

---

