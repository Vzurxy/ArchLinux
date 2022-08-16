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

---

### Keyboard Configuration
> The `archlinux-keyring` package may be outdated on your ISO, so you should run `pacman -Sy archlinux-keyring` to update it.

Sync repositories with `pacman -Sy`, and download git `pacman -S git` 
Clone the repository and set the console keymap

```bash
# Clone ColemakMods mod-dh repository and cd into it
git clone https://github.com/ColemakMods/mod-dh && cd mod-dh

# Set the console keymap
loadkeys console/colemak_dh_ansi_us.map
```

---

### Update system clock & Partitioning

* System Clock

Update system clock with `timedatectl set-ntp true`

* Partitioning
> Note the device field is what I currently have set up.

Use the `cfdisk` utility to manage the partitions.

Partition guide:
| Device         | Partition    | Size      |
|----------------|--------------|-----------|
| /dev/nvme0n1p1 | /boot        | 260MB     |
| /dev/nvme0n1p5 | /            | **REST**  |

* Making the file systems

```bash
# This is not required as my boot partition will be the existing ESP
# mkfs.vfat -F 32 /dev/nvme0n1p1
mkfs.btrfs /dev/nvme0n1p5

# Sometimes if you already formatted a partition
# on the same partition number before,
# It still recognizes the previous file system
# Just use the -f option to force it
```

---

### Mounting

Mount the root partition you just made to `/mnt`, mine is *nvme0n1p5*
`mount /dev/nvme0n1p5 /mnt`

Change directory into `/mnt`
`cd /mnt`

Make btrfs subvolumes
* `btrfs subvolume create @`
* `btrfs subvolume create @home`

Unmount `/mnt`
`umount /mnt`

Mount the btrfs subvolumes, `nvme0n1p1` is my ESP
* `mount -o noatime,space_cache=v2,compress=zstd,ssd,subvol=@ /dev/nvme0n1p5 /mnt`
* Make the home and boot directories `mkdir /mnt/{boot,home}`
* `mount -o noatime,space_cache=v2,compress=zstd,ssd,subvol=@home /dev/nvme0n1p5 /mnt/home`
* `mount /dev/nvme0n1p1 /mnt/boot`

---

### Install base system and generate fstab

* Base system

I will be installing these packages for my base system
```bash
pacstrap /mnt base linux linux-firmware nano intel-ucode sof-firmware btrfs-progs
```

* fstab
This will generate the fstab entries with UUID for the currently mounted file system
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Arch Chroot
We will enter our newly installed base system, to configure more stuff inside

### Set up timezone, hardware clock and locales

* Timezone and hardware clock

```bash
# Set up timezone
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime

# Hardware clock sync
hwclock --systohc
```

* Locales
> It is important you use locale-gen to generate the necessary locale settings.

We need to uncomment **en_US.UTF-8** in /etc/locale.gen
Use `nano` to edit `/etc/locale.gen`
Find the correct locale and uncomment it, save the file and exit

Generate the locales with `locale-gen`

We will need to edit `/etc/locale.conf`, as some programs check there instead.
Use `nano` to edit `/etc/locale.conf`
Insert `LANG=en_US.UTF-8`, save and exit.

### Hostnames and hosts

* Hostnames
Change the host name by echoing your preferred host name (*computer name*) to /etc/hostname
`echo "arch" >> /etc/hostname`

Change the hosts file so programs can find the correct address locally
Use `nano` to edit `/etc/hosts`

> Replace **<HOSTNAME>** with the hostname you gave to the computer just above.

Insert the following:
```
127.0.0.1    localhost
::1          localhost
127.0.1.1    <HOSTNAME>.localdomain    <HOSTNAME>
```

---

### Change password for the root user
Run `passwd` and input the password you want to use for the root user.

---

### Essential and user packages, and configuration

* Packages

Some programs are essential to the installation
Other programs are useful to me in certain cases

These can be installed with

```
pacman -S grub efibootmgr networkmanager mtools dosfstools reflector base-devel linux-headers avahi xdg-user-dirs xdg-utils cups bluez bluez-utils pipewire wireplumber pipewire-pulse firewalld os-prober terminus-font bash-completion mesa
```

* Configuration
> grub
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

> systemd services and timers
```bash
systemctl enable NetworkManager
systemctl enable bluetooth
systemctl enable avahi-daemon
systemctl enable cups
systemctl enable reflector.timer

# ONLY IF YOU HAVE A TRIM SUPPORTED SSD OR NVME
systemctl enable fstrim.timer

systemctl enable firewalld
```

---

### Making our user
We will make a user and add it to the wheel group to be able to run sudo with password

```bash
# Replace <USERNAME> with your username

useradd -m <USERNAME>

# Change password for <USERNAME>
passwd <USERNAME>

# Add user to groups
usermod -aG wheel ceg
```

Now we need to uncomment the %wheel line, so members of the wheel group can have access to run sudo with password
We can do this by using `visudo`

```bash
EDITOR=nano visudo
```

---

### Finalizing

Unmount everything from the mount point `/mnt`
`umount -R /mnt`

Exit the chroot
`exit`

Reboot the system
`reboot`

# Post Install

Login into your system and connect to a network with `sudo nmtui`
Check for any updates with `sudo pacman -Syu`

### Install an AUR helper
We will be using paru to help install and manage AUR packages.

```bash
git clone https://aur.archlinux.org/paru-bin
cd paru-bin
makepkg -si
```

### Enable OS Prober
OS Prober does not work in the live usb, so we wait until we finish to configure it

Edit `/etc/default/grub` and uncomment the DISABLE_OS_PROBER=false line
Remake the grub config with `grub-mkconfig -o /boot/grub/grub.cfg`

---
# END
# EVERYTHING BEYOND THIS IS JUST A TODO LIST
# TODO: make plymouth and display manager along with system performance things like thermald and other stuff

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
