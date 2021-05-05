# Advanced Operative Systems and Virtualization

[[_TOC_]]

# Virtual File System

## Outline

1. Introduction
2. The Common File Model
   1. Operations
3. Pathname Lookup
4. Files
5. The /proc filesystem
6. The /sys filesystem
7. Device Management
   1. Char Devices
   2. Block Devices
   3.  Devices and VFS
   4.  Classes
   5.  udev


# Introduction

The **VFS** is a **software** **layer** which **abstracts** the actual implementation of the **devices** and/or the **organization** of the **files** on a storage system. The VFS exposes a uniform interface to userspace applications.

The **main** **roles** of the virtual filesytem are:
- keeping track of available filesystem types;
- **associating** (and de-associating) **devices** with instances of the appropriate filesystem.
- do any **reasonable** generic processing for **operations** involving **files**.
- when filesystem-specific operations become necessary, vector them to the filesystem in charge of the file, directory, or inode in question.


### Supported File Systems

The filesystems supported by the VFS can be grouped in:
- **Disk-based Filesystems**
- **Network Filesystems**
- **Special Filesystems** (/proc /sys)

### File System Representation

The VFS representation has a two fold nature, one in RAM and one on disk.

In **RAM** we have a **partial** or **full** **representation** of the current structure and the content of the FS.

On the **device** we have the **full** **representation** of of the current structure and the content of the FS but **possibly outdated**.


The data access and manipulation comprehends:
- a **FS-independent** part, that is the interface towards other subsystems within the kernel
- a **FS-dependent** part, that is the code for managing data in that particular filesystem

**Connecting the two parts**: any filesystem object that can be a directory, a device or a file is
**represented** in **RAM** via specific **data structures**. Each data structure **keeps a reference** to the
**functions** that **talks directly to the device**, if any. That reference is reached by means of a
kernel API interface (like read(), write(), etc.). Function pointers are used to reference
actual drivers' functions.


# The common file model



## Operations

# Pathname lookup

# Files

# The /proc filesystem

# The /sys filesystem

# Device management

## Char devices

## Block devices

## Devices and VFS

## Classes

## udev