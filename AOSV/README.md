
# Advanced Operative Systems and Virtualization

## Outline
1. **BIOS/UEFI** Actual Hw setup
   1. Pre-Boot and Real Mode
   2. BIOS
2. **Bootloader Stage 1** Executes the stage 2 bootloader (skipped for UEFI)
   1. MBR
   2. x86 Protected Mode
   3. x86 Memory Addressing
   4. x86 Privileges and Protection
   5. Paging
3. **Bootloader Stage 2** Loads and starts the kernel
4. **Kernel** Takes control and initializes the machine (machine-dependent operations)
   1. GRUB/UEFI
   2. Multi-core Support
5. **Init (or systemd)** First process: basic environment initialization
6. **Runlevels/Targets** Initializes the user environment

# BIOS and UEFI
## Pre-Boot and Real Mode
### Pre-Pre Boot
When the power button is pushed, the CPU does not start directly to run code (BIOS).

There are some operation that must be carried out before.
- power supply must settle down to its nominal state
- a number of derived voltages must stabilize: 1.5V, 3.3V, 5V and 12V. **must be supplied in a particular sequence, this is called power sequencing**
- platform clocks must be derived and this takes time

after this the CLPD de-asserted the reset line of the CPU.

![](/AOSV/img/clpd_pre_boot.PNG)

### x86 Real Mode
In this point the sistem is in a very nasic state:
- caches are disabled
- the MMU (Memory Management Unit) is disabled
- only one CPU core can run the code (the BSP - bootstrap processor)
- the CPU runs in **Real Mode**, a compatible way with the Intel 8086 (1978, yes 1978)
- nothing is in RAM
  
The x86 real mode is charatirized by:
- **no memory protection, no privilege levels, no multitasking**
- **direct access to I/O and peripheral** 
- memory
  - 20 bit of a segmented memory space for a total of 1MB of addressable memory
  - 16 bit for instructions

### Segmented memory 
Starting from the Intel 8086, the addressing of memory is segmented. This means that a memory location is referenced with two components: the segment id and the offset.
Therefore, the **logical address** can be expressed as:
```assembly
<seg:offset> (e.g. <A:0x10>)
```
There are 4 basic **16-bit** segment registers:
- **CS** code segment
- **DS** data segment
- **SS** stack segment
- **ES** Extra segment (used by programmer)
  
nowdays the segmentation is still present and **il always enabled**. Each assembly instruction that uses memory **implicitly uses a segment register**, for example:

- **jmp** uses CS
- **'push** uses SS

Most of the segment addresses can be loaded with mov instruction but **CS only with jmp or call**.

![](/AOSV/img/x86_seg_mem.PNG)

## BIOS

### The First Fetched Instruction
Once the CLPD de-assert the reset line, newer processors load a microcode update for example for **patching vulnerabilities**. This, obviously, must be done before executing any program. After that the CPU starts executing instructions located at a precise memory address, called the reset vector.

For Intel x86 the reset vector is at: **0xF000:FFF0**

Only 16 bytes from the top memory boundary. On IBM PCs that specific memory area is **bound to a ROM**, the so-called **BIOS**. The first fetched instruction is **ljmp \$0xF000,$0xe05b**

This starts the actual BIOS code, the long-jump also sets CS to 0xF0000.

The usual operations carried out by the BIOS are: 
- looking for **video adapters**
- **POST (Power-on Self-Test)** does peripheral check (mouse, keyboard), also checks RAM consistency, initialize the Video Card
- loads the **boot order configuration**, from the CMOS (64bytes)
- **copying itself in RAM** for a faster access (shadowing)
- **identifying the Stage 1 Bootloader** (512bytes) using the specified boot order and loading it in RAM at address **0000:7c00**
- finally the **control is given** with the instruction **ljmp \$0x0000,$0x7c00**

# Stage 1 Bootloader
## MBR
### The Master Boot Record (MBR)

The first sector (512 bytes) of the disk contains the Master Boot Record, which stores executable code and the partition table of the disk. Nowadays, with UEFI, MBR has been replaced with GPT which will we see later.

![](/AOSV/img/mbr.PNG)

![](/AOSV/img/mbr_anatomy.PNG) 

The actual code: 
```assembly

.code16
.text

.globl _start;

_start:
jmp stage1_start

OEMLabel: .string "BOOT"
BytesPerSector: .short 512
SectorsPerCluster: .byte 1
ReservedForBoot: .short 1
NumberOfFats: .byte 2
RootDirEntries: .short 224
LogicalSectors: .short 2880
MediumByte: .byte 0x0F0
SectorsPerFat: .short 9
SectorsPerTrack: .short 18
Sides: .short 2
HiddenSectors: .int 0
LargeSectors: .int 0
DriveNo: .short 0
Signature: .byte 41 #41 = floppy
VolumeID: .int 0x00000000 # any value
VolumeLabel: .string "myOS "
FileSystem: .string "FAT12 "

.stage1_start:
cli # Disable interrupts
xorw %ax,%ax # Segment zero
movw %ax,%ds
movw %ax,%es
movw %ax,%ss
...
```
