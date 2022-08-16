![ARCH](https://archlinux.org/static/logos/archlinux-logo-dark-scalable.518881f04ca9.svg)

> A **personal** [Arch Linux](https://archlinux.org) guide for my setup.

___

**Table of Contents**

* [Install](#install)
* [Post Install](#post-install)

# Install
### Internet Connection

Execute `iwctl` to get into the CLI helper
* Scan wireless adapter `station wlan0 scan`
* Get available networks `station wlan0 get-networks`
* Connect to network `station wlan0 connect <SSID>`


### Keyboard Configuration
> The `archlinux-keyring` package may be outdated on your ISO, so you should run `pacman -Sy archlinux-keyring` to update it.

Sync repositories with `pacman -Sy`, and download git `pacman -S git` 

Git clone the (ColemakMods)[https://github.com/ColemakMods) mod-dh repository and cd into it
`git clone https://github.com/ColemakMods/mod-dh && cd mod-dh`

# Post Install


### Live ISO

My KBD keyboard for Arch Linux, since there is no DHm variant of Colemak in the live iso

```bash
# Sync repos and download git
pacman -Sy && pacman -S git

# Clone mod-dh repo from ColemakMods and use it
git clone https://github.com/ColemakMods/mod-dh && cd mod-dh
loadkeys console/colemak_dh_ansi_us.map
```

### Metal Install

```bash
localectl set-x11-keymap us pc104 colemak_dh caps:backspace
```

## Essential Packages

```
base
linux
linux-firmware
sof-firmware
btrfs-progs
grub
intel-ucode
intel-media-driver
mesa
bluez
bluez-utils
cups
xdg-user-dirs
xdg-utils
wezterm
firefox
```

### Entire package list

In the PKGS file
