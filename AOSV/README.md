
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
   1. GRUB/UEFI
   2. Multi-core Support
4. **Kernel** Takes control and initializes the machine  (machine-dependent operations)
   1. Initial Life of the Linux Kernel
   2. startup_32()
   3. start_kernel()
      1. A Primer on Memory Organization
      2. Bootmem and Memblock Allocators
      3. Paging Introduction
      4. Paging Initialization
      5. TLB
      6. Final operations and recap
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

- In Linux Distributions we usually have GRUB (formerly LILO), it uses /boot/grub/grub.conf for loading the startup entries
- In Windows NT is ntldr which uses boot.ini as configuration file

The kernel image is loaded in RAM by using BIOS I/O services
- In Linux Distributions the kernel is located in **/boot/vmlinuz-\<version>**
- In Windows NT the kernel is located at **C:\Windows\System32\ntoskrnl.exe**

#### UEFI

The Unified Extensible Firmware Interface is a set of specifications of software interfaces between an operating system and the platform firmware. 

**Uefi features**
- Ability to use large disks partitions (over 2 TB) with a GUID Partition Table (GPT)
- Flexible pre-OS environment, including network capability, GUI, multi language
- 32-bit (for example IA-32, ARM32) or 64-bit (for example x64, AArch64) pre-OS environment
- C language programming
- Modular design
- Backward and forward compatibility

The UEFI boot manager takes control right after powering on the machine. It looks at the boot configuration, loads the firmware settings from the nvRAM and then uses startup files located in a specific FAT32 partition that must be created ad hoc (ESP - EFI System Partition). The partition has a folder for every boot entry (OS) and a .efi files that follows a standard path name:
- **/efi/boot/boot_x64.efi**
- **/efi/boot/bootaa64.efi**

```c

#include <efi.h>
#include <efilib.h>

EFI_STATUS
EFIAPI
efi_main (EFI_HANDLE ImageHandle, EFI_SYSTEM_TABLE *SystemTable)
{
    InitializeLib(ImageHandle, SystemTable);
    Print(L"Hello, world!\n");

    return EFI_SUCCESS;
}


```

#### GUID Partition Table (GPT)

The GUID Partition Table is a partition table standard defined within UEFI. GPT makes use of GUIDs (Globally Unique Identifiers) for identifying partitions.

![](/AOSV/img/gpt.PNG)

**A certain kind of malware can take control of the system before the OS starts (e.g. MBR Rootkits).**

These Rootkits can hijack the IDT for I/O operations in order to execute their own wrapper. Once the kernel is loaded, the rootkit notices that and patches the binary code while loading it into RAM.

UEFI overcomes this issue by allowing only signed executables by using 3 kinds of keys:
- Platform Keys (PK): tell who owns and controls the hardware platform
- Key-Exchange Keys (KEK): shows who is allowed to update the hardware platform
- Signature Database Keys (DB): show who is allowed to boot the platform in secure mode



## Multi-core support

Who shall execute the startup code? For legacy reasons, startup code is always sequential and it is executed by a single core (the master). For this reason, upon startup only one core is active and the others are in idle state. We need a way to “wake” all of the other cores.

##### Interrupts on multicore architectures

The **Advanced Programmable Interrupt Controller (APIC)** is an **interrupt controller**. Every processor has a Local-APIC which is used for sending inter-processor interrupts requests (IPIs). LAPICs are connected through a logical bus called APIC Bus and interrupts are of two types:
- LINT 0: normal interrupts
- LINT 1: non-maskable interrupts

The I/O APIC contains a redirection table which is used to route the interrupts it receives from peripheral buses to one or moder LAPICs.

![](/AOSV/img/lapic.PNG)

The **Interrupt Command Register (ICR)** is used for initiating an IPI. In that register is specified the kind of the interrupt and the target core.

![](/AOSV/img/icr.PNG)

##### INIT-SIPI-SIPI Sequence

```assembly
# address Local-APIC via register FS
mov $sel_fs, %ax
mov %ax, %fs

# broadcast 'INIT' IPI to 'all-except-self'
mov $0x000C4500, %eax ; 11 00 0 1 0 0 0 101 00000000
mov %eax, %fs:(0xFEE00300)
# wait until command is received
.B0: btl $12, %fs:(0xFEE00300)
jc .B0

# broadcast 'Startup' IPI to 'all-except-self'
# using vector 0x11 to specify entry-point
# at real memory-address 0x00011000
mov $0x000C4611, %eax ; 11 00 0 0 1 0 0 0 110 00010001
mov %eax, %fs:(0xFEE00300)
.B1: btl $12, %fs:(0xFEE00300)
jc .B1
```

## Kernel Boot

### Initial Life of the Linux Kernel

The stage 2 of the Bootloader (or UEFI) loads in RAM the image of the kernel, but this image is really different from the one that we have at steady state. **We remind thet the CPU starts it in Real Mode** with 1Mb of addressable memory. So **where is the entry point?** and **when** the kernel **switch to Protected Mode?**

![](/AOSV/img/real_protected_flow.PNG)

The first istruction executed is a 2_byte jump to **start_of_setup** directly written in machine code.

```assembly

   .globl _start

_start:
         # Explicitly enter this as bytes, or the assembler
		   # tries to generate a 3-byte jump here, which causes
		   # everything else to push off to the wrong offset.
		   .byte	0xeb		# short (2-byte) jump
		   .byte	start_of_setup-1f

```

#### **start_of_setup()**

The **start_of_setup()** routine is a little routine that makes some initial setup:
- set up **stack**
- zeroes the **.bss** section
- jump to **main()** in /arch/x86/boot/main.c

**in this portion of code the wernEr still run in real mode**, and the function implements part of the [Kernel Boot Protocol](http://lxr.linux.no/linux+v2.6.25.6/Documentation/i386/boot.txt). (for examle it loads the boot option in memory).

#### **main()**

The **main()** function prepare the machine to enter protected and then to the switch. And so it:

- enable **A20** line
- setup the IDT (Interrupt desc tab) and the GDT (Global desc tab)
- setup memory, asks BIOS which is the available memory for creating a physical address map. As a general rule, the kernel is installed in RAM starting from the physical address 0x00100000, i.e. from the second megabyte. For kernel 2.6, a typical amount of required RAM is 3MB.

In the end the function calls **go_to_protected_mode()** in arch/x86/boot/pm.c

#### **go_to_protected_mode()**

```c

/*
 * Actual invocation sequence
 */
void go_to_protected_mode(void)
{
	/* Hook before leaving real mode, also disables interrupts */
	realmode_switch_hook();

	/* Enable the A20 gate */
	if (enable_a20()) {
		puts("A20 gate not responding, unable to boot...\n");
		die();
	}

	/* Reset coprocessor (IGNNE#) */
	reset_coprocessor();

	/* Mask all interrupts in the PIC */
	mask_all_interrupts();

	/* Actual transition to protected mode... */
	setup_idt();
	setup_gdt();
	protected_mode_jump(boot_params.hdr.code32_start,
			    (u32)&boot_params + (ds() << 4));
}

```

##### Interrupt Descriptor Table
In real mode the Interrupt Vector Table is always at address 0. The IDTR register is set up in the following way:

```c

/*
 * Set up the IDT
 */
static void setup_idt(void)
{
	static const struct gdt_ptr null_idt = {0, 0};
	asm volatile("lidtl %0" : : "m" (null_idt));
}

```

##### Global Descriptor Table 

```c

static void setup_gdt(void)
{
	/* There are machines which are known to not boot with the GDT
	   being 8-byte unaligned.  Intel recommends 16 byte alignment. */
	static const u64 boot_gdt[] __attribute__((aligned(16))) = {
		/* CS: code, read/execute, 4 GB, base 0 */
		[GDT_ENTRY_BOOT_CS] = GDT_ENTRY(0xc09b, 0, 0xfffff),
		/* DS: data, read/write, 4 GB, base 0 */
		[GDT_ENTRY_BOOT_DS] = GDT_ENTRY(0xc093, 0, 0xfffff),
		/* TSS: 32-bit tss, 104 bytes, base 4096 */
		/* We only have a TSS here to keep Intel VT happy;
		   we don't actually use it for anything. */
		[GDT_ENTRY_BOOT_TSS] = GDT_ENTRY(0x0089, 4096, 103),
	};
	/* Xen HVM incorrectly stores a pointer to the gdt_ptr, instead
	   of the gdt_ptr contents.  Thus, make it static so it will
	   stay in memory, at least long enough that we switch to the
	   proper kernel GDT. */
	static struct gdt_ptr gdt;

	gdt.len = sizeof(boot_gdt)-1;
	gdt.ptr = (u32)&boot_gdt + (ds() << 4);

	asm volatile("lgdtl %0" : : "m" (gdt));
}

```

#### protected_mode_jump()

After setting the initial IDT and GDT, the kernel jumps to protected mode via **protected_mode_jump()** in arch/x86/boot/pmjump.S. This routine:
- sets PE in CR0
- issues a ljmp to its very next instruction to load in CS the boot CS sector
- sets up a data segment for flat 32-bit mode
- sets up a temporary stack

```c

movl	%cr0, %edx
	orb	$X86_CR0_PE, %dl	# Protected mode
	movl	%edx, %cr0

	# Transition to 32-bit mode
	.byte	0x66, 0xea		# ljmpl opcode
2:	.long	.Lin_pm32		# offset
	.word	__BOOT_CS		# segment

```

#### startup_32() #primary

**startup_32() #primary** jumps into startup_32() in arch/x86/boot/compressed/head_32.S and this routine does the following:

- sets the segments to known values (__BOOT_DS)
- loads a new stack
- clears again the BSS section
- determines the actual position in memory via a call/pop (image below)
- calls extract_kernel() (previously named decompress_kernel())

```c

/*
 * Calculate the delta between where we were compiled to run
 * at and where we were actually loaded at.  This can only be done
 * with a short local call on x86.  Nothing  else will tell us what
 * address we are running at.  The reserved chunk of the real-mode
 * data at 0x1e4 (defined as a scratch field) are used as the stack
 * for this calculation. Only 4 bytes are needed.
 */
	leal	(BP_scratch+4)(%esi), %esp
	call	1f
1:	popl	%edx
	addl	$_GLOBAL_OFFSET_TABLE_+(.-1b), %edx


```

#### KASLR (Kernel Address Space Layout Randomization)

In order to prevent that an attacker patches the kernel memory image, at the boot time the kernel **randomly choses** where to decompress itself in memory replying on the most accurate source of entropy avaiable. However, since the kernel is mapped using 2Mb aligned pages, the number of valid slot is limited.

*The current layout of the kernel's virtual address space only leaves **512M for the kernel code** and 1.5G for modules. Since there is no need for that much module space, his patches reduce that to 1G, leaving 1G for the kernel, thus 512 possible slots (as it needs to be 2M aligned). The number of slots may increase when the modules' location is added to KASLR.*

### startup_32() #secondary
After the decompression the true image of kernel can run, and this is done by a jump to startup_32() at arch/x86/kernel/head_32.S. **This routine sets up the environment for the first Linux process (process 0):**

- initializes the segmentation registers with their final values
- clears again the bss
- builds the page table
- enables paging
- creates the final IDT
- jumps to the architecture-dependent kernel entry point (i.e. start_kernel() at init/kernel.c

#### Memory

During the initialization the steady-state kernel must take control of the available physical memory. This because it will have to manage it with respect to the virtual address spaces of all processes, in particular it needs to be able to:
- allocate and deallocate memory
- swap

For this reason, upon starting, the kernel must have an early organization setup out of the box. For this reason the kernel use a set of statically generated page tables.

**On 32bit architecture, the process address space is divided in two parts:**
- from 0x00000000 to 0xbfffffff (3GB) addressed when User or Kernel Mode
- from 0xc0000000 to 0xffffffff (1GB) addressed when Kernel Mode

What should be kept in mind is that addresses lower than 0xc0000000 (value often referred as PAGE_OFFSET) depend on the specific process, the others **are the same for every process** and
**equal to the corresponding entries of the Master Kernel Page General Directory.**

##### Provisional Kernel Page Tables

A provisional **Page Global Directory (PGD)** is initialized statically during the kernel compilation, while the provisional **Page Tables** are initialized by **startup_32().**

The Page **Global Directory (PGD)** is stored in the **swapper_page_dir** variable. Now suppose that all the kernel segments, the provisional page tables and the dynamic area fits 8MB of RAM. In the early paging, with pages of size 4MB we needed 2 entries in the Page Table.

Now, the **objective of this phase** of paging is to **allow these 8MB of RAM to be easily addressed both in real mode and protected mode**. Therefore the kernel must create a mapping from both the linear address 0x00000000 through 0x007fffff and the linear addresses 0xc0000000 through 0xc07fffff into the physical 0x00000000 through 0x007fffff.

##### Enabling Paging

![](/AOSV/img/enable_paging.PNG)

##### Kernel-Level MM Data Structures

The main data structures for memory management in the kernel are:
- **Kernel Page Tables**, that keeps the memory mapping for kernel level code and data, it will pointed by swapper_pg_dir
- **Core Map**, that keeps the status information for any frame (or page) of the physical memory and the free memory frames for any NUMA node

### start_kernel()

![](/AOSV/img/kernel_flow.PNG)

**start_kernel() **executes on a single core (the master). All of the other cores keep waiting that the master has finished. 

The start_kernel() function is declared as
```c
asmlinkage __visible void __init __no_sanitize_address start_kernel(void)
```

- **asmlinkage** tells the compiler that the calling convention is such that parameters are passed on stack
- **__visible** prevent Link-Time Optimization (since gcc 4.5)
- **__init** tells the kernel that the function is only used at initialization phase so memory can be freed’ after
- **__no_sanitize_address** prevent address sanitizing (since gcc 4.8)


The main operation of this function are:
1. **setup_arch()** that initializes the architecture
2. **build_all_zonelists()** - builds the memory zones
3. **page_alloc_init()** / **mem_init()** - the steady state allocator (Buddy System) is initialized and the boot one removed
4. **sched_init()** - initializes the scheduler
5. **trap_init()** - the final IDT is built
6. **time_init()** - the system time is initialized
7. **kmem_cache_init()** - the slab allocator is initialized
8. **arch_call_rest_init()** / **rest_init()** - prepares the environment
   1. kernel_thread(kernel_init) - starts the kernel thread for process 1 is created
      1. kernel_init_freeable() -> prepare_namespace() -> initrd_load() - mounts the initramfs, a temporary filesystem used to start the init process
      2. run_init_process() -> kernel_execve() - Execute /bin/init
   2. cpu_startup_entry() -> do_idle() - starts the idle process
   
##### setup_arch()
The main operations carried out by setup_arch() (/arch/x86/kernel/setup.c) are:
1. **load_cr3()** - initializes kernel page tables
2. **__flush_tlb_all()** - flush the TLB
3. **init_bootmem()** - initializes the bootmem allocator (v < 5)
4. **e820__memory_setup()** / **e820__reserve_resources()** - initializes the available memory (also for memblock allocator)
5. **x86_init.paging.pagetable_init()** -> **native_pagetable_init()** -> **paging_init()** - initializes paging

#### A Primer on Memory Organization

##### NUMA

Linux is avaiable for a great number of architecturs, and so a **machine independent** way for describing the memory is needed.

Large scale machines memory may be arranged into banks. A memory bank can be assigned to each CPU, or a bank can be suitable for Direct Memory Access (DMA) near the devices.

In linux each of this banks is called **node** and the *concept of addressing* the memory in nosed is called Non-Uniform Memory Access (**NUMA**) (with single node we have a UMA architecture). 

Each node is represented by the **struct pg_data_t** and all nodes are kept in linked list

![](/AOSV/img/numa.PNG)

###### Zones

Each node is divided in a number of blocks called zones. On x86 there are three kinds of zone:

- **ZONE_DMA is directly mapped** by the kernel in the lower part of memory and it is destined to ISA (Industry Standard Architecture) devices, in x86 first 16 MB
- **ZONE_NORMAL is directly mapped** by the kernel into the upper region of the linear address space, in x86 from 16MB to 896MB
- **ZONE_HIGHMEM** is the remaining available memory and it is **not directly mapped** by the kernel, in x86 from 896MB to end of memory.

The **Page table is usually located** at the top beginning of **ZONE_NORMAL**. This ZONE_NORMAL is fixed in size, addressing 16GiB can require 176MB of data structures!

![](/AOSV/img/zones.png)

#### Bootmem and Memblock Allocators

