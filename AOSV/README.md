
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
 
**The stage 1 bootloader must:**
- enable the **A20 line**
- switch to **32-bit protected mode**
- setup a **stack**
- load the kernel, but for doing that we need to navigate the filesystem so this must be done by the Stage 2 bootloader
  
##### The A20 line
The intel 80286, the successor of the 8086, increased the addressable memory to 16MB, that means **24 bits** for addresses.

For maintaining the compatibility with the programs written for the 8086 the 21th bit is forced to zero, in this way, the memory “wrap-around” when exceeds the 1MB limit. For example:

![](/AOSV/img/a20.PNG)

**For forcing the A20 to zero** the IBM decided to make a **modification on the motherboard**, in particular by using a a **spare pin of the 8042 keyboard controller. The pin has been routed to the A20 line**, so called Gate A20.

The A20 is disabled by default when the CPU starts and it must be enabled before entering in protected mode.

```assembly

call wait_for_8042
movb $0xd1, %al #command write
outb %al, $0x64
call wait_for_8042
movb $0xdf, %al # Enable A20
outb %al, $0x60
call wait_for_8042

wait_for_8042:
    inb %al, $0x64
    testb $2,%al # Bit2 set=busy
	jnz wait_for_8042
    ret

```


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

## x86 Protected Mode

The x86 protected mode was introduced with the 80286 (1982) and it was extended with memory paging in the 80386 (1985). Still today, modern PCs starts in Real Mode for backward compatibility, therefore **the Protected Mode must be enabled** during the startup.

![](/AOSV/img/x86_register.PNG) 

The **CR0** register is 32 bits long on the 386 and higher processors. On x64 processors in long mode, it (and the other control registers) is 64 bits long. CR0 has various control flags that modify the basic operation of the processor.

![](/AOSV/img/cr0_reg.PNG) 

The first action to do for entering protected mode is to set the bit 0 (PE) of CR0 to 1, but this is not enough for enabling all of the facilities. We need to set the CS and the only way to do this is to use a far jump (ljmp), then the code will execute in 32/64 bit mode.
 
```assembly
ljmp 0x0000, PE_mode

.code32

PE_mode:
    # Set up the protected-mode data segment registers
    movw $PROT_MODE_DSEG, %ax
    movw %ax, %ds
    movw %ax, %es
    movw %ax, %fs
    movw %ax, %gs
    movw %ax, %ss
```

## x86 Memory Addressing

The 8086 defined three kinds of memory addresses:
- a **logical address** that is used in the ASM code is always composed by two parts: a segment (selector) and an offset within the segment (e.g. 0xFFFF:FFFF)
- a **linear address** that in a 32bit architecture is a 32bit unsigned integer and can be used to address up to 4GB (e.g. 0x00000000 - 0xFFFFFFFF)
- a physical address that is used to address memory cells in memory chips, they correspond to the electrical signal sent along the address pins of the cpu to the memory bus.
  
Address are translated by the MMU (Memory Management Unit) set of circuits

![](/AOSV/img/mmu.PNG) 

### Segmentation 

In protected mode a segment in no longer a raw number but it contains an index to a table of segment descriptors. The table is an array containing 8-byte records of this kind:

![](/AOSV/img/segment_tables.PNG) 

There are three type of segments: **code, data and system**. The main sections are:

- **Base** a 32-bit linear address that pointing to the beginning of the segment
- **Limit** the size of the segment
- **G** the granularity (if 0 size is bytes otherwise it is a multiple of 4096)
- **DPL** the descriptor privilege a number from 0 to 3 to control the access to the segment

The Segment Descriptors are stored either in:
- the **Global Descriptor Table** (GDT) that is system wide and pointed by the register GDTR (with the size)
- the **Local Descriptor Table** (LDT) that was specific for one process and it was pointed by the register LDTR (with the size), today is not used anymore

Each segment register (CS, DS, SS, FS, GS), contains a Segment Selector (16bit). Beside of the index to the GDT also contains TI (the table indicator 0/1 = GDT/LDT) and the RPL that we will see later. Remember that a logical address is a segment selector + offset.

##### **In the Linux Kernel**

In the Linux kernel segmentation is redundant and used in very limited way, since paging is favoured. All Linux Processes running is User Mode use the user code segment **(__USER_CS)** and the user data segment **(__USER_DS)**, the ones that runs in Kernel Mode uses the kernel code segment **(__KERNEL_CS)** and the kernel data segment **(__KERNEL_DS)**. All of these segments have base 0 and max limit, therefore all processes **may use** the same logical addresses and **coincide** with the linear addresses.

![](/AOSV/img/logical_to_linear.PNG)

## x86 Privileges and Protection

We have seen that each S. Descriptor has a **DPL (Descriptor Privilege Level)**, each S. Selector has an **RPL (Requestor Privilege Level)**. We also need a current execution privilege level **(CPL)**, that describes the current privileges that the CPU has.

More in detail, the privilege fields are three:
1. **RPL** is the Requestor Privilege Level and it is present only in data segment selectors (e.g. SS, DS registers)
2. **CPL** is the Current Privilege Level and it is present only in code segment selectors (i.e. CS register that can be loaded only with ljmp/call); the CPL it’s **always equals** to the current CPU privilege level
3. **DPL** is the Descriptor Privilege Level and it is present in segment descriptors of the GDT

When enforcing memory protection? In two cases:
- when memory is accessed through a linear address
- when a data segment is loaded from a selector

![](/AOSV/img/seg_load.PNG)

#### **Gates**

Accessing a segment with a higher privilege (lower ring) with no control might allow malicious code to subvert the kernel. To transfer control, code must pass through a controlled **gate**.

Gate are represented again by descriptors. There are different kinds of gates desgriptors:
- interrupt-gate descriptors
- trap-gate descriptors
- task-gate descriptors
- (call-gate descriptors)
  
These descriptors are referenced by the **Interrupt Descriptor Table** (IDT), pointed by the IDTR register.

![](/AOSV/img/idtgdt.PNG)

**There is ine GDT per CPU core**

#### **The TSS (Task State Segment)**
The Base field of the TSS entry in the GDT (the TSSD) for the n-th CPU stored a pointer to the n-th entry of the init_tss array

According to the Intel Manual, the role of the structure is to contain all the necessary information about the current “task”.

It stores:
- processor registers
- I/O ports permissions
- Inner-level stack pointers
- a link to the previous TSS (after a context switch)

**Linux does not use hardware context switches** but it is obliged to maintain a TSS for each CPU. A TSS is maintained by the Linux kernel only for active processes. The TR register of each CPU contains the TSSD of the corresponding TSS.

##### **The TSS on x86_64 (amd64)**

On x86_64 hardware context switch is no more supported, indeed as we can see the registers are disappeared from the TSS.

#### From ring 0 to 3

![](/AOSV/img/from0to3.PNG)

## Paging

The **last step for the x86 Protected Mode, is to enable memory paging**. This is not done automatically when enabling x86 protected mode.

![](/AOSV/img/mmu.PNG) 

The Paging unit translates linear addresses to physical ones, the advantages wrt the
segmentation (that we remind is not used by the kernel) is that it offers a smaller granularity memory protection. 

The paging unit also checks the request type again the access rights of the linear address, and if access is not granted it generates a Page Fault exception.

**To enable paging we need to set up some data structures before**

The data structures that maps linear addresses to physical addresses are called page tables. The linear address, in the x86 architecture is divided as in the following figure (2 levels of indirection):

![](/AOSV/img/2lev_pages.PNG) 

Every active process must have a Page Directory, but there’s no need to allocate all the Page Tables. In the x86 paging mechanism:
- each block (PDE and PTE) is an array of 4-bytes
- we can map 1K x 1K pages
- every page is 4KB so we can address a total of 4GB

![](/AOSV/img/pd_pt_entry.PNG) 

![](/AOSV/img/paging_path.PNG) 

#### **TlB**

![](/AOSV/img/tlb.PNG) 

The Paging circuitry performs the following operations:

1. Upon a TLB miss the firmware access the page table
2. It checks the bit P (present) of the table:
   1. If it is 0 we have a page fault and a trap is risen
      1. CPU registers (incl. EIP and CS) are saved on the system stack and they will be restored returning from the trap
      2. The trap instruction is re-executed
      3. The re-execution can give rise to another trap and so on
   2. If it is 1 the page is loaded

As for example, writing to a read-only page will give rise to a trap, which is handled by the Segmentation Fault Handler.

#### **Physical Address Extension (PAE)**

The Physical Address Extension (PAE) has been introduced by Intel, starting with the Pentium Pro (1995) for increasing the RAM size support over 4GB. In practice, the address pins where increased to 36bit (max 64GB) but this required a new page indirection scheme that was increased to 3 levels. Linear addresses obviously remained of 32bit! The support to PAE is enabled by setting the PAE bit (5th bit) in the CR4 register.

**Increasing the memory pins to 64bit**, again required to extend the page indirection scheme. Increasing the memory pins to 64bit, again required to extend the page indirection scheme.
The PAE scheme is further extended with the long mode addressing. with the long mode addressing.

![](/AOSV/img/4lev_pages.PNG)


The Linux kernel allows the usage of certain pages with bigger size than 4KB, up to 1GB (e.g. useful for DBMS). 

They are listed in /proc/meminfo and /proc/sys/vm/nr_hugepages. Once enabled, huge pages can be mapped with mmap by using the flag MAP_HUGETLB.

Enabling long mode in x86_64, after we set up the proper data strictures we can enable the log mode in the following way.

![](/AOSV/img/long_mode_en.PNG)

## Stage 2 Bootloader

### GRUB & UEFI

The stage 1 bootloader (MBR) leaves the control to stage 2 bootloader which has the role of starting the kernel.