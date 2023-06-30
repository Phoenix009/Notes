Class: Distributed Systems
Created: April 23, 2023 5:50 PM
Date: April 27, 2023 4:36 PM
Semester: SoSe'23

**Why use Concurrency?**
1. Use of multiple processors
2. Driving slow devices such as disks, networks. In these cases an efficient program should be doing some other useful tasks while waiting for device to produce its next event.
3. A third source of concurrency is human users. When your program is performing some lengthy task for the user, the program should still be responsive
4. you can deliberately add concurrency to your program in order to reduce the latency of operations. some of the work incurred by a method call can be deferred, since it does not affect the result of the call. For example, when you add or remove something in a balanced tree you could happily return to the caller before re‐balancing the tree.

**Design of thread facility:**

In general there are four major mechanisms:

Thread Creation: Creating and starting a thread is called “forking”. Most forked threads are daemon threads

Mutual Exclusion: Threads interact through access to shared memory. To avoid errors arising when more than one thread is accessing the shared variable a mutual exclusion tool is used. It specifies a particular region of code that only one thread can execute at any time.

The $\texttt{lock}$ statement locks the given object, then executes the contained statements, then unlocks the object. 
A thread executing inside the $\texttt{lock}$ statement is said to “hold” the given object’s lock. If another thread attempts to lock the object when it is already locked, the second thread blocks (enqueued on the object’s lock) until the object is unlocked.

In general we achieve mutual exclusion on a set of variables by associating them with a particular object. We can then write our program so that it accesses those variables only from a thread which holds that objects lock.

Waiting for Events:

Often programmer needs to express more complicated scheduling policies. This requires use of a mechanism that allows a thread to block itself until some condition is true.

$\texttt{Wait(object)}$: atomically unlocks the object and blocks the thread.

$\texttt{Pulse(object)}$: awakens a thread that is waiting on that object

$\texttt{PulseAll(object)}$: awakens all threads that are waiting on that object


Interrupt:

The final mechanism is for interrupting a particular thread. if $\texttt{threadA}$ is blocked waiting for a condition, and $\texttt{threadB}$ calls $\texttt{threadA.interrupt()}$, then $\texttt{threadA}$ will resume execution by relocking the object and throwing an $\texttt{InterruptException}$. 


**Using Locks: Accessing Shared Data:**

Unprotected Data: 
The simplest bug related to locks occurs when we fail to protect some mutable data and then we access it without synchronization.

The $\texttt{lock}$ statement enforces serialization of threads, so that anytime only one thread executes the statements inside the $\texttt{lock}$.

Invariants:
Programmers find it easier to think of the lock as protexting the invariant of the associated data. An invariant is a boolean function of the data that is true whenever the associated lock is not held

Releasing the lock while our state is in a transient inconsistent state will inevitably lead to confusion if it is possible for another thread to acquire lock in this state.

Deadlocks involving only locks:
The most effective rule for avoiding deadlocks is to have a partial order for the acquisition of locks in out programs.

Poor performance through lock conflicts:
Whenever a thread is holding a lock, it is preventing another thread from making progress.

The best way to reduce lock conflicts is to lock at a finer granularity, which introduces complexity. It is a trade-off inferent in concurrent computation.

There is an interaction between locks and the thread scheduler thar can produce insidious performance problem. Genreally, the scheduler makes its decision based on a priority associated with each thread. 

Lock conflicts can lead to priority inversion in which a thread even with the highest priority fails to make progress. The best solution to this problem lies in the thread scheduler.

**Wait and Pulse:** 

Scheduling shared Resources:
When we want to schedule in a way in which multiple threads access some shared resource, then we want to make threads block by waiting on an object.

Use the following general pattern, which is strongly recommended for all uses of condition variables.

$$
\texttt{while(!expression) Monitor.wait(object);}
$$

This programming convention allows us to verify correctness by local inspection, which is always preferred over global inspection (looking for all places where $\texttt{pulse(object)}$ is called).


Using $\texttt{PulseAll()}$:
If we always program in the recommended style mentioned above then the correctness of our program will be unaffected if we replace all $\texttt{Pulse}$ with $\texttt{PulseAll}$.

This use trades slightly poorer performance for greater simplicity.

Spurious Wake-Ups: NONE

Spurious Lock Conflicts:
Another potential source of excessive scheduling overhead comes from situtations where a thred is awakened from waiting on an object, and before doing useful work the thread blocks trying to locak an object.

Example: A thread awakens another thread by using $\texttt{Pulse()}$ but is yet to release the lock that will block the awakened thread. This has cost us two extra reschedule operations, which is a significant expense.

If getting the best performance is important, we need to consider carafully whether a newly awakened thread will necessarily block on some other object shortly after it starts running. If so we need to arrange to defer the wake-up to a more suitable time.

Starvation:
When you are locking an object, this consideration is dealt with for you by the threads implementation typically by a first‐in‐first‐out rule for each priority level.

The most extreme form of unfairness is “starvation”, where some thread will never **make progress.

The programmer must excercise restraint and only add such features if they are really required by the actual load on the resource.

Complexity:

Deadlock:
It is generally risky to call into a lower level abstraction while holding locks, unless we understand fully the circumstand under which the called method might block.

One solution here is to explicitly unlock the higher lever lock before calling lower level abstraction. this solution has its own dangers.

A better solution is to arrange the end of the lock statement before calling down.