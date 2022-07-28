# ArchLinux

My KBD keyboard for Arch Linux, since there is no DHm variant of Colemak in the live iso

```bash
# Sync repos and download git
pacman -Sy && pacman -S git

# Clone mod-dh repo from ColemakMods and use it
git clone https://github.com/ColemakMods/mod-dh && cd mod-dh
loadkeys console/colemak_dh_ansi_us.map

# TODO: Make caps lock backspace
```
