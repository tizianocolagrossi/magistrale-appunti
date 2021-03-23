
# Advanced Operative Systems and Virtualization
# Memory Management

## Outline
1. **Memory Representation**
2. **The Buddy System**
3. **High Memory**
4. **Memory Finalization**
5. **Steady-state memory allocation**
   1. Fast Allocations & Quicklists
   2. SLAB Allocator
   3. CPU Caches
   4. Large Allocations & vmalloc
6. **User & Kernel Space**


# Memory Management

During the boot, the Kernel relies on a **temporary** memory manager. (During the boot, the Kernel relies on a temporary memory manager. The rationale is that there are not many memory requests during the boot)

At steady state the **boot allocator can no more be used**, because **allocations/deallocations are frequent** and memory must be used wisely, accounting for hardware performance.

## NUMA (Non-Uniform Memory Access)
As anticipated, in modern computer architectures allows to see **memory organized in nodes**. This because the momory access latency heavily depends on the distance between the CPU and the memory banks.

###### little recap
In the Linux Kernel **each node** is represented by the **struct pg_data_t** and all nodes are kept in a NULL terminated **linked list called pgdat_list**. Each node is linked to the other with the field **pg_data_t->node_next**. In **UMA** architectures we **only one pg_data_t** referenced by **contig_page_data**.

```c
typedef struct pglist_data {
    zone_t node_zones[MAX_NR_ZONES];
    zonelist_t node_zonelists[GFP_ZONEMASK+1]; // Preferred zone allocation order
    int nr_zones;
    struct page *node_mem_map; // Pointer to the first page of the array of frames of the node
    unsigned long *valid_addr_bitmap;
    struct bootmem_data *bdata;
    unsigned long node_start_paddr; // Starting physical address of the node (PFN)
    unsigned long node_start_mapnr;
    unsigned long node_size; // Total number of pages in the node
    int node_id;
    struct pglist_data *node_next;
} pg_data_t;
```

## Zone
Each node is divided in a number of blocks called zones. A zone is described by the **struct zone_struct typedef as zone_t**. On x86 there are
three kinds of zone:
- **ZONE_DMA** directly mapped by the kernel in the lower part of memory, it is **destined to ISA** (Industry Standard Architecture) devices, in x86 first 16 MB.
- **ZONE_NORMAL** is directly mapped by the kernel into the upper region of the linear address space. In x86 from 16MB to 896MB.
- **ZONE_HIGHMEM** is the **remaining** available **memory** and it is **not directly mapped by the kernel**.

The **Page table** is usually **located** at the **top beginning of ZONE_NORMAL**. To access memory between 1GB and 4GB the kernel temporarily maps pages from high memory to **ZONE_NORMAL** (ZONE_NORMAL is fixed in size).


|              | x86                | x86_64          |
|--------------|--------------------|-----------------|
| ZONE_DMA     | First 16MB         | First 16MB      |
| ZONE_DMA32   | -                  | First 4GB       |
| ZONE_NORMAL  | From 16MB to 896MB | From 4GB to end |
| ZONE_HIGHMEM | From 896MB to end  | -               |
| ZONE_MOVABLE | User Defined       | User Defined    |


Please remind that for example in x86_64 if we have only 2GB of RAM, all the RAM will be ZONE_DMA32. If instead we have 16GB the kernel will allocate memory by following possible flags you pass and the available memory type. See also /proc/pagetypeinfo.

![](/AOSV/img/zones_86_8664.PNG)

Zones are initialized after the kernel page tables have been fully set up by **paging_init()**. The goal is to determine what parameters to send to:
- **free_area_init()** for UMA machines
- **free_area_init_node()** for NUMA machines

```c
typedef struct zone_struct {
    spinlock_t lock;
    unsigned long free_pages;
    zone_watermarks_t watermarks[MAX_NR_ZONES];
    unsigned long need_balance;
    unsigned long nr_active_pages,nr_inactive_pages;
    unsigned long nr_cache_pages;
    free_area_t free_area[MAX_ORDER];
    wait_queue_head_t *wait_table;
    unsigned long wait_table_size;
    unsigned long wait_table_shift;
    struct pglist_data *zone_pgdat;
    struct page *zone_mem_map;
    unsigned long zone_start_paddr;
    unsigned long zone_start_mapnr;
    char *name;
    unsigned long size;
    unsigned long realsize;
} zone_t;
```

## Zone Watermarks
When **available** **memory** in the system is **low**, the pageout **daemon kswapd** is woken up to **start freeing pages**.

Each **zone** has **three watermarks** called **pages low**, **pages min** and **pages high**, which help track how much pressure a zone is under.
- **pages low** When the pages low number of free pages is reached, **kswapd** is **woken** **up** by the buddy allocator
- **pages min** When pages min is reached, the **allocator** will do the **kswapd work** in a **synchronous** fashion
- **pages high** After kswapd has been woken to start freeing pages, it will not consider the zone to be “balanced” when pages high pages are free

![](/AOSV/img/zone_wAtermarks.PNG)

## Core Map

The Core Map is an **array of mem_map_t** structures defined in include/linux/mm.h and kept in **ZONE_NORMAL**. **The struct page is associated to every physical frame available in the system.**

```c
// page flags
#define PG_locked 0
#define PG_referenced 2
#define PG_uptodate 3
#define PG_dirty 4
#define PG_lru 6
#define PG_reserved 14

typedef struct page {
    struct list_head list; // List head to which the page belongs. A page may belong to different lists
    struct address_space *mapping; // Address space (e.g. inode)
    unsigned long index; // index to which the page belongs
    struct page *next_hash; 
    atomic_t count; // Usage counter, if zero the page may be free’d
    unsigned long flags; // Page flags:
    struct list_head lru;
    struct page **pprev_hash;
    struct buffer_head * buffers;
#if defined(CONFIG_HIGHMEM) || defined(WANT_PAGE_VIRTUAL)
    void *virtual;
#endif /* CONFIG_HIGMEM || WANT_PAGE_VIRTUAL */
} mem_map_t;
```

### How to manage flags

![](/AOSV/img/manage_page_flags.PNG)

### On UMA
Initially we have the **core map pointer, mem_map** defined in **mm/memory.c**. The **pointer initialization** is done within the function **free_area_init()**. After the initialization each entry will keep the value 0 within the count field and the value 1 into flags for the **PG_RESERVED** flag. Therefore we do not have any virtual reference to the frame and the frame is reserved. The un-reserving is done by the **mem_init()** function.

### On NUMA
There’s **not a global mem_map array** since **every node** keeps its **own map** in its own memory. The **map** is **pointed** by **pg_data_t -> node_mem_map** but the **map organization** is the **same**.

# The Buddy System
The **kernel subsystem** that **handles** the **memory allocation** for contiguous page frames is called **zoned page frame allocator**.

![](/AOSV/img/zoned_page_frame_allocator.PNG)

## Fragmentation
When allocating groups of contiguous page frames, the algorithm that we need to design, must deal with a well-know problem called **External Fragmentation**. 

We allocate #1, #2, #3,
#4 and #5 consecutively, then we deallocate #2 and #4. Where can we put a new allocation request for a size of #2 + #4 for example? We have that memory available but it is **not** **contiguous**.

```
[--#1--][#2][-#3-][#4][--------#5--------]
[--#1--]    [-#3-]    [--------#5--------]

        [#2][#4]
        [  #?  ]
```

There are two approaches to solve the problem:
1. **paging circuitry** to **map** group of **non-contiguous pages** into intervals of **contiguous linear addresses**
2. develop a suitable technique to **keep track** of the existing blocks of **free contiguous page frames**, **avoiding** as much as possible **the need to split up large free block** to satisfy a request for a smaller one

The Linux kernel prefers the second, for 3 good reasons:
- in **some cases we really need contiguous pages**, not only contiguous linear addresses (e.g. DMA)
- **frequent page table modifications** lead to **higher average memory access times**, e.g. flushing the TLB
- **large chunks of physical memory** can be accessed with 4MB pages, **reducing TLB miss** and speeding up access times

## Buddy System
