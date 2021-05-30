# Memory

## Describe the false cache sharing problem, and discuss how the Linux kernel tries to mitigate it.

In some processors between a slower memory and a High speed register is placed a cache (High speed memory). When a read to this slower memory 
is requested a portion of this memory is stored inside the cache (this in order to optimize the time for reading on the same spot of memory).
The false cache sharing problems occurs when in the same cache line there are two value (eg fst and snd in a struct) that are not logically 
relate one to the other and so, for example if we have on the same line a costant value and a counter even if the costant value is the same
during the time of the execution of the program whenever the counter is updated the cache line is uotdated and so alco the value of the 
costant value. so even if the costant value is the same each time need to be reloaded from memory when is read after that the counter is 
updated. The solution for this is project well the data structure and place as distant as possible two value that are no logically related.






## What is the role of the bootmem allocator?
The bootmem allocator is needed durind the boot phase in order to allocate portion of the memory when the proncipal memory allocator aka
Buddy allocator or the SLAB allocator are not alredy loaded and initialized. It uses a bitmap to see wich 4kb pages are free or busy. 
So even if is not as performant as the buddy and the SLAB is used ad bootime whwn there are only few request for allocating memory. 
Is required because it's unpractical to initialize all the core kernel data structure at compile time (only the memory map of the 
initial kerne image is initialized at compile time). 
After the boot the mem_init() function frees all the memory to the buddy allocator.
The bootmem allocator was replaced with the memblock allocator (from version 5+).





## What is the role of the Memblock allocator?
The memblock allocator is a method to manage the region at the early boot time. When the user kernel memory allocator (SLAB and Buddy)
are not alredy running. the memblocl dofferently to the bootmem doesn't uses a bitmap but uses collection of memory region to keep track
of allocated memory regions. Memblock sees the memory syystem as a set of continuous regions. And there are different types of this regions.
There are collection for **memory** for **reserved** and **physmem** regions. The memory regions contains the memory avauable to the kernel
may differ from the physical memory. Then there are the reserved memory, that are the memory regions alredy allocated. And then there are 
the physmem that is the representation of all the physical memory. 
After the boot the mem_init() function frees all the memory to the buddy allocator.






## Describe how the Linux kernel manages physical memory on NUMA machines, and what is the fundamental data structure used for physical frame allocation.
Today the architecture of a computer  allow to organize memory in nodes (NUMA NODES). This representation is because the memory access latency
heavely depends on the distance between CPU and the memory bank. So for example if we have more than one CPU, each CPU should use the memory node
closer to the CPU. In the linux kernel each node is represented by the struct pg_data_t and all nodes are kept into a linked list NULL terminated.
The physical memory is divided in three areas: the ZONE_DMA, the ZONE_NORMAL and the ZONE_HIGHMEM. The ZONE_DMA is mapped by the kernel in the 
lower part of the memory and is destinarted to ISA (Indistry Standard Architecture) devices (in X86 is the first 16MB). Then The ZONE_NORMAL is 
direclty mapped by the kernel into the upper region of the memory (for X86 from 16MB to 896MB). Finally the ZONE_HIGHMEM is not directly mapped 
by the kernel and for X86 start from 896MB to the end of the avaiable memory. (page table is usually located at the top begining of ZONE_NORMAL)
The fundamantal data structure used for physical frame allcoation is the **struct page**(mem_map_t), this struct is associated to each physical frame 
avayable in the system. In this struct are present the flag if the page (if locked, dirty etc..), the usage counter that need to be zero in 
order to free the page and come link to the list where all the page belongs. 






# System Calls / Interrupts

## Discuss how parameters are passed to system calls in Linux, also explaining how the used approach helps at kernel portability.







## Describe the hardware and software architecture used for interrupt delivery and management on modern Linux/x86 systems. Explain how Inter-Processor Interrupts are explicitly handled in this context, emphasizing on the differences with respect to standard interrupt management.







## Discuss what are the different types of interrupts supported by x86 CPUs. Illustrate how Linux supports the management of these interrupts.







## Discuss the Linux system call invocation path on the different flavors of x86 architectures. Describe how the kernel source base allows for kernel portability to different architectures of the system call interface







# Time

# Concurrency

## Describe how Linux supports per-CPU variables. What are the benefits of relying on this programming facility? What are the problems a programmer must face in order for their code to be correct?







# FS

## Illustrate the differences between character and block devices. Discuss how the Linux kernel allows userspace applications to interact with these classes of devices in a homogeneous way.







# Userspace / Process Management

## Illustrate what are the steps undertaken by the Linux kernel to startup a new process, following the invocation of an execve() syscall.







# Scheduling

## Discuss the main differences between the CPU scheduling algorithms in Linux 2.4 and Linux 2.6.







# Virtualization

# Security

## Describe the ring model on x86 CPUs. Illustrate what are the steps required to enable and use it, and the way modern operating systems rely on it to enforce internal security from userspace applications







## Describe the methodologies which allow to enhance the security of modern operating systems.







## Describe what are the reasons behind the development of the Linux kpatch system, and illustrate how it works






