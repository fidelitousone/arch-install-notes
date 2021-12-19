# arch-install-notes
this exists in case i fuck up

## pre-installation
noveau is extremely ass, if running a GTX 1080, the boot disk spams the screen with errors.
Before booting the disk:
- hit e
- append `nomodeset`
- enter

## installation

### fix system clock
`timedatectl set-ntp true`

### partition disks

use `cfdisk`

Partition | Partition Type | Size
---------- | ---------------- | ---------
/dev/efi_shit | EFI System Partition | 1024 MiB
/dev/swap | Swap space | 2 GiB
/dev/root | rest of disk | whatever tf is left

### format partitions

format the efi
`mkfs.fat -F 32 /dev/sdX1`

make the swap space
`mkswap /dev/sdX2`

make the main filesystem root stuff
`mkfs.ext4 /dev/sdX3`

### mount everything and prepare for install

mount root
`mount /dev/sdX3 /mnt`

mount efi boot partition
`mount /dev/sdX1 /mnt/boot`

turn on swap
`swapon /dev/sdX2`

### install linux

`pacstrap /mnt base linux linux-firmware emacs networkmanager`

### fstab shit

`genfstab -U /mnt >> /mnt/etc/fstab`

### chroot

`arch-chroot /mnt`

### system time

`ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`
`hwclock --systohc`

### locales

`emacs /etc/locale.gen`

uncomment `en_US.UTF-8 UTF-8`

`emacs /etc/locale.conf`

put in `LANG=en_US.UTF-8` and save

### network

`emacs /etc/hostname`

```
whateverhostname

```

### network configuration

`systemctl enable NetworkManager.service`

### bootloader

`pacman -S grub efibootmgr dosfstools os-prober mtools`

`mkdir /boot/EFI`
`mount /dev/sdX1` /boot/EFI`
`grub-install --target=x86-efi --efi-directory=/boot/EFI --bootloader-id=grub_uefi --recheck`

`reboot`





## sources i used
- https://www.reddit.com/r/archlinux/comments/otu8lw/comment/h6zozdq/?utm_source=share&utm_medium=web2x&context=3
- https://www.youtube.com/watch?v=PQgyW10xD8s
