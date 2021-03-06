---
title: "Operating Systems: Three Easy Pieces"
date: 2016-11-03 14:35:13
mathjax: true
---

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Virtualization](#virtualization)
    - [The Abstraction: The Process](#the-abstraction-the-process)
    - [CPU Virtualization (Scheduling Policies)](#cpu-virtualization-scheduling-policies)
        - [Workload Assumptions](#workload-assumptions)
        - [Scheduling metric](#scheduling-metric)
        - [First In, First Out (FIFO)](#first-in-first-out-fifo)
        - [Shortest Job First (SJF)](#shortest-job-first-sjf)
        - [Shortest Time-toCompletion First (STCF)](#shortest-time-tocompletion-first-stcf)
        - [A New Metric: Response Time](#a-new-metric-response-time)
        - [Round Robin (RR)](#round-robin-rr)
        - [Incorporating I/O](#incorporating-io)
        - [The Multi-Level Feedback Queue](#the-multi-level-feedback-queue)
            - [How to Change Priority](#how-to-change-priority)
            - [The Priority Boost](#the-priority-boost)
            - [Better Accounting](#better-accounting)
            - [Tuning MLFQ And Other Issues](#tuning-mlfq-and-other-issues)
    - [The Abstraction: Address Spaces](#the-abstraction-address-spaces)
        - [The Address Space](#the-address-space)
        - [Goals](#goals)
    - [Interlude: Memory API](#interlude-memory-api)
    - [Mechanism: Address Translation](#mechanism-address-translation)
        - [Dynamic (Hardware-based) Relocation](#dynamic-hardware-based-relocation)
    - [Segmentation](#segmentation)
        - [Support for Sharing](#support-for-sharing)
        - [OS Support](#os-support)

<!-- markdown-toc end -->


# Virtualization

## The Abstraction: The Process ##

The abstraction provided by the OS of a running program is something we call a **process**.

What consititutes a process:

1. The memory that the process can address (called its **address space**)
2. Registers
3. I/O information

Process API:

- **Create**
- **Destroy**
- **Wait**
- **Miscellaneous Control**
- **Status**
  - Running
  - Ready
  - Blocked

Process creation:

1. Loading the code and static data into memory
2. initializing a stack
3. Doing other work as related to I/O setup

Data structures for process:

```c
// the registers xv6 will save and restore
// to stop and subsequently restart a process
struct context {
  int eip;
  int esp;
  int ebx;
  int ecx;
  int edx;
  int esi;
  int edi;
  int ebp;
};
// the different states a process can be in
enum proc_state { UNUSED, EMBRYO, SLEEPING,
                  RUNNABLE, RUNNING, ZOMBIE };
// the information xv6 tracks about each process
// including its register context and state
struct proc {
  char *mem; // Start of process memory
  uint sz; // Size of process memory
  char *kstack; // Bottom of kernel stack
                // for this process
  enum proc_state state; // Process state
  int pid; // Process ID
  struct proc *parent; // Parent process
  void *chan; // If non-zero, sleeping on chan
  int killed; // If non-zero, have been killed
  struct file *ofile[NOFILE]; // Open files
  struct inode *cwd; // Current directory
  struct context context; // Switch here to run process
  struct trapframe *tf; // Trap frame for the
                        // current interrupt
};
```

Process API:

- `fork`
- `wait`
- `exec`

Problem of direct execution protocol (without limits) 

- How can the OS make sure the program doesn't do anything that we don't want it to do, while still running it efficiently
- How does the operating system stop it from running and switch to another process, thus implementing the **time sharing** we require to virtualize the CPU

Limited Direct Execution protocol

1. At boot time, the kernel initializes the trap table, and the CPU remenbers its location for subsequent use. -> restricted operations
2. When running a process, the kernel sets up a few things before using a return-from-trap instruction to start the execution of the process; this switches the CPU to user mode and begins running the process. -> switching between processes

## CPU Virtualization (Scheduling Policies) ##

### Workload Assumptions ###

Workload: the processes running in the system.

### Scheduling metric

Turnaround time: 

$$T\_{turnaround} = T\_{completion} - T\_{arrival}$$

### First In, First Out (FIFO)

There is a problem named convoy effect, where a number of relatively-short potential consumers of a resource get queued behind a heavyweight resource consumer.

### Shortest Job First (SJF) ###

Problem: height weight resource consumer may arrive first.

### Shortest Time-toCompletion First (STCF) ###
 If we knew job lengths, and that jobs only used the CPU, and our only metric was turnaround time, STCF would be a great policy.

### A New Metric: Response Time

Response time is defined as the time from when the job arrives in a system to the first time it is scheduled: 

$$T\_{response} = T\_{firsturn} - T\_{arrival}$$

STCF is quite bad for response time and interactivity.

### Round Robin (RR)

Instead of running jobs to completion, RR runs a job for a **time slice** and then switches to the next job in the run queue.

RR may exact a noticeable performance cost.

Trade-off: if you are willing to be unfair, you can run shorter jobs to completion, but at the cost of response time; if you instrad value fairness, response time is lowered, but ay the cost of turnaround time.

### Incorporating I/O

By treating each CPU burst as a job, the scheduler makes sure processes that are "interactive" get run frequently. While those interactive jobs are performing I/O, pther CPUintensive jobs run, thus better utilizing the processor.

### The Multi-Level Feedback Queue ###

The two-fold fundamental problem MLFQ tries to address is two-fold:

1. It would like to optimize *turnaround time*.
2. MLFQ would like to make a system feel responsive to interactive users and thus minimize *response time*.

Structure of MLFQ: a number of distinct **queues**, each assigned a different **priority level**.

The basic rules for MLFQ:

- **Rule 1:** If priority(A) > Priority(B), A runs (B doesn't).
- **Rule 2:** If priority(A) = Priority(B), A & B run in RR.

#### How to Change Priority ####

- **Rule 3:** When a job enters the system, it is placed at the highest priority.
- **Rule 4a:** If a job uses up an entire time slice while running, its priority is *reduced* (i.e., it moves down one queue).
- **Rule 4b:** If a job gives up the CPU before the time slice is up, it stays at the same priority level.

Problems With Out Current MLFQ:

1. There is the problem of **starvation**.
2. A smart user could rewrite their program to **game the scheduler**.
3. A program may *change its behavior* over time

#### The Priority Boost ####

- **Rule 5:** After some time period *S*, move all the jobs in the system to the top queue.

The addition of the time period *S* leads to the obvious question: what should *S* be set to?

*S* is a **voo-doo constants**, because they seemed to require some form of black magic to set them correctly.

#### Better Accounting ####

Rewrite Rules 4a and 4b to the following single rule to prevent gaming of our scheduler.

- **Rule 4:** Once a job uses up its time allotment at a given level (regardless of how many times it has given up the CPU), its priority is reduced (i.e., it moves down one queue).

#### Tuning MLFQ And Other Issues ####


- How big should the time slice be per queue?
  - Varying time-slice length across different queues.
  - The high-priority queues are usually given short time slice.
  - The low-priority queues, in contrast, contain long-running jobs that are CPU-bound hence longer time slices works well.
- How many queues should there be?
- How often should priority be boosted in order to avoid starvation and account for changes in behavior?

Many schedulers have a few other features:

- Some schedulers reserve the highest priority levels for operating system work.
- Some systems also allow some user **advice** to help set priorities.

## The Abstraction: Address Spaces ##

In order to implement **time sharing** **efficiently** we leave processes in memory to while switching between them. In particular, allowing multiple programs to reside concurrently in memory makes **protection** an important issue.

### The Address Space ###

**Address space** is the running program's view of memory in the sytem.

The address space of a process contains all of the memory state of the running program:

- Code of the program
- Stack
- Heap
- Etc.

Virtualizing memory: the running program thinks it is loaded into memory at a particular address and has a potentially very large address space.

### Goals ###

To make sure the OS virtualize memory, we need some goals to guide us:

1. **Transparency:** the OS should implement virtual memory in a way that is invisible to the running program.
2. **Efficiency**
3. **Protection:** The OS should make sure to protect processes from one another as well as the OS itself from processes (isolation).

## Interlude: Memory API ##

Type of memory:

1. Stack
2. Heap

API:

- `malloc()` 
- `free()`

## Mechanism: Address Translation ##

Strategy in virtualizing memory:

1. Efficiency
2. Control
3. Virtualization

Assumptions:

1. Usre's address space must be placed *contiguously* in physical memory
2. The size of address space is *less than the size of physical memory*
3. Each address space is eactly the *same size*

### Dynamic (Hardware-based) Relocation ###

Two hardware registers within each CPU:

1. **Base register**
2. **Bounds** (sometimes called a limit register)
  - Bounds register is there to help with protection

In this setup:

- Programs are written and compiled as if it is loaded at address zero
- When a program starts running, the OS decides where in physical memory it should be loaded and sets the base register to that value

$$physical\ address = virtual\ address + base$$

Problems need to be solved:

1. The OS must take action when a process is created, finding space for its address space in memory.
2. The OS must do some work when a process is terminated.
3. The OS must also perform a few additional steps when a context switch occurs.
4. The OS must provide **exception handlers**.

The disadvantages of dynamic relocation: when the process stack and heap are not too big, all of the space between the stack and heap is simply wasted (**internal fragmentation**).

## Segmentation ##

A segment is a contiguous portion of the address space of a particular length.

Three logically-different segments:

1. Code
2. Stack
3. Heap

OS places each one of those segments in different parts of physical memory to avoid filling physical memory with unused virtual address space.

Thus each process needs three base and bounds register pairs.

Which segment are we referring to?

- Explicit approach: use the top few bits of the virtual address to record the segment type.
- Implicit approach: the hardware determines the segment by noticing how the address was formed.

Pay attention to the stack, which *grows backwards*.

### Support for Sharing ###

To support sharing, we need a little extra support from the hardware, in the form of **protection bits**.

- Ready
- Write
- Execute

### OS Support ###

There are some new OS issues to support segmentation:

1. What should the OS do on a context switch?
  - The segment registers must be saved and restored.
2. How to manage free space in physical memory?
  - The general problem that arises is that physical memory quickly becomes full of little holes of free space.
    1. One solution would be to **compact** physical memory by rearranging the existing segments.
    2. A simpler approach is to use a free-list management algorithm that tries to keep large extents of memory available for allocation. (There are literally hundreds of approaches that people have taken)

## Free-Space Management ##

To track the size of allocated regions, most allocators store a little bit of extra information in a **header** block which is kept in memory.

```c
typedef struct __header_t {
  int size;
  int magic;
} header_t;
```

In the structure, the magic number provide additional integrity checking, and other information.

When the user calls `free(ptr)`, the library then uses simple pointer arithmetic to figure out where the header begins:

```c
void free(void *ptr) {
  header_t *hptr = (void *) ptr - sizeof(header_t);
  // ...
}
```

### Basic Strategy ###

- Best fit
  - Best fit tries to reduce wasted space.
  - A heavy performance penalty to search for the correct free block.
- Worst fit
  - Worst fit tries to leave big chunks free instead of lots of small chunks that can arise from a best-fit approach.
  - The same performance problem as best fit.
- First fit
  - First fit has the advantage of speed.
  - Sometimes pollutes the beginning of the free list with small objects.
- Next fit

### Other Approaches ###

- Segregated lists
- Buddy Allocation

## Pagine: Introduction ##

- Page: fixed-sized units.
- Page frames: an array of fixed-sized slots.

Advantages:

1. Flexibility
2. Simplicity

Page table: a per-process data structure to store address translations.

## Beyond Physical Memory: Mechanisms ##

How can the OS make use of a larger, slower devices to tranparently provide the illusion of a large virtual address?

### Swap Space ###

Swap space: reserve some space on the disk fgor moving pages back and forth.

- Present bit
- Page fault

Why hardware doesn't handle page faults

1. Page faults to disk is so slow that the extra overheads of running software are minimal
2. To be able to handle a page fault, the hardware would have to understand swap space, how to issue I/Os to the disk, and a lot of other details which it currently doesn't know much about.

## Beyond Physical Memory: Policies ##

How can the OS decide which page to evict from memory?

### Chache Management ###

- Cache misses/cache hits
- Average memory access time

$$AMAt = (P_{Hit} \times T_M) + (P_{Miss} \times T_D)$$

Where $T_M$ represents the cost of accessing memory, $T_D$ the cost of accessing disk, $P_{Hit}$ the probability of finding the data in the cache, and P_{Miss} the probability of not finding the data in the cache.

