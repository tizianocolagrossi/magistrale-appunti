# Core concept AOSV 2020-2021

## Boot sequence (Basically slide 0)
When we click the power button the OS is not magically loaded instantaneously.
The Boot sequence is:
1. BIOS/UEFI (hw setup)
2. Bootloader stage 1 (only in bios) (jump to stage 2)
3. Bootloader stage 2 (load and start the kernel)
4. Kernel (initialize the machine)
5. Init (systemd) (basic enviroinment initialization)
6. Runlevels/Targets (initualize user env)

##### BIOS/UEFI
In order to run the Kernel first we need to launch the BIOS or from the 2010 the UEFI.
The BIOS is the first program executed after booting the pc. It perform some HW check
and initialization for starting the OS. 
The UEFI perform basically the same operation of the BIOS but in a more modular way.
##### Bootloader stage 1 
Is present only in the BIOS. Is stored in the MBR (512 bytes). The role of this 
bootloader is only to run the 2nd bootloader because in the MBR there is not enough 
space to run directly the Stage 2. Instead the UEFI launch directly the Kernel (Stage 2)
##### Bootloader stage 2
The stage 2 is concretized with GRUB (or in the previous version LILO). Its role is to
launching the kernel. And usully allow the selection of the kernel image to run.
##### Kernel
Once the kernel has take control it perform some operation before passing the control 
to the end user.
- configure HW environment
- Mount the rootFS
- Configure internal DS
- Spawn the first process called init
##### init/systemd
The init process, that nowdays is replaced with systemd is the first process launched. 
It start as a daemon and it configire the software environment, loads the default runlevel
and spawn other processes. In Linux the only way to crate a new process is using the fork function.
So each process in Linux is a fork of init.
##### Runlevel/Targhets
The targets represent the state of the machine within the context of systemd. Before the use of systemd
they were also called runlevels and they where identified by numbers.
Every target has associated a set of programs or services that needs to be run.

## Real Mode and Protected Mode (X86)
##### Real Mode
The Real mode is an operative mode of all x86 compatible CPUs. The name is taken from the fact 
that in real mode adresses correspond to the real location in the physical memory. Is the mode 
that the CPU is at startup by default. In this mode the CPU is in a very basic state.
It has no cache enabled, the MMU is disabled so we cannot translate addresses from logical to linear 
and to physical. Only one core of the CPU is alive and we have no memory protecion and no privilege 
level nor multitasking. We have direct access to I/O and peripheral. Only 1MB can be addressed 
(20 bit of segmented memory), this memory wrap around. Finally we can run only 16 bit code.
##### Protected Mode
The protected mode was introduced with the 80286 (1982) and it was extended with memory paging
in the 80386 (1985). Since the CPU start in real mode the protected mode must be enabled during 
the startup.

This is done using the CR0 register. The CR0 register is a control register of the CPU and by 
modifying the bit in thos register we can modify the basic operation of the processor.
In order to enable the protected mode we need to set the 0 bit to 1 and for enabling paging
the 31th bit must be set to 1.  

## Segmented Memory
Starting from the intel 8086 the adressing memory is segmented. This means that a memory location 
is referenced by segment id and offset. The **Logical Address** can be represented by seg:offset.
There are 4 bassic 16-bit segment register CS, DS, SS, ES.

Nowdays the segmentation is still present and cannot be disabled. Each asm instruction that uses memory
implicitly uses a segment register:
- jmp uses CS
- push uses SS
  
Most of the segment addresses can be loaded with mov instruction but CS only with jmp or call.

In protected mode a segment in no longer a number but it contains an index to a table of segment descriptors.
There are three type of segments: code, data and system. And the main section on the entry of this table are:
- Base linear address to base of the segment
- Limit is the size of the segment
- DPL descriptor privilege level, number from 0 to 3 to control the access to the segment

The segment descriptor are stored either in the GDT and the LDT (global/local descriptor table),
pointed by the GDTR and LDTR. Each segment register contain a segment selector Beside of the
index to the GDT also contains TI (the table indicator 0/1 = GDT/LDT) and the RPL.

In Linux kernel the segmentation is not used but sice the segmentation canno be disbled all the segment
specified code_user code_kernel data_user data_kernel have base 0 and limit max.
therefore all processes may use the same logical addresses and coincide with the linear addresses.

## Difference between BIOS/UEFI
Both BIOS/UEFI can be used at startup of the computer to initialize the HW and to load the 
chosen Kernel. **BIOS** work by using the first sector of the hard drive (MBR limited to 512 bytes) 
wich has the next address to code. The BIOS still work in 16-bit mode, limiting the amount or
code that can be executed from the ROM. **UEFI** does the same task of the BIOS but it store 
the startup in an .efi file instead in the firmware. This file is stored into a particular 
partition called EFI System Partition (ESP). This partition also contain the bootloader program.
UEFI implement also the secure boot. UEFI can allow only authentic drivers and services to load at 
boot time, making sure that no malware can be loaded at computer startup. The UEFI has also:
- Ability to use large disks partitions (over 2 TB) with a GUID Partition Table (GPT)
- 32-bit (for example IA-32, ARM32) or 64-bit (for example x64, AArch64) pre-OS environment
- Modular design

## What BIOS does (including stage 1 and 2 bootloader)
At the startup the CPU starts executing instructions located at a precise memory called **the reset vector**.
For intel x86 is placed at: **0xF000:FFF0**. On IBM pc s that memory is bounded to a ROM the so-called BIOS

The first instruction fetched by the BIOS is: **ljmp $0xf000,$0xe05b**. This starts the actual BIOS code, 
the long-jump also sets CS to 0xf000.

The usual operation that bios does are: executing POST checks, load boot order configuration, copying itself
in ram (**shadowing**) for fast access and **identifying the Stage 1 Bootloader (512bytes) using the specifiedboot**
**order and loading it in RAM at address 0000:7c0**. Finally ljump to the stage 1 bottloader.

##### Stage 1 bootloader (MBR)
The stage 1 bootloader must enable the A20 line, switch to 32-bit protected mode, setup a stack, give control to 
stage 2 bootloader.
##### Stage 2 bootloader (GRUB&UEFI)
The stage 1 bootloader (MBR) leaves the control to stage 2 bootloader which has the role of starting the kernel.
In Linux Distributions we usually have GRUB (formerly LILO), it uses /boot/grub/grub.conf for loading the startup entries.

If we have a UEFI instead of the BIOS the UEFI boot manager takes control right after powering on the machine.
It looks at the boot configuration, loads the firmware settings from the nvRAM and then uses startup files located
in a specific FAT32 partition that must be created ad hoc (ESP - EFI System Partition).



## What is the MBR
The first sector of an hdd (512 bytes) contain the MBR, wich store **executable code** to function as a loader for the 
installed operating system, and the partition table of the disk. We have only a 384 bytes program for starting the OS!

## Privileges and Protection how it works and how are implemented (DPL CPL RPL, Gates)
The privilege in the CPU is represented as a number from 0 (high priv) to 3 (low priv). 
Increasing the ring (0->3) should be allowed instead the opposite should be denied or **controlled**.

We have three privilege fields:
- **RPL** is the RequestorPL 
- **CPL** is the CurrentPL
- **DPL** is the DescriptorPL

The memory protection is enforced in two cases: when memory is accessed through a linear address, or 
when a data segment is loaded from a selector (max(cpl, rpl)<=dpl).

Accessing a segment with a higher privilege can be done only by passing trough a **gate**.
Gates are a way to control the access to higher privilege. 
Gates are represented again by descriptors. There are different kinds of gates descriptors:
- interrupt-gate descriptors
- trap-gate descriptors
- task-gate descriptors

These descriptors are referenced by the Interrupt Descriptor Table (IDT), pointed by the IDTR register

## Paging Memory (and TLB Translation Lookaside Buffer, Little of long mode in X86_64) (TEORY)
The paging is a strategy that represent the memory as a set of pages of foxed size (usually 4kb).
A page is a set of contiguous linear address. In order to enable paging, that is required before 
entering in 32-bit protected mode, some data structures need to be set up. In the MMU there is a 
Paging unit that translare the linear addresses to physical ones. The paging permit also to have a
smaller granularity for memory protection since all the request to the paging unit are checked in
order to see if the access can be granted or not.

The data structures that need to be setup are the so called page tables. The linear address id divided
in 3 or more parts (in 3 we have 2 level of indirection). We have the directory, table and offset selector.

Every process have its own page directory, But there is no need to allocate all the page tables.

Since the translation from linear to physical address can take time in order to save time we have the 
TLB (Translation Lookaside Buffer) that store the Virtual page number to the physical page number.

## Multi Core (LAPICs and APIC Bus)
For legacy reasons, startup code is always sequential and it is executed by a single core (the master).
At startup only one core is active and the others are in idle state. In order to wake all the other core
we need to use the Local Advanced Programmable Interrupt Controller (Local-APIC) wich is used to send 
inter-processor interrupts requests (IPIs).The I/O APIC contains a redirection table which is used to 
route the interrupts it receives from peripheral buses to one or moder LAPICs.

We can use the IPIs to wakeup cores, make them execite some function and more. The IPIs can be send to 
single, all, or a subset of cores.

---
**end s 01**

---

## What the kernel does in its initial life

## Kernel Page tables (how are set, where, and what are their particularities)(initialization and bootstrapping)(PRACTICAL)

## How physical memory is managed (including the main struct, NUMA architectures)(see also s3)

## Bootmem vs Memblock allocator ( see also book )

---
**end s 02**

---

## What is the ZONE Wathermarks

## Buddy System

## What is the High Memory

## How iS done the Memory finalization

## FAST Allocators (Quicklist and SLAB)

## CPU cacheS and False Sharing problem

## Large Allocations and vmalloc

---
fst day 

**end s 03**

---

## What are the System Calls and how works (also dispatcher and parameter in x86 trough register)

## How the syscall can be invoked in x86 (int 0x80 and sysenter)

## Kernel can call a syscall? why cannot use the same approach as in the user mode (kernel wrapper routines)

## What is the vsyscall page and the vDSO 

---
**end s 04**

---

## Interrupt vs Exceptions

## How IRQs and Inter-Processor Interrupts work

## Activation Scheme for Interrupts and Exceptions

## Exception Handling (Fixup and Pagefault Handler also)

## Interrupt Handling (I/O)

## Inter-Processor Interrupts (IPIs) Handling

## Software Interrupts (SoftIRQs and Tasklet)

## Difference between SoftIRQs and Tasklet

## Difference between Tasklet and Work Queues

---
**end s 05**

---

## What are the main Clock and Time Circuits

## How Work the Timer Wheel

## Watchdogs

---
snd day

**end s 06**

---

## Memory barriers

## Spinlocks and Seqlocks

## Semaphore

## RCU

---
**end s 07**

---

## Common File Model

## /proc FS

## /sys FS

## What are the Kobjects

## Char vs Block devices

---
**end s 08**

---

## runlevels and targets

## systemd 

---
**end s 09**

---

## Describe the PCB

## How a new process is created (fork and exec) (initial step of a program)

## OOM killer 

## Process starting 

## ELF format

## Static relro vs Dynamic relro (PLT and GOT)

---
trd day

**end s 10**

---

## Describe the main Scheduler algorithm in the history of linux

## What is a scheduler

## Context Switch

---
**end s 11**

---

## Host vs Native Hypervisor

## Software-based Virtualization

## Ring De-Privileging

## Paravirtualization

## Hardware-assisted Virtualization (VT-x)

## Shadow Page Tables and Nested / Extended Page Tables (EPT)

## Containers 

## Cgroups

## Namespaces

## Container Runtimes and Docker

---
**end s 12**

---

## User Authentication

## Internet Security

## Secure Operating Systems

---
frth day

**end s 13**

---

# Lab arguments

## How to build the Kernel

## Write ASM in c

## Kernel Modules

## Hot Patching
