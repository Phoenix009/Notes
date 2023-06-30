### Introduction:

The issues of how to parallelize the computation, distribute the data, and handle failures conspire to obscure the original simple computation with large amounts of complex code to deal with these issues.

Map Reduce is a new abstraction that allows us to express the simple computations we were trying to perform but hides the messy details of parallelization, fault-tolerance, data distribution and load balancing in a library.


### Programming Model:

### Execution:
When the user program calls the `MapReduce` function, the following sequence of actions occurs:

Does each map task have individual R splits?



### Fault Tolerance:

#### Worker Failure:

Uses re-execution as the primary mechanism for fault tolerance.

When the node fails or becomes unreachable then the there is no way to retrieve the data processed so far. So obvious solution is to restart these tasks on the same node or some other nodes. This makes re-execution the primary mechanism for fault tolerance.

Any completed or in-progress tasks on a failed worker are reset to idle and become eligible for rescheduling.

!Completed reduce tasks do not need to be re-executed since their output is stored in a global file system.


#### Master Failure:

Master writes periodic checkpoints of master data structure. If the master dies a new copy can be started form the last checkpointed state.

Current implementation stops the MapReduce computation if the master fails. Clients can restart the MapReduce task if needed.

