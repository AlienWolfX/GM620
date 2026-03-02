# GM620 Reverse Engineering

> [!WARNING]
> **Modifying the NAND flash can brick your device. Always create a backup before proceeding.**
> [!NOTE]
> After switching kernels, reverting to the previous kernel involves overwriting the current running kernel with the one you want to use, so you **MUST FIRST CREATE A BACKUP**

## Changing Boot Kernel

> [!TIP]
> Before starting check the others partition with `cat /proc/mtd`

Modify `mtd7` partition by changing `BootImageNum` value:

- Change from `0x00000001` to `0x00000000` to boot from kernel 0
- Change from `0x00000000` to `0x00000001` to boot from kernel 1

### Write 0 to the partition

```bash
dd if=/dev/zero of=/tmp/zero.bin bs=1048576 count=6 2>/dev/null
nandwrite -p /dev/mtd7 /tmp/zero.bin
rm /tmp/zero.bin
```

### Write modified bin to flash

```bash
nandwrite -p /dev/mtd7 /mnt/{USB_DIR}/mtd7_others.bin
```

## Flashing Kernel Partitions

> [!CAUTION]
> **Flashing kernel partitions is extremely dangerous and can brick your device permanently!**
> - Always verify your backup files before flashing
> - Ensure the device doesn't lose power during the write operation
> - Make sure you have a working kernel in at least one partition
> - Consider the consequences: a corrupted kernel means the device won't boot

## Flash Kernel to mtd3 (kernel0)

> [!WARNING]
> Only proceed if you have a verified backup and understand the risks!

### Write new kernel to flash

```bash
nandwrite -p /dev/mtd3 /mnt/{USB_DIR}/mtd6_kernel1.bin
```

### Verify the flash

After flashing, verify the write was successful:

```bash
md5sum /dev/mtd3
reboot
```
