# GM620 Reverse Engineering

## Changing Boot Kernel

Modify `mtd7` partition by changing `BootImageNum` value:

- Change from `0x00000001` to `0x00000000` to boot from kernel 0
- Change from `0x00000000` to `0x00000001` to boot from kernel 1

## Flashing

> [!WARNING]
> **Modifying the NAND flash can brick your device. Always create a backup before proceeding.**
> [!NOTE]
> After switching kernels, reverting to the previous kernel can be challenging. If you have experience with this process, please create an issue to collaborate on a solution.

### Erase

#### Method 1: Using dd

```bash
dd if=/dev/zero of=/tmp/zero.bin bs=4194304 count=1 2>/dev/null
nandwrite -p /dev/mtd7 /tmp/zero.bin
```

#### Method 2: Without dd

```bash
cat /dev/zero | head -c 4194304 | nandwrite -p /dev/mtd7 -
```

### Flash

```bash
nandwrite -p /dev/mtd7 /mnt/usb1_1/modified_mtd7.bin
```
