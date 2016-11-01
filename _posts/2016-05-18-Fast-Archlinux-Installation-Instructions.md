The first thing I do when I get a new computer is install Archlinux. I have cached instructions here to breeze through the installation next time.
Most of the instructions are based on the guide [here](https://gist.github.com/mattiaslundberg/8620837).

- Check if Archlinux is reported to run well on the new computer. I have a new T460s, so I checked [here](https://wiki.archlinux.org/index.php/Lenovo_ThinkPad_T460s).

- Boot X86_64 Archlinux in UEFI mode. In this mode, the boot screen looks plain without graphics.

- Load preferred key map. I use dvorak.

```
# loadkeys dvorak
```

- Connect to internet via ethernet and confirm it works. Alternatively use `wifi-menu`.

```
# dhcpcd
# ping www.google.com
```

- Partition hard-disk. I am assuming disk is 512 GiB.

```
# parted /dev/sda
(parted) mklabel gpt
(parted) mkpart ESP fat32 1MiB 1024MiB
(parted) set 1 boot on
(parted) mkpart primary ext4 1024MiB 480GiB
(parted) mkpart primary linux-swap 480GiB 511GiB
(parted) quit
```

- Format partitions.

```
# mkfs.fat -F32 /dev/sda1
# mkfs.ext4 /dev/sda2
# mkswap /dev/sda3
# swapon /dev/sda3
```

- Mount filesystem.

```
# mount /dev/sda2 /mnt
# mkdir -p /mnt/boot
# mount /dev/sda1 /mnt/boot
```

- Install base system.

```
# pacstrap /mnt base base-devel grub-efi-x86_64 vim git efibootmgr dialog wpa_supplicant openssh parted
```

- Create `fstab`.

```
# genfstab -pU /mnt >> /mnt/etc/fstab
```

- Edit `fstab` and add disk optimizations

```
# vim /mnt/etc/fstab
(vim) Mount `/tmp` as `tmpfs` (in-memory filesystem) by adding the line:
tmpfs    /tmp    tmpfs    defaults,noatime,mode=1777    0    0
(vim) Change `relatime` on all non-boot partitions to `noatime` to reduce SSD wear.
```

- Change root into new filesystem.

```
# arch-chroot /mnt /bin/bash
```

- Setup system clock.

```
# ln -s /usr/share/zoneinfo/Europe/Stockholm /etc/localtime
# hwclock --systohc --utc
```

- Set the hostname. Choose a cool hostname!

```
# echo mahaprachanda > /etc/hostname
```

- Update locale.

```
# echo LANG=en_US.UTF-8 >> /etc/locale.conf
# echo LANGUAGE=en_US >> /etc/locale.conf
# echo LC_ALL=C >> /etc/locale.conf
```

- Set password for root.

```
# passwd
```

- Add user.

```
# useradd -m -g users -G wheel,storage,power ananya
# passwd ananya
```

- Configure `mkinitcpio` with modules needed for the `initrd` image.

```
# vim /etc/mkinitcpio.conf
(vim) Add 'ext4' to MODULES list
(vim) Write out and exit.
```

- Regenerate `initrd` image.

```
# mkinitcpio -p linux
```

- Check partition table is correct. Ensure `/dev/sda1` has `boot` and `esp` flags set.

```
# parted /dev/sda print
```

- Setup grub.

```
# grub-install --target=x86_64-efi --efi-directory=/boot /dev/sda
# grub-mkconfig -o /boot/grub/grub.cfg
```

- Exit the `chroot` environment.

```
# exit
```

- Unmount all partitions.

```
# umount -R /mnt
# swapoff -a
```

- Reboot and remove the installation media when shutdown completes.

```
# reboot
```

- If everything went well, you should boot into the installed system.

- Follows tips to improve performance [here](https://wiki.archlinux.org/index.php/Improving_performance).
