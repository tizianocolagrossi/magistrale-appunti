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

**The key idea behind the VFS is to introduce a common file model capable of representing all the possible filesystems.** This means that each physical filesystem implementation must translate its physical organization into the VFS’s common file model.

The **Common File Model** consists of the following “*object*” types:

- **superblock**
Stores the information concerning a mounted filesystem, this object **corresponds** to a
**filesystem control block** stored on disk
- **inode**
Stores general information about a specific file, this **corresponds** to to a **file control block**
stored on disk, each inode has a unique number associated to it
- **file**
Stores the **information** about the **interaction** between an **open file and a process**, this
exists **only in kernel memory** when a process opens a file
- **dentry**
Stores the **information** about the **linking** of a **directory** entry with the **corresponding** **file**,
each FS stores this information in its own particular way.

![](img/common_file_model.PNG)

![](img/common_file_model_str.PNG)

### Filesystem types

The *file_system_type* structure describes a file system (it is defined in include/linux/fs.h), it keeps information related to:
- the file system name
- a pointer to a function to be executed upon mounting the file system (superblock-read)

#### ramfs

Ramfs is a very simple filesystem that exports Linux's disk caching mechanisms (the page
cache and dentry cache) as a dynamically resizable RAM-based filesystem.

With ramfs, **there is no backing store**. Files written into ramfs allocate dentries and page
cache as usual, but there's nowhere to write them to.

**Ramfs can eat up all the available memory:**
- tmpfs is a derivative, with size limits
- only root should be given access to ramfs


#### rootfs

Rootfs is a special instance of ramfs (or tmpfs, if that's enabled), which is always present in 2.6 systems.

It provides an empty root directory during kernel boot. Rootfs cannot be unmounted and this has the same idea behind the fact that init process cannot be killed.

During kernel boot, another (actual) filesystem is mounted over rootfs (remember initramfs/initrd).


### File system mounting

In most traditional Unix-like kernel, each filesystem can be mounted once, the command used is for instance

```mount -t ext4 /dev/sda1 /mnt```

However in Linux it is possible to mount the same filesystem n times, this means that its root
directory can be accessed through n mount points. This means that each mount point
(represented by the struct vfsmount) will point to the same superblock

Mounted filesystems form a hierarchy: the mount point of a filesystem might be the directory
of a second filesystem, which in turn is already mounted over a third filesystem and so on.
```c
struct vfsmount {
	struct list_head mnt_hash;
	struct vfsmount *mnt_parent;	/* fs we are mounted on */
	struct dentry *mnt_mountpoint;	/* dentry of mountpoint */
	struct dentry *mnt_root;	/* root of the mounted tree */
	struct super_block *mnt_sb;	/* pointer to superblock */
#ifdef CONFIG_SMP
	struct mnt_pcp __percpu *mnt_pcp;
	atomic_t mnt_longterm;		/* how many of the refs are longterm */
#else
	int mnt_count;
	int mnt_writers;
#endif
	struct list_head mnt_mounts;	/* list of children, anchored here */
	struct list_head mnt_child;	/* and going through their mnt_child */
	int mnt_flags;
	/* 4 bytes hole on 64bits arches without fsnotify */
#ifdef CONFIG_FSNOTIFY
	__u32 mnt_fsnotify_mask;
	struct hlist_head mnt_fsnotify_marks;
#endif
	const char *mnt_devname;	/* Name of device e.g. /dev/dsk/hda1 */
	struct list_head mnt_list;
	struct list_head mnt_expire;	/* link in fs-specific expiry list */
	struct list_head mnt_share;	/* circular list of shared mounts */
	struct list_head mnt_slave_list;/* list of slave mounts */
	struct list_head mnt_slave;	/* slave list entry */
	struct vfsmount *mnt_master;	/* slave is on master->mnt_slave_list */
	struct mnt_namespace *mnt_ns;	/* containing namespace */
	int mnt_id;			/* mount identifier */
	int mnt_group_id;		/* peer group identifier */
	int mnt_expiry_mark;		/* true if marked for expiry */
	int mnt_pinned;
	int mnt_ghosts;
};
```

```c
struct super_block {
	struct list_head	s_list;		/* Keep this first *///////////////////   
	dev_t			s_dev;		/* search index; _not_ kdev_t */
	unsigned char		s_dirt;
	unsigned char		s_blocksize_bits;
	unsigned long		s_blocksize;
	loff_t			s_maxbytes;	/* Max file size */
	struct file_system_type	*s_type; //////////////////////////////////////
	const struct super_operations	*s_op; ////////////////////////////////
	const struct dquot_operations	*dq_op;
	const struct quotactl_ops	*s_qcop;
	const struct export_operations *s_export_op;
	unsigned long		s_flags;
	unsigned long		s_magic;
	struct dentry		*s_root; //////////////////////////////////////////
	struct rw_semaphore	s_umount;
	struct mutex		s_lock;
	int			s_count;
	atomic_t		s_active;
#ifdef CONFIG_SECURITY
	void                    *s_security;
#endif
	const struct xattr_handler **s_xattr;

	struct list_head	s_inodes;	/* all inodes */
	struct hlist_bl_head	s_anon;		/* anonymous dentries for (nfs) exporting */
#ifdef CONFIG_SMP
	struct list_head __percpu *s_files;
#else
	struct list_head	s_files;
#endif
	/* s_dentry_lru, s_nr_dentry_unused protected by dcache.c lru locks */
	struct list_head	s_dentry_lru;	/* unused dentry lru */
	int			s_nr_dentry_unused;	/* # of dentry on lru */

	struct block_device	*s_bdev;
    //...
    //...
}
```

```c
struct dentry {
	/* RCU lookup touched fields */
	unsigned int d_flags;		/* protected by d_lock */
	seqcount_t d_seq;		/* per dentry seqlock */
	struct hlist_bl_node d_hash;	/* lookup hash list */
	struct dentry *d_parent;	/* parent directory */ //////////////////////
	struct qstr d_name; /////////////////////////////////////////////////////
	struct inode *d_inode;		/* Where the name belongs to - NULL is //////
					 * negative */
	unsigned char d_iname[DNAME_INLINE_LEN];	/* small names */

	/* Ref lookup also touches following */
	unsigned int d_count;		/* protected by d_lock */ ///////////////////
	spinlock_t d_lock;		/* per dentry lock */
	const struct dentry_operations *d_op; ///////////////////////////////////
	struct super_block *d_sb;	/* The root of the dentry tree */ ///////////
	unsigned long d_time;		/* used by d_revalidate */
	void *d_fsdata;			/* fs-specific data */

	struct list_head d_lru;		/* LRU list */
	/*
	 * d_child and d_rcu can share memory
	 */
	union {
		struct list_head d_child;	/* child of parent list */
	 	struct rcu_head d_rcu;
	} d_u;
	struct list_head d_subdirs;	/* our children *////////////////////////////
	struct list_head d_alias;	/* inode alias list */
}; 
```

```c
struct inode {
	/* RCU path lookup touches following: */
	umode_t			i_mode;
	uid_t			i_uid;
	gid_t			i_gid;
	const struct inode_operations	*i_op; //////////////////////////////////
	struct super_block	*i_sb; //////////////////////////////////////////////

	spinlock_t		i_lock;	/* i_blocks, i_bytes, maybe i_size */
	unsigned int		i_flags;
	struct mutex		i_mutex;

	unsigned long		i_state; ///////////////////////////////////////////
	unsigned long		dirtied_when;	/* jiffies of first dirtying */

	struct hlist_node	i_hash;
	struct list_head	i_wb_list;	/* backing dev IO list */
	struct list_head	i_lru;		/* inode LRU list */
	struct list_head	i_sb_list;
	union {
		struct list_head	i_dentry;
		struct rcu_head		i_rcu;
	};
	unsigned long		i_ino;
	atomic_t		i_count; /////////////////////////////////////////////////
	unsigned int		i_nlink;
	dev_t			i_rdev;
	unsigned int		i_blkbits;
	u64			i_version;
	loff_t			i_size;
#ifdef __NEED_I_SIZE_ORDERED
	seqcount_t		i_size_seqcount;
#endif
	struct timespec		i_atime;
	struct timespec		i_mtime;
	struct timespec		i_ctime;
	blkcnt_t		i_blocks;
	unsigned short          i_bytes;
	struct rw_semaphore	i_alloc_sem;
	const struct file_operations	*i_fop;	//////////////////////////////////
                     /* former ->i_op->default_file_ops */ 
	struct file_lock	*i_flock;
	struct address_space	*i_mapping;
	struct address_space	i_data;
    //...
    //...
}
```

Each inode can always appear in one of the following **circular doubly linked lists**:

- list of valid **unused** inodes, they are mirroring on disk but they are not used by any process, they are not dirty and i_count is 0
- list of **in-use** inodes, they are mirroring on disk and used by some process, they are not dirty and i_count > 0
- list of **dirty** inodes

Moreover, inodes objects are also included in a **hash table** that speeds up the search of the
inode object when the kernel knows both the inode number and the address of the superblock
corresponding to the FS that includes the file.

### VFS and PCB

In the PCB, struct fs_struct *fs points to information related to the current directory and
the root directory for the associated process. fs_struct is defined in include/fs_struct.h

```c
struct fs_struct {
    int users;
    spinlock_t lock;
    seqcount_t seq;
    int umask;
    int in_exec;
    struct path root, pwd; |--
} __randomize_layout;        |
                             |
struct path { <---------------
    struct vfsmount *mnt;
    struct dentry *dentry;
} __randomize_layout;


```

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