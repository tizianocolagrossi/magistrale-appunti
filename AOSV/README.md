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
System calls are special function. The parameters are alway passed trought the CPU register because the system calls are function that 
cross user and kernel space, and for this reason neighter the user mode stack nor the kerner stack can be used. The syscall dispathcer 
then copies the paramethers from the CPU registers into the kernel stack.  In a X86 machine the register are used followind this order
here: eax, ebx, ecx, edx, esi, edi and ebp. And this is usefoul for portability because the dispatcher then place the register in the 
right spot in the stack and this help for portability reason.






## Discuss the Linux system call invocation path on the different flavors of x86 architectures. Describe how the kernel source base allows for kernel portability to different architectures of the system call interface
On the x86 architecture the syscalls can be invoked using the INT 0x80 instruction or the SYSENTER instruction 
(supported from the kernel 2.6 after pentium II). The handlers for the two methods are system_call() and sysenter_entry(). 
The INT 0x80 instruction is registered as a trap gate and this instruction is slow because need to perform all the security check for 
an interrupt. Instead the sysenter instruction is more fast (is also called Fast System Call by intel) because it provides a faster way 
to switch from user mode and kernel mode using three MSR register. Obviously a libc wrapper can call the sysenter instruction only if the 
CPU and the kernel support it and in order to doing that the libc invoke the __kernel_vsyscall() in the vsyscall page where is placed at 
boot time the more performant way to invoke a syscall.
When a User mode process invoke a syscall the CPU switch to Kernel mode and start the execution of a kernel function. Both the INT 0x80
and the sysenter instruction end with a jump to the syscall handler (dispatcher). Each syscall is identified by a number (in x86 in eax register) 
the dispatcher use this identifier by searching in the syscall table to identify the right syscall routine. The syscall handler is similar 
to other exception handler. The syscall handler forst store the context of most of the register, then via a call invoke the C function 
(syscall service routine find by the dispatcher searching into the syscall table) then after the syscall ser routine and the execution 
the user mode context is Restored with in eax the output of the syscall. The paramether of the syscall are passed all (up to 6) in the CPU
register because the syscall is a function that cross user and kernel mode and so neighter the user nor kernel stack can be used. This
is usefoul also for portability reason because only a little portion of the code change from changing the architecture of the machine
(the portion of store and restore the registers) 







## Describe the hardware and software architecture used for interrupt delivery and management on modern Linux/x86 systems. Explain how Inter-Processor Interrupts are explicitly handled in this context, emphasizing on the differences with respect to standard interrupt management.
HW point of view: Each HW device capable of issuing interrupt REQ usually has a single output line designated ad Interrupt Request Line
This line basically has two state. All these line are connected to the input pins of an circuit called Programmable Interrupt Controller
wich perform the action needed to control the IRQs. It monitor the line and is a signal is raised in a IRQ line the signal is elaborated
and than is send to the INTR pin of the CPU, the PIC waits until the CPU ack the signal. In the SW point of view instead the interrupt signal
are controlled after each asm instruction and if a signal is raised then create a switch (if we are in user mode ) to kernel mode and is handled
the Interrupt ( we can have also a nested excution when a non critical section of an interrupt handler is interrupted from another IRQs).
The interrupt handler is selected similarly to the syscall handler. But due to the fact that in this case more device can share the same IRQ line 
the CPU runs at cascade all the handler of the devices that uses that IRQ line Till the CPU execute the right handler. Before some control 
must be done for security reason where the CPL and segment DPL and gate DPL are checkedin order to procede. (CPL < seg DPL or gate DPL < CPL). 
The interrupt handler run at the expenses of the user process interrupted and since the interrupt can come at any time the interrupt handling 
must ho out as soon as possible. For this the interrupt handling process is always split in two part a critical part and a deferrable part. 
IPIs are some particular interrupts. Since nowdays we have multiple core in a CPU the IPIs are some interrupt created in order to propagate 
interrupt to other cores. they are syncronous at the sender but asyncrhonous at the receiver. The main role for the IPIs is to startup core,
forcing a core to run a function, invalidate a tlb line and other. Each core has a Local APIC (advanced interrupt controller) with this apic
the i/o interrupt can be managed using a round robin fashon or a smarter way, sending the interrupt to the core that are sunning the lower 
proority task. The Ipis can be send to all core or sended only to a subset or a single core. 






## Discuss what are the different types of interrupts supported by x86 CPUs. Illustrate how Linux supports the management of these interrupts.
There are two kind of interrupt synch (generated after instruction or error) and async (i/o int) these interrupt are generally electrical signal.
Each device that can generate an interrupt request has an interrupt line, all the line of the devices are connected to the PIC Programmable Interrupt
Controller that receive the interrupt and than propagate the high priority interrupt to the INTR pin of the CPU than need to ACK the signal in order
to permit to lower the signal by the PIC and can raise another signal. There are I/O interrups and loval interrupts. The local interrupt (IPI) are handled
by the local APIC that each core has. 
The CPU control if a signal is raised after each instruction (id the interrupt are not disabled by CLI and renabled by STI). Than the CPU runs all the 
handler associated to that IRQ line till it find the device that has generated that interrupt. before some check must be done to check if the CPL < seg DPL
or if is a gate if gate DPL < than CPL. 
the i/o interrupt can be handled or in a round robin fashon by the core or can be selected the core that is running the task with lower priority.
the ipis are handled by the APIc that are Advanced PIC present on each core in the cpu. The IPIs can be used to force the scheduled of a function in a core, 
or wake up a core, or flish a tlb line and more. This kind of interrups (IPIs) can be send to all the cores or a subset of the cores in the CPU.







# Time

# Concurrency

# FS

## Illustrate the differences between character and block devices. Discuss how the Linux kernel allows userspace applications to interact with these classes of devices in a homogeneous way.

The main difference from the char and the block devices is that a char device can be accessed only as a stream of sequential data,
one byte after another. Instead the block devices can be accessed as chunk of fixed data that can be randomly accessed (not seq).
A kar deivices is like a serial port instead a block devices is like an HDD. The write and the read from a block devices are handled
by the driver of the dcevices. The char and the Block devices are mapped into the VFS (Virtual file system) The VFS is an unified way
to handle the FS. the main idea of the VFS is the Common File System, this means that each physical implementation
must translate its representation into the VFS's common model. The common model consist of 4 main objects. 

Superblock, that is the object that contains thee info and the dtriver of the FS

Inode, is the objects that represent all the file, directory etc

File, is the representation od the open Inode, so the interaction from the inode to a process

Dentry, stores info anout the linking of a directory entru (its a special inode)





# Userspace / Process Management

## Illustrate what are the steps undertaken by the Linux kernel to startup a new process, following the invocation of an execve() syscall.







# Scheduling

## Discuss the main differences between the CPU scheduling algorithms in Linux 2.4 and Linux 2.6.
from the 2.4 and the 2.6 in the linux kernel has changed the cheduler algorithm. The main difference is that in the 
2.4 version the schediler operate in O(N) instead in the 2.6 version the scheduler operate in O(1) (so constant time).
This because has changed the main data structure and how the priority is calculated. In the O(N) was used a LL and the 
scheduler in order to select the right task to schedule need to scan and calculate the priority of all the process in 
the LL (for this we have O(N), so a linear algo), insead in the O(1) version of the scheduler the data structure
used i a RB three and the node are ordered by its 'priority' even is the priority are no more used ad in the O(N).
The priority are used to calcilate the **virtual runtime** so the task insithe the RB are ordered using the virtual
priority (left lowest vruntime, right high runtime). And the scheduler schedule the lefter node in the tree. The
evaluation of the vruntime is done in the descheduing procedure.





# Virtualization

# Security

## Describe the ring model on x86 CPUs. Illustrate what are the steps required to enable and use it, and the way modern operating systems rely on it to enforce internal security from userspace applications







## Describe the methodologies which allow to enhance the security of modern operating systems.







## Describe what are the reasons behind the development of the Linux kpatch system, and illustrate how it works






