# dotfiles

# ==============================
# Keyd
# ==============================
https://github.com/rvaiya/keyd
/etc/keyd/default.conf
```
[ids]

*

[main]

# Maps capslock to escape when pressed and super when held.
capslock = overload(meta, esc)
```

# ==============================
# AppImageLauncher from upstream
# ==============================
```
sudo pacman -S --needed \
  git base-devel cmake \
  glib2 cairo librsvg fuse2 libarchive libxpm \
  qt5-base qt5-declarative qt5-tools qt5-wayland \
  curl boost patchelf nlohmann-json

cd ~
git clone https://github.com/TheAssassin/AppImageLauncher.git
cd AppImageLauncher
git checkout v3.0.0-beta-3
git submodule update --init --recursive

cd ~
git clone https://github.com/vietjtnguyen/argagg.git
cd argagg
sudo install -Dm644 include/argagg/argagg.hpp \
  /usr/local/include/argagg/argagg.hpp

cd ~/AppImageLauncher
rm -rf build
mkdir build
cd build

cmake .. \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/usr/local

make -j"$(nproc)"
sudo make install

which AppImageLauncher
which appimagelauncherd
```

# ==============================
# Switch CachyOS from fish to zsh
# ==============================

chsh -s /usr/bin/zsh
getent passwd "$USER"

# Log out and back in after this.


# ==============================
# Fresh zsh + CachyOS config + fish-like prompt
# ==============================

rm -f ~/.zshrc ~/.p10k.zsh
touch ~/.zshrc

cat > ~/.zshrc <<'EOF'
POWERLEVEL9K_DISABLE_CONFIGURATION_WIZARD=true

source /usr/share/cachyos-zsh-config/cachyos-config.zsh

unsetopt correctall

# Disable Powerlevel10k prompt hooks
unset -f p10k 2>/dev/null
precmd_functions=()
preexec_functions=()

# CachyOS fish/Pure-like prompt
PROMPT='%F{cyan}%~%f
%(?.%F{magenta}.%F{red})❯ %f'
RPROMPT=''

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion"
EOF

exec zsh


# ==============================
# Install/use nvm
# ==============================

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
source ~/.zshrc

nvm install node
nvm current
node --version
npm --version


# ==============================
# Niri display scaling / refresh rate
# ==============================

niri msg outputs

nano ~/.config/niri/cfg/display.kdl

# Add/edit this block:
# output "Dell Inc. DELL U3225QE FFWMNF4" {
#     mode "3840x2160@119.999"
#     scale 2
# }

niri msg action load-config-file
niri msg outputs


# ==============================
# Niri maximize-window-to-edges binding
# ==============================

niri msg action --help | grep maximize

nano ~/.config/niri/cfg/keybinds.kdl

# Add inside binds { ... }:
# Mod+Shift+M { maximize-window-to-edges; }

niri msg action load-config-file

# ==============================
# NVM Shell Performance Fix
# ==============================
# Lazy-load nvm to avoid slow shell startup.
# Remove/comment any existing eager nvm lines like:
#   export NVM_DIR="$HOME/.nvm"
#   [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
#   [ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion"

export NVM_DIR="$HOME/.nvm"

__load_nvm() {
  unset -f nvm node npm npx pnpm yarn corepack 2>/dev/null

  [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
  [ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion"
}

nvm() { __load_nvm; nvm "$@"; }
node() { __load_nvm; command node "$@"; }
npm() { __load_nvm; command npm "$@"; }
npx() { __load_nvm; command npx "$@"; }
pnpm() { __load_nvm; command pnpm "$@"; }
yarn() { __load_nvm; command yarn "$@"; }
corepack() { __load_nvm; command corepack "$@"; }

# ==============================
# Lazygit Alias
# ==============================
alias lazy='lazygit'

# ==============================
# Treesitter
# ==============================
`sudo pacman -S tree-sitter-cli`

# ==============================
# For Yazi
# ==============================
`sudo pacman -S 7zip zoxide resvg`

# ==============================
# Proton VPN
# ==============================
`sudo pacman -S proton-vpn-gtk-app`
run with:
`protonvpn-app`

# ==============================
# Monitor Arrangement
# ==============================
`sudo pacman -S --needed wdisplays`

# ==============================
# Make CachyOS Hyprland looke like CachyOS Niri
# ==============================
```
sudo pacman -Syu
sudo pacman -S --needed cachyos-hypr-noctalia
```
Then replace your user configs with the package’s CachyOS defaults:
```
rm -rf \
  ~/.config/hypr \
  ~/.config/noctalia \
  ~/.config/btop \
  ~/.config/dolphinrc \
  ~/.config/gtk-3.0 \
  ~/.config/gtk-4.0 \
  ~/.config/kdeglobals \
  ~/.config/kitty \
  ~/.config/menus \
  ~/.config/qt6ct \
  ~/.config/uwsm \
  ~/.config/xsettingsd \
  ~/.icons/default \
  ~/.local/share/icons/Bibata-Modern-Ice

mkdir -p ~/.config ~/.icons ~/.local/share/icons

cp -a /etc/skel/.config/hypr ~/.config/
cp -a /etc/skel/.config/noctalia ~/.config/
cp -a /etc/skel/.config/btop ~/.config/
cp -a /etc/skel/.config/dolphinrc ~/.config/
cp -a /etc/skel/.config/gtk-3.0 ~/.config/
cp -a /etc/skel/.config/gtk-4.0 ~/.config/
cp -a /etc/skel/.config/kdeglobals ~/.config/
cp -a /etc/skel/.config/kitty ~/.config/
cp -a /etc/skel/.config/menus ~/.config/
cp -a /etc/skel/.config/qt6ct ~/.config/
cp -a /etc/skel/.config/uwsm ~/.config/
cp -a /etc/skel/.config/xsettingsd ~/.config/
cp -a /etc/skel/.icons/default ~/.icons/
cp -a /etc/skel/.local/share/icons/Bibata-Modern-Ice ~/.local/share/icons/
```
Then reboot:
`systemctl reboot`
At SDDM, choose the Hyprland/UWSM Hyprland session if there are multiple Hyprland entries.

Do not build Noctalia manually from GitHub for this setup. The CachyOS package path should give you:
```
cachyos-hypr-noctalia
noctalia-shell
noctalia-qs
```
You can verify afterward with:
```
command -v qs
pacman -Q cachyos-hypr-noctalia noctalia-shell noctalia-qs
hyprctl configerrors
```
