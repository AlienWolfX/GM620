# GM620 Reverse Engineering

GM620 FTTH, an optical network terminal (ONT), is a high-end home gateway in FTTH solution. By using the GPON technology, ultra-broadband access is provided for home and SOHO users. At the same time, GM620 FTTH provides 1ge&3fe+1tel+2usb+Ac 5g Wifi. The product features high-performance forwarding capabilities to ensure excellent experience with VoIP, Internet and HD video services.

## Specs

| Component          | Specification                          |
| ------------------ | -------------------------------------- |
| **SoC**            | ZTE ZX279128                           |
| **CPU**            | ARM Cortex-A9 Dual-Core @ 800MHz       |
| **Architecture**   | ARMv7 Processor [414fc091]             |
| **RAM**            | 512 MiB DDR                            |
| **Flash**          | 256 MiB NAND (Toshiba TC58BVG1S3HTA00) |
| **Cache**          | L2C-310, 512 KB, 16-way                |
| **Bootloader**     | U-Boot 2013.04-svn7621                 |
| **Kernel**         | Linux 4.1.25+ SMP PREEMPT              |
| **GPON**           | ZX279100 GPON MAC                      |
| **Optical Module** | GN25L95 (ZTE_B+\_G)                    |
| **Voice (SLIC)**   | Siliconlab Si3218x                     |
| **Ethernet Ports** | 1x GE + 3x FE                          |
| **USB**            | 2x USB (DWC OTG + xHCI)                |
| **PCIe**           | 2x PCIe ports                          |
| **WiFi**           | 2Ghz 5Ghz WiFi                         |
| **Phone**          | 1x TEL port                            |

> [!WARNING]
> **Modifying the NAND flash can brick your device. Always create a backup before proceeding.**

### Changing Boot Kernel

> [!NOTE]
> After switching kernels, reverting to the previous kernel involves overwriting the current running kernel with the one you want to use, so you **MUST FIRST CREATE A BACKUP**.

> [!TIP]
> Before starting check the `others` partition with `cat /proc/mtd`.

Modify `mtd7` partition by changing `BootImageNum` value:

- Change from `0x00000001` to `0x00000000` to boot from kernel 0
- Change from `0x00000000` to `0x00000001` to boot from kernel 1

#### Write 0 to the partition

```bash
dd if=/dev/zero of=/tmp/zero.bin bs=1048576 count=6 2>/dev/null
nandwrite -p /dev/mtd7 /tmp/zero.bin
rm /tmp/zero.bin
```

#### Write modified bin to flash

```bash
nandwrite -p /dev/mtd7 /mnt/{USB_DIR}/mtd7_others.bin
```

### Flashing Kernel Partitions

> [!CAUTION]
> **Flashing kernel partitions is extremely dangerous and can brick your device permanently!**.
>
> - Always verify your backup files before flashing
> - Ensure the device doesn't lose power during the write operation
> - Make sure you have a working kernel in at least one partition
> - Consider the consequences: a corrupted kernel means the device won't boot

#### Flash Kernel to mtd3 (kernel0)

> [!WARNING]
> Only proceed if you have a verified backup and understand the risks!

#### Write new kernel to flash

```bash
nandwrite -p /dev/mtd3 /mnt/{USB_DIR}/mtd6_kernel1.bin
```

#### Verify the flash

After flashing, verify the write was successful:

```bash
md5sum /dev/mtd3
reboot
```
