
### Introduction:

Systems based on message passing suffer mainly in two aspects: passing complex data structures and process migrations. this paper shows that a ==solution to these problems is to build a shared virtual memory==.


### Shared Memory vs. Message Passing:

There are two main models for message passing: the message passing interface (MPI) and the remote procedure call (RPC) model. 

In MPI, processes communicate directly with one another. Although programmers can use these primitives to synchronize programs, they need to be concious of data movement between processes at all times. 

In RPC, processes call remote procedures as if they were local procedures. Allows to worry less about data movement. However, transparency is limited as it simulates the execution in the same address space using different address spaces.

The difficulty of passing complex data structures is the main drawback of message passing models for parallel programming.

Another problem with the message passing system is the difficulty of process migration because there are multiple address spaces.

In a shared virtual memory system, a process migration only requires moving a process form the ready queue on the source processor to the ready queue of the destination processor. PCB, code and stack are all in the same address space.


### Shared Virtual Memory:

A shared virtual memory is a single address space shared by a number of processors.

Memory mapping managers are responsible for :
1. implementing the mapping from the local memory to the shared virtual memory,
2. keeping the address space coherent.

Implies a sequential consistency:
- Shared virtual memory is partitioned into pages.
- Pages marked read only can have local copies. => Concurrent reads that are possibly stale
- Pages marked write can only have one copy. => Synchronous writes. total order given my the order in which locks are acquired.

The shared virtual memory has the following properties:
1. Address space is partitioned into pages
2. Allows multiple readers
3. Uses Lazy Release Consistency
4. Uses Invalidation


### Prototype Implementation:

IVY consists of five modules:
1. Remote Operations
2. Memory Mapping
3. Process Management
4. Memory Allocation
5. Initialization


#### 1. Memory Mapping:

Memory mapping managers are responsible for :
1. implementing the mapping from the local memory to the shared virtual memory
2. keeping the address space coherent.

We can refer the "[[Memory Coherence in Shared Virtual Memory Systems]]" paper to understand how the address space is kept consistent.


#### 2. Process and Process Scheduling:

The module provides clients with a set of calls for writing parallel programs.

The process scheduling mechanism is designed to be simple and uses a ready queue. If there is no ready process available, the dispatcher runs a system process called the ==null process==. 

The null process waits for two events:

1. TIMEOUT:

When a timeout event occurs, the null process runs the passive load balancing. The algorithm lets each procesor ask for work when it is idle using ==hint information==.

hint information informs other processors about current workload (how many processes it can handle). This is important to minimize the rejections of migrating requests.


2. READY:

happens in two cases when a new process is migrated or a new process is created. The null process suspends itself and dispatcher does another schedule.



#### 3. Process Migration:

A process is either migratable or non-migratable, indicated by a field in the PCB. 

A process migration must:
1. send the PCB of the process to the destination processor.
2. Copy the current page of the processes stack to the destination processor.
3. Transfer the ownership of all the pages in the stack to the destination processor.
4. put the PCB on the ready queue of the destination processor.

#### 4. Memory Allocation:

IVY uses a simple memory allocation module that uses a "first-fit" algorithm. IVY uses a binary lock on each processor for memory allocation purposes.


#### 5. Remote Operation:

Implements a remote request/reply mechanism that handles all the remote operations of other modules.

Is a simple RPC facility with a few special features. Has a broadcast request having three reply schemes:
1. reply from a receiving processor: useful for finding the owner of a page.
2. replies from all receiving processors: implementing invalidation operation.
3. no reply: for broadcasting hint information for process scheduling.

