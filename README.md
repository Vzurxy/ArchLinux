# ArchLinux

My KBD keyboard for Arch Linux, since there is no DHm variant of Colemak in the live iso

```bash
curl https://raw.githubusercontent.com/Vzurxy/ArchLinux/main/us-colemak_dh.map.gz
  --output /usr/share/kbd/keymaps/us-colemak_dh.map.gz ;
  
loadkeys /usr/share/kbd/keymaps/us-colemak_dh.map.gz
```
