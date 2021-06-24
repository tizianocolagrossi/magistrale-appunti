# Core concept AOSV 2020-2021

## Boot sequence (Basically slide 0)

## Real Mode and Protected Mode (X86)

## Segmented Memory

## Difference between BIOS/UEFI

## What BIOS does (including stage 1 and 2 bootloader)

## What is the MBR

## Privileges and Protection how it works and how are implemented (DPL CPL RPL, Gates)

## Paging Memory (and TLB Translation Lookaside Buffer, Little of long mode in X86_64) (TEORY)

## Multi Core (LAPICs and APIC Bus)

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
**end s 13**

---

# Lab arguments

## How to build the Kernel

## Write ASM in c

## Kernel Modules

## Hot Patching
