Class: Distributed Systems
Created: April 27, 2023 5:17 PM
Date: May 3, 2023 3:41 PM
Semester: SoSe'23


**Design Chocies for Memory Coherence:**

Our design goals require that the shared virtual memory are coherent. Coherence can be maintained if a shared virtual memory satisfies the following single constraint:

A processor is allowed to update a peice of data only while no other processor is updating or reading it

There are two design choices that greatly influence the implementaiton of a shared virtual memory, granularity and strategy for maintaining coherence.

**Granularity:**

In a typical loosely-coupled multiprocessor system, sending large packets of data (say 1KB) is not much more expensive than sending small ones (say < 10B)

Memory Contention: When two or more threads try to access the same memory location simultaneously, contention occurs, and one or more threads may be blocked or delayed in their execution. This can lead to performance degradation and even deadlock in some cases.

The possibility of contention pushes one toward relatively small memory units.

A suitable compromise in granularity is the typical page used in a conventional virtual memory implementation.

Part of the justification for using page size granularity, of course, is that memory references in sequential programs generally have a high degree of locality

Although memory references in parallel programs may behave differently from those in sequential ones, a single process remains a sequential program, and should exhibit a high degree of locality.

In addition, such a choice allows us to use existing pagefault schemes. This can be done by setting the access rights to the pages in such a way that memory accesses that could violate memory coherence cause a page fault, and thus the memory coherence problem can be solved in a modular way in the page fault handlers.

**Memory Coherence Strategies:**

The strategies may be classified by the way in which one deals with page synchronization and page ownership.

Page Synchronization:

1. Invalidation:
    
    If a processor has a write fault, the fault handler will copy the true page and invalidate all other copies of the page.
    
2. Write Back:
    
    for a write fault, the fault handler will write to all the copies of the page. Clearly this is expensive as all write operations will cause a write back. Not suitable for loosely coupled processors.
    

Page Ownership:

1. Static:
    
    A page is always owned by the same processor. This means that other processors request the owner process for any updates to the page. Generates write faults for every write operation. Not suitable for our system
    
2. Dynamic:
    1. Centralized Managers
    2. Distributed Managers

**Improved Centralized Manager Algorithms**:

Each processor maintains data structure $\texttt{ptable}$ such that for every page it stores:

1. $\texttt{access}$: type of access.
2. $\texttt{copy\_set}$: list of processor ids that have copies of the page.
3. $\texttt{lock}$: for synchronizing the request to the page.

A manager stores only the page ownership information in the $\texttt{owner}$ table.

![memory_coherence-centralized_read.png](file:///Users/phoenix/academic_year22-23/SoSe'23/DIS/papers/memory-coherence/memory_coherence-centralized_read.png)

![memory_coherence-centralized_write.drawio.png](file:///Users/phoenix/academic_year22-23/SoSe'23/DIS/papers/memory-coherence/memory_coherence-centralized_write.drawio.png)

**Distirbuted Manager Algorithms**:

1. Fixed Distributed Manager Algorithm:
    
    Every processor is given a predetermined subset of the pages to mange. The most straightforward approach is to distributed the pages evenly in a fixed manner to all processors.
    
    When a fault occurs on a page $p$. the faulting processor asks $H(p)$ where the true page owner is, and then proceeds as in the centralized manager algorithm.
    
    It is however difficult to find a good static distribution that fits all applications well.
    

1. Broadcast Distributed Manager Algorithm: 
    
    An obvious way of eliminating the centralized manager is by using a broadcase mechanisms. With this strategy, each processor manages precisely those pages that it owns, and faulting processors send broadcasts into the network to find the owner of the page.
    
2. Dynamic Distributed Manager Algorithm:

![memory_coherence-Dynamic Distributed Manager Algorithm_ read.png](file:///Users/phoenix/academic_year22-23/SoSe'23/DIS/papers/memory-coherence/memory_coherence-Dynamic%20Distributed%20Manager%20Algorithm_%20read.png)

![memory_coherence-distribtued_write.drawio.png](file:///Users/phoenix/academic_year22-23/SoSe'23/DIS/papers/memory-coherence/memory_coherence-distribtued_write.drawio.png)

1. Dynamic Distributed Manager Algorithm with fewer Broadcasts:
    
    In the previous algorithm, at initialization or after a broadcase operation, all processors know the true owner of the page.
    
    After a broadcase, an upper bound on the total number of messages for locating the owner of the page for $K$ page faults on different processors is $2K-1$
    
    This enforces a broadcast message after every $K$ page faults to a page. Thus, a counter is needed in each entry of the page table.