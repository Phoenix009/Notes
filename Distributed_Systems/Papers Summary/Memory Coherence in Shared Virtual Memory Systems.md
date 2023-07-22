
## Design Choices for Memory Coherence:

Our design goals require that the shared virtual memory are coherent. Coherence can be maintained if a shared virtual memory satisfies the following single constraint:

**A processor is allowed to update a piece of data only while no other processor is updating or reading it**

There are two design choices that greatly influence the implementation of a shared virtual memory, granularity and strategy for maintaining coherence.

### Granularity:

**Memory Contention**: When two or more threads try to access the same memory location simultaneously, contention occurs, and one or more threads may be blocked or delayed in their execution. This can lead to performance degradation and even deadlock in some cases.

The possibility of contention pushes one toward relatively small memory units.

A suitable compromise in granularity is the typical page used in a conventional virtual memory implementation.

Part of the justification for using page size granularity, of course, is that memory references in sequential programs generally have a high degree of locality

Although memory references in parallel programs may behave differently from those in sequential ones, a single process remains a sequential program, and should exhibit a high degree of locality.

In addition, such a choice allows us to use existing page-fault schemes. This can be done by setting the access rights to the pages in such a way that memory accesses that could violate memory coherence cause a page fault, and thus the memory coherence problem can be solved in a modular way in the page fault handlers.

### Memory Coherence Strategies:
The strategies may be classified by the way in which one deals with page synchronisation and page ownership.

#### Page Synchronisation:
1. **Invalidation**:  
    If a processor has a write fault, the fault handler will copy the true page and invalidate all other copies of the page.
    
2. **Write Back**:
    For a write fault, the fault handler will write to all the copies of the page. Clearly this is expensive as all write operations will cause a write back. Not suitable for loosely coupled processors.

#### Page Ownership:
1. **Static**:
    A page is always owned by the same processor. This means that other processors request the owner process for any updates to the page. Generates write faults for every write operation. Not suitable for our system
    
2. **Dynamic**:
    1. Centralised Managers
    2. Distributed Managers


## Solutions for Memory coherence Problems:
![[distributed_systems-Page-2.drawio.svg]]

## Improved Centralised Manager Algorithms:

The responsibility of synchronisation of pages is given to the individual processors/owners.

Each processor maintains data structure $\texttt{ptable}$ such that for every page it stores:

1. $\texttt{access}$: type of access.
2. $\texttt{copy\_set}$: list of processor ids that have copies of the page.
3. $\texttt{lock}$: for synchronising the request to the page.

A manager stores only the page ownership information in the $\texttt{owner}$ table.

The following diagrams provide the flowchart of the fault handlers and their corresponding service.

- Fault handlers are functions that are invoked when a read/write operation is performed on a page that is not owned by the processor.
- Services are functions that are invoked when a read/write request is received by a page owner from the manager.

![[centralised-manager-read.svg]]
![[centralised-manager-write.svg]]


## Distributed Manager Algorithms:

### 1. Fixed Distributed Manager Algorithm:
Every processor is given a predetermined subset of the pages to mange. The most straightforward approach is to distributed the pages evenly in a fixed manner to all processors.

When a fault occurs on a page $p$. the faulting processor asks $H(p)$ where the true page owner is, and then proceeds as in the centralised manager algorithm.

*It is however difficult to find a good static distribution that fits all applications well.*


### 2. Broadcast Distributed Manager Algorithm: 
An obvious way of eliminating the centralised manager is by using a broadcast mechanisms. With this strategy, each processor manages precisely those pages that it owns, and faulting processors send broadcasts into the network to find the owner of the page.

Thus the `owner` table is eliminated completely, and the information of ownership is stored in each processor's `ptable`, which in addition to `access`, `copy_set` and `lock` fields, also has an `owner` field.

More precisely, when a read fault occurs, the faulting processor `P` sends a broadcast read request, and the true owner of the page responds by adding `P` to the page's `copy_set` field and sending a copy of the page to `P`. 

Similarly, when a write fault occurs, the faulting processor sends a broadcast write request, and the true owner of the page gives up ownership and sends back the page and its `copy_set`. When the requesting processor receives the page and the `copy_set`, it will invalidate all copies.

*Broadcasting every time a page fault occurs leads to the communication system being a potential bottleneck*

### 3. Dynamic Distributed Manager Algorithm:
The heart of a dynamic distributed manager algorithm is to attempt to keep track of the ownership of all pages in each processor's local `ptable`. To do this, the `owner` field is replaced with another field, `prob_owner`, whose value can be either `null` or the "probable" owner of the page. 

The information that `porb_owner` contains is not necessarily correct at all times, but if incorrect it will at least provide the beginning of a sequence of processors through which the true `owner` can be found.


![[distributed-manager-read.svg]]
![[distributed-manager-write.svg]]

