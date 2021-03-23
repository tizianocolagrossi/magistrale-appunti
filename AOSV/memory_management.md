
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