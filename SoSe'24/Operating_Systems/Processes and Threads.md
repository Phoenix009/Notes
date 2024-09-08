- [ ] What constitutes the context of a process
- [ ] What are the primary concerns when virtualising multiple processes in a multiprocessing system
- [ ] What are user space threads and kernel space threads
- [ ] Definitive list of resources that are shared between threads and processes

# Process:
A program is a passive entity, such as a file containing a list of instructions stored on a disk. In contrast, a process is an active entity with a program counter specifying the next instruction to execute and a set of associated resources. 

## Process State:
The state of a process is defined in part by the current activity of that process.

| State      | Info                                                 |
| ---------- | ---------------------------------------------------- |
| NEW        | the process is being created                         |
| RUNNING    | instructions are being executed                      |
| WAITING    | the process is waiting for some event to occur       |
| READY      | the process is waiting to be assigned to a processor |
| TERMINATED | the process has finished execution                   |

## PCB - Process Control Block
Each process is represented in the operating system by a process control block.

It contains many pieces of information associated with a specific process including the following:

| Data                          | Description                                                                                     |
| ----------------------------- | ----------------------------------------------------------------------------------------------- |
| Process State                 | the state of the process                                                                        |
| Program Counter               | stores the address of the next instruction to be executed                                       |
| CPU Registers                 | The state of the registers must be stored to ensure process continues correctly after switching |
| CPU Scheduling information    | Includes process priority, pointers to scheduling queues etc.                                   |
| Memory Management Information | Page Tables, Segment Tables, etc                                                                |
| Accounting Information        | Includes amount of CPU and other statistics                                                     |
| I/O Status Information        | Includes the list of I/O devices allocated to the process, list of open files etc.              |

## Thread:
The process model discussed so far has implied that a process is a program that performs a single thread of execution.

# Process Scheduling
The objective of multiprogramming is to have some process running at all times, to maximise CPU utilisation.

To meet this objective, the *process scheduler* selects an available process for program execution on the CPU.

## Scheduling Queues:

![[queuing_diagram.png]]

As process enters the system, they are put in the `job_queue`. The processes that are residing in main memory and are ready and waiting to execute are kept on a list called the `ready_queue`. 

the system also includes other queues. The list of processes waiting for a particular device is called a `device_queue`. Every device has its own queue.

## Schedulers:
The operating system must schedule processes from queues in some fashion. This selection process is carried out by the *scheduler*.

Often in batch system, more processes are submitted than can be executed immediately. these processes are spooled to a mass storage device.

The *long-term-scehduler* or *job-scheduler* selected processes from this pool and loads them into memory.

The *short-term-scheduler* or *CPU-scheduler*, selects from among the processes that are ready to execute and allocates the CPU to them.

the long-term-scheduler controls the *degree of multiprogramming* (the number of processes in the memory). 

In general most processes can be described as either:
1. IO Bound: Spends more time doing IO than computation
2. CPU Bound: Spends more time doing computations than IO
It is important that the long term scheduler selects a good mix of IO and CPU bound processes.

Some operating systems also use a *medium term scheduler*. the key idea is that sometimes it can be advantageous to remove a process from the memory and thus reduce the degree of multiprogramming. Later it can be swapped back for execution. This scheme is called *swapping*. 


## Context Switch:

Interrupts cause the operating system to change a CPU from its current task and to run a kernel routine.

When an interrupt occurs, the system needs to save the current context of the process running on the CPU so that it can restore that context when its processing is done. This context is represented in the PCB of the process.

Generically, we perform a state save of the current state of the CPU and then a state restore to resume operations.

Switching the CPU to another process requires performing a state save of the current process and a state restore of a different process. This task is known as a *context switch*.

Context switch time is pure overhead as the system does no useful work while switching.

# Operations on Process:

## Process Creation: 
`fork()`

## Process Termination:
`wait()` and `exit()`


# Interprocess Communication:

A process is *independent* if it does not share data with any other process. *Cooperating* process shares data with other processes and thus can be affected or affect other processes. 

Why use process cooperation?
- Information Sharing
- Computation Speedup
- Modularity
- Convenience

Cooperating process require an interprocess communication mechanism that will the to exchange data.

there are two fundamental models for IPC
1. Shared Memory
2. Message Passing


## Shared Memory System:
Requires communicating process to establish a region of shared memory.

A shared memory region resides in the address space of the process creating the shared memory segment. Other process must attach it to their address space.

Normally, the operating system tries to prevent one process from accessing another processes memory. Shared memory requires that two or more processes agree to remove this restriction.

Two types of buffers can be used. The *unbounded buffer* places no practical limit on the size of the buffer. The *bounded buffer* assumes a fixed buffer size.

## Message Passing System:
Message passing provides a mechanism to allow processes to communicate and to synchronise their actions without sharing the same address space.

A message passing facility provides at least two operations:
1. send(message)
2. receive(message)

If process P and Q want to communicate, a communication link must exist between them. This link can be implemented in a variety of ways. We are concerned with the logical implementation of the link.

Following are the several methods for logically implementing a link and then `send()`/`receive()` operations.

### Naming:
Processes that want to communicate must have a way to refer to each other.

#### Indirect Communication:
with indirect communication, the messages are sent to and received from mailboxes or ports. 


### Synchronisation:
Message passing may be either blocking or non-blocking. 


