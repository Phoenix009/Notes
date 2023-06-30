Our system may execute the operations in a different order than is specified.

The execution is correct if the result of the execution is the same as if the operations had been executed in the order they were specified.

The result of any execution is the same if the operations of all the processors were executed in some sequential order, and the operation of each individual processor appear in this sequence in the order specified.

==Rule1==: There exists a total order of `read` and `write` producing the same outcome for the application.

==Rule2==: We can find each machines individual sequence of instructions within the total order 

==Reads may be stale== in terms of real time but not in logical time. ==Writes are totally ordered== according to logical time (defined by the total order) across all replicas.

The writes have to be synchronous => non concurrent write operations.

This consistency model can be actually implemented for distributed systems.

### Implementation

#### 1. Primary Server:
Designate a primary machine. Every machine sends the `read` and `write` operations to the primary.

The order in which the primary executes the writes determines the total order of the operations.

The operations are slow. Every operation goes over the network
The primary server is the bottleneck.

#### 2. Partition Memory:
Divides the memory into partitions. Each machine is assigned as the primary for different partitions.

this spreads the storage loads across machines. Good performance is acheived if the machines operate mostly on the local partitions.

#### 3. Global Locks:
This implementaiton of SC uses one global lock for all addresses in memory. To access memory, machine must first acquire lock, operate on locked memory, release lock.

This implementation attains SC, total order is determined by the locks movement.

Read: [[IVY - Shared Virtual Memeory System]]