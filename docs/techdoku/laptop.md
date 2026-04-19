# Laptop Neuinstallation, Stand: 12/25

## Arch-Installation

```
loadkeys de-latin1
archinstall
```

***Settings***

- Partitionierung: Nur 1 Partition, danach LVM (macht eigenes LV für Home)  
    \-- Größe muss später angepasst werden!
- Bei Verschlüsselung die crypt-Partition explizit auswählen, Iterations reduzieren
- NetworkManager verwenden

***Nach Installation***

```
sudo pacman -S vim git openssh man-db bash-completion
sudo systemctl enable --now sshd.service

git config --global user.name "Urban Hillebrand"
git config --global user.email "urban.hillebrand@gmail.com"
git config --global init.defaultBranch main
```

***Nur bei winziger Konsolenschrift:***

```
sudo pacman -S terminus-font 
setfont ter-132n

# dauerhaft in /etc/vonsole.conf:
FONT=ter-132n
KEYMAP=de-latin1
```

## yay

Zuerst `makepkg` [optimieren](https://wiki.archlinux.org/title/Makepkg#Optimization)

```
mkdir -p ~/git/aur.archlinux.org && cd ~/git/aur.archlinux.org
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -i
```

## sway(fx)

```
yay -S swayfx kanshi kitty matugen rofi swaylock swayidle waybar wlogout xdg-desktop-portal-gtk fuzzel
```

- noto-fonts auswählen!

## ssh & stow

ssh-keys nach `.ssh` kopieren

```
yay -S keychain stow zoxide fzf starship vi-vim-symlink swww power-profiles-daemon swaync nerd-fonts noto-fonts-emoji ttf-ibm-plex grim slurp cliphist wl-clipboard wl-clip-persist wl-color-picker wev
mkdir -p ~/git/github.com/urbanhillebrand & cd ~/git/github.com/urbanhillebrand
eval $(ssh-agent) && ssh-add
git clone github.com:urbanhillebrand/dotfiles
git clone github.com:urbanhillebrand/scripts
```

***`~/.stowrc`:***

```
--dir=~/git/github.com/urbanhillebrand/dotfiles
--target=~
```

```
rm ~/.bashrc
stow bash kanshi kitty matugen ovpnconf rofi sway swaylock waybar wlogout xdg-user-dirs zathura polkit
mkdir ~/bin
cp ~/git/github.com/urbanhillebrand/scripts/* ~/bin
```

Danach sollte sich sway manuell starten lassen (ggf. mit `Super-Shift-w` ein Wallpaper wählen & neu anmelden)

## Login-Manager

```
sudo pacman -S ly
sudo systemctl enable ly@tty1.service
sudo systemctl disable getty@tty1.service
sudo systemctl enable --now getty@tty2.service
```

Reboot!

## Browser

```
yay -S firefox chromium google-chrome zen-browser-bin
xdg-settings set default-web-browser firefox.desktop
```

## Tools

```
yay -S tcpdump mtr bind openvpn rsync jq meld htop lazygit zathura-pdf-mupdf
sudo pacman -S yazi ffmpeg 7zip jq poppler fd ripgrep fzf zoxide resvg imagemagick satty
yay -S nwg-clipman nwg-displays nwg-look ristretto wlprop
yay -S uhk-agent-appimage winbox tio
yay -S netcalc inetutils sshfs
```

Rules f. Benennung der Interfaces (`/etc/udev/rules.d/99-persistent-net.rules`)

## cibex

==#== OpenVPN

`.ovpn/0_shared` von einem Rechner herkopieren (Vorsicht, ggf. Links reparieren)  
`cbx.ovpn` von einem Rechner herkopieren, und Verbindung aufbauen  
in `/etc/hosts`: `172.23.0.119 repo.cibex.net` eintragen

```
mkdir -p ~/git/repo.cibex.net/ops/configs/openvpn
cd ~/git/repo.cibex.net/ops/configs/openvpn
git clone git@repo.cibex.net:ops/configs/openvpn

mkdir -p ~/git/repo.cibex.net/ops/scripts
cd ~/git/repo.cibex.net/ops/scripts
git clone git@repo.cibex.net:ops/scripts/ovpn
ln -s ~/git/repo.cibex.net/ops/scripts/ovpn/ovpn ~/bin

sudo systemctl enable --now systemd-resolved
ln -s /home/urban/git/repo.cibex.net/ops/configs/openvpn/0_common/helpers/update-systemd-resolved /usr/bin	

sudo rm /etc/resolv.conf && sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

***Programme***

```
yay -S apachedirectorystudio bitwarden remmina freerdp tio mpv ncdu btop

sudo pacman -S libreoffice-still 
sudo pacman -S ttf-caladea ttf-carlito ttf-dejavu ttf-liberation ttf-linux-libertine-g adobe-source-code-pro-fonts adobe-source-sans-fonts adobe-source-serif-fonts --needed
yay -S ttf-gentium-basic light
sudo pacman -S hunspell hunspell-de hunspell-en_us
sudo pacman -S hyphen hyphen-en hyphen-de
sudo pacman -S libmythes mythes-en mythes-de
```

==#== Drucker

`avahi` und `cups` gemäß Wiki einrichten, dann Lexmark und Brother ohne Treiber via IPP everywhere einrichten.

==#== Sonstiges

- cica cbx internal ca-Zertifikat nach `/etc/ca-certificates/trust-source/anchors/` kopieren, dann `sudo trust extract-compat`
- Antora
- paccache
- Gemäß Wiki:
- Microcode
- fwupd
- nvim, LazyVim
- gvfs, gvfs-smb, yazi-gvfs-Plugin...
- self-signed CAs cibex + u
- bluez, bluez-utils, bluetui
- qemu-base libvirt dnsmasq openbsd-netcat virt-manager
- vagrant vagrant-libvirt
- gocryptfs + Aliase
- autofs
- beets, abcde? -> Tidal, oder mopiy?
- solaar

## TODO

- beet convert (komplett)
- mpd, oder mopidy inkl. Streaming? (https://mopidy.com/ext/ -> beets, yt + tidal?)

## Probleme

***check***

- chromium/google-chrome "restore pages"?
- Scratchpads verschwinden manchmal beim ersten Klick (s. Gemini-Vorschläge?)
- Auflösung bzw. Größe der Login-Konsole stimmt nicht
- red screen of death mit swaylock-effects

***erledigt***

- NetworkManager / suspend -> gelöst?
- VPN DNS Leak -> erl.
- fuzzel, rofi oder beides -> fuzzel
- oh-my-posh -> Teil von zsh stow


***ignorieren***

- qt nicht an Thema angepasst
- sway vertauscht Monitore -> Super+Shift+S `sway_switch_outputs`