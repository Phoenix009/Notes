## Introduction:
A "thread" is a straightforward concept: a single sequential flow of control. Having multiple threads in a program means that at any instant the program has multiple points of execution. 

The programmer can mostly view the threads as executing simultaneously, as if the computer were endowed with as many processors as there are threads. The programmer must be aware that the computer might not in fact execute all his threads simultaneously.

Each thread executes on a separate call stack with its own separate local variables while the off-stack (global) variables being shared among all the threads of the program. The programmer is responsible for using appropriate synchronisation mechanisms to ensure that the shared memory is accessed in a manner that will give the correct answer.

A thread facility allows us to write programs with multiple simultaneous points of execution, synchronising through shared memory. In this article we discuss the basic thread and synchronisation primitives.

## Why use Concurrency?
1. Use of multiple processors (Obvious)
2. Driving slow devices such as disks or networks. In these case an efficient program should be doing some other useful tasks while waiting for device to produce its next event.
3. A third source of concurrency is human users. When your program is performing some lengthy task for the user, the program should still be responsive.
4. We can deliberately add concurrency to our program in order to reduce the latency of operations. For example, some of the work incurred by a method call can be deferred if it does not affect the result of the call. For example, when you add or remove something in a balanced tree you could happily return to the caller before re‐balancing the tree. Re-balancing done in a separate thread.

## Design of thread facility:
In general there are four major mechanisms:

### Thread Creation: 
Creating and starting a thread is called “forking”. Most forked threads are daemon threads
  
### Mutual Exclusion: 
Threads interact through access to shared memory. To avoid errors arising when more than one thread is accessing the shared variable a mutual exclusion tool is used. It specifies a particular region of code that only one thread can execute at any time.

A $\texttt{lock}$ statement locks the given object, then executes the contained statements, then unlocks the object.

A thread executing inside the $\texttt{lock}$ statement is said to “hold” the given object’s lock. If another thread attempts to lock the object when it is already locked, the second thread blocks (enqueued on the object’s lock) until the object is unlocked.

In general we achieve mutual exclusion on a set of variables by associating them (mentally) with a particular object. We can then write our program so that it accesses those variables only from a thread which holds that objects lock.

### Waiting for Events:
Often programmer needs to express more complicated scheduling policies. This requires use of a mechanism that allows a thread to block itself until some condition is true.

The mechanism used to achieve this is generally called "condition variables" which provides functionalities to allow programmers to express complicated scheduling policies. These functions are:

- $\texttt{Wait(object)}$: atomically unlocks the object and blocks the thread.
- $\texttt{Pulse(object)}$: awakens a thread that is waiting on that object
- $\texttt{PulseAll(object)}$: awakens all threads that are waiting on that object


### Interrupt:
The final mechanism is for interrupting a particular thread. if $\texttt{threadA}$ is blocked waiting for a condition, and $\texttt{threadB}$ calls $\texttt{threadA.interrupt()}$, then $\texttt{threadA}$ will resume execution by re-locking the object and throwing an $\texttt{InterruptException}$. 


## Using Locks: Accessing Shared Data

### Unprotected Data: 
The simplest bug related to locks occurs when we fail to protect some mutable data and then we access it without synchronisation.

The $\texttt{lock}$ statement enforces serialisation of threads, so that at any time only one thread executes the statements inside the $\texttt{lock}$.

### Invariants:
Programmers find it easier to think of the lock as protecting the invariant of the associated data. 

An invariant is a boolean function which checks the constraints the object must satisfy at any given time. Invariant must be true whenever the associated lock is not held.

Releasing the lock while our state is in a transient inconsistent state will inevitably lead to confusion if it is possible for another thread to acquire lock in this state.

### Deadlocks involving only locks:
The most effective rule for avoiding deadlocks is to have a partial order for the acquisition of locks in our programs.

### Poor performance through lock conflicts:
Whenever a thread is holding a lock, it is preventing another thread from making progress.

The best way to reduce lock conflicts is to lock at a finer granularity, which introduces complexity. It is a trade-off inherent in concurrent computation.

The most typical example where locking granularity is important is in a class that manages a set of object, for example a set of open buffered files. 

The simplest strategy is to use a single global lock for all the operations. But this would prevent simultaneous operations of two different files. The most obvious way to use the locks is to have one global lock that protects the global data structures of the class and have object specific locks which protect the data specific to that instance.

There is an interaction between locks and the thread scheduler that can produce insidious performance problem. Thread scheduler decides which  of the non-blocked threads should be given a processor to run on. Generally, this decision is based on a priority associated with each thread. 

Lock conflicts can lead to priority inversion in which a thread even with the highest priority fails to make progress. Ex:

```
C is running (e.g., because A and B are blocked somewhere); 
C locks object M;  
B wakes up and pre-empts C(i.e., B runs instead of C since B has higher priority); 
B embarks on some very long computation;  
A wakes up and pre-empts B (since A has higher priority); 
A tries to lock M, but can’t because it’s still locked by C;  
A blocks, and so the processor is given back to B;  
B continues its very long computation.
```

The best solution to this problem lies in the thread scheduler. Ideally, the scheduler should raise threads C's priority which thats needed to enable thread A to eventually make progress.

## Wait and Pulse: 

### Scheduling shared Resources:
When we want to schedule **the way in which multiple threads access some shared resource**, then we want to make threads block by waiting on an object. Simple mutual exclusion is not sufficient in such cases.

Use the following general pattern, which is strongly recommended for all uses of condition variables.

$$
\texttt{while(!expression) Monitor.wait(object);}
$$

The main reason for advocating use of this pattern is to make your program more obviously, and more robustly, correct. With this style it is immediately clear that the expression is true before the following statements are executed

This programming convention allows us to verify correctness by local inspection, which is always preferred over global inspection (looking for all places where $\texttt{pulse(object)}$ is called).

### Using $\texttt{PulseAll()}$:
A simple example where $\texttt{PulseAll()}$ is useful in when we want to awaken multiple threads, because the resource we have just made available can be used by several other threads.

One use of $\texttt{PulseAll()}$ is when you want to simplify your program by awakening multiple threads, even though you know that not all of them can make progress.

If we always program in the recommended style mentioned above then the correctness of our program will be unaffected if we replace all $\texttt{Pulse}$ with $\texttt{PulseAll}$.

This use trades slightly poorer performance for greater simplicity.

### Spurious Lock Conflicts:
A potential source of excessive scheduling overhead comes from situations where a thread is awakened from waiting on an object, and before doing useful work the thread blocks trying to lock an object.

Example: A thread awakens another thread by using $\texttt{Pulse()}$ but is yet to release the lock that will block the awakened thread. This has cost us two extra reschedule operations, which is a significant expense.

If getting the best performance is important, we need to consider carefully whether a newly awakened thread will necessarily block on some other object shortly after it starts running. If so we need to arrange to defer the wake-up to a more suitable time.

### Starvation:
Whenever we have a program that is making scheduling decisions, we must worry about how fair these decisions are; in other words, are all threads equal or are some more favoured?

When you are locking an object, this consideration is dealt with for you by the threads implementation typically by a first‐in‐first‐out rule for each priority level.

The most extreme form of unfairness is “starvation”, where some thread will never make progress.

