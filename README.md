# ArchLinux

## Keyboard

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
sof-firmware
btrfs-progs
grub
intel-ucode
intel-media-driver
mesa
```

### Entire package list

In the PKGS file
