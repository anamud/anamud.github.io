Dang! Windown 10 refuses to boot. Thankfully I have a recovery ISO saved on
`firenze`, my other laptop.  All that is now required is to transfer the
recovery ISO to a USB disk, reebot on the busted machine, and hope Windows can
repeair itself.

This guide assumes the USB disk is atleast 4GB in size and is mapped to device
/dev/sdc1. Use the fdisk program to understand your USB disk properties.

> Warning: All data on USB disk will be erased. Ensure valuable data on disk
is backed up!

- Unmount USB disk

    ```bash
    [ananya@firenze WIN10-Recovery-ISO]$ sudo umount /dev/sdc1
    ```

- Prepare USB disk

    Required:
    - MSDOS partition table
    - Format FAT32
    - Single primary partition
    - Flags lba and boot set

    We will use the parted program to prepare the USB disk.

    > Note the argument to parted. It is */dev/sdc*, not /dev/sdc1.

    ```bash
    [ananya@firenze WIN10-Recovery-ISO]$ sudo parted /dev/sdc
    GNU Parted 3.2
    Using /dev/sdc
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) mklabel msdos
    (parted) mkpart primary fat32 1MiB 100%
    (parted) set 1 boot on
    (parted) set 1 lba on
    (parted) quit
    ```

- Confirm USB disk state

    Ensure USB drive is formatted to FAT32, has a single primary partition,
    has a MSDOS partition table, and has flags lba and boot set
    as shown below.

    ```bash
    [ananya@firenze WIN10-Recovery-ISO]$ sudo parted /dev/sdc
    GNU Parted 3.2
    Using /dev/sdc
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Corsair Flash Voyager (scsi)
    Disk /dev/sdc: 4060MB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End     Size    Type     File system  Flags
     1      65.5kB  4060MB  4060MB  primary  fat32        boot, lba

    (parted) quit
    ```

- Copy contents of ISO to USB disk

    ```bash
    [ananya@firenze WIN10-Recovery-ISO]$ sudo dd if=RECOVERY.ISO of=/dev/sdc
    ```
