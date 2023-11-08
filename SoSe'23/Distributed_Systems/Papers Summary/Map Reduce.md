### Introduction:

The issues of how to parallelise the computation, distribute the data, and handle failures conspire to obscure the original simple computation with large amounts of complex code to deal with these issues.

Map Reduce is a new abstraction that allows us to express the simple computations we were trying to perform but hides the messy details of parallelisation, fault-tolerance, data distribution and load balancing in a library.


### Programming Model:

The user of the MapReduce library expresses the computation as two functions: Map and Reduce.

- Map: $(k_1, v_1) \mapsto \texttt{list}(k_2, v_2)$ 
	takes an input pair and produces a set of intermediate key/value pairs. The MapReduce library groups together all intermediate values associated with the same intermediate key $k_2$ and passes them to the Reduce function.

- Reduce: $(k_2, \texttt{list}(v_2)) \mapsto \texttt{list}(v_2)$
	accepts an intermediate key $k_2$ and a set of values $\texttt{list}(v_2)$ for that key. It merges together these values to form a possibly smaller set of values .


> [!info]
> In addition, the user writes code to fill in a mapreduce specification object with the names of the input and output files, and optional tuning parameters


### Implementation

#### Execution:
The following is the overall flow of a MapReduce operation:

1. Split the input files into $M$ pieces.
2. Starts up many copies of the program on a cluster of machines. One of the copy is the master and the rest are workers.
3. There are $M$ map tasks and $R$ reduce tasks to assign. The master picks idle workers and assigns each one a map task or a reduce task
4. A map worker reads the contents of the corresponding input split. It parses key-value pairs and passes them to the user defined map function. The intermediate key-value pairs are buffered in memory.
5. The buffered pairs are written to local disk partitioned into $R$ regions. The locations of these partitions are passed back to the master to forward it to the reduce workers
6. When a reducer is notified by the master about these locations, it uses RPC calls to read the buffered data.
7. The reduce tasks after reading entire data, it does sorting and grouping using the key. 
8. The reducer iterates over the sorted intermediate data and for each unique key and the associated group of values it passes it to the reduce function.
9. The output of the reduce function is appended to a final output file.

#### Fault Tolerance:
##### Worker Failure:
- For Map tasks:
	- If **completed**:
		The output is stored on the local disks of a failed machine and is non-accessible.
		Mark as `idle`. Thus can be rescheduled and re-executed
	- If **in progress**:
		Mark as `idle`. Thus can be rescheduled and re-executed

- For Reduce tasks:
	- if **completed**:
		no need to be re-executed since their output is stored in a global file system.
	- if **in progress**:
		Mark as `idle`. Thus can be rescheduled and re-executed


##### Master Failure:
Master writes periodic checkpoints of master data structure. If the master dies a new copy can be started form the last check-pointed state.

Current implementation stops the MapReduce computation if the master fails. Clients can restart the MapReduce task if needed.

#### Locality:
MapReduce master takes the location information of the input files into account and attempts to schedule a map task on a machine that contains a replica of the corresponding input data. 


#### Task Granularity:
Ideally, $M$ and $R$ should be much larger than the number of worker machines. Having each worker perform many different tasks improves dynamic load balancing and speeds up recovery when a worker fails.

#### Backup Tasks:
One of the common causes that lengthens the total time taken for a MapReduce operation is a "straggler": a machine that takes an unusually long time to complete one of the last few map or reduce tasks in the computation.

We have a general mechanism to alleviate the problem of stragglers. When a map reduce operation is close to completion, the master schedules backup executions of the remaining in-progress tasks.

### Refinements:

#### Partitioning Function:
Users of MapReduce specify the number of reduce tasks/output files $R$ that they desire. Data gets partitioned across these tasks using a partitioning function on the intermediate key. It is useful to partition data by some other function of the key. This partition function can be provided.

#### Ordering Guarantees:
It is guaranteed that within a given partition, the intermediate key/value pairs are processed in increasing key order.

#### Combiner Function:
In some cases, there is significant repetition in the intermediate keys produced by each map task, and the user-specified Reduce function is commutative and associative.

We allow the user to specify an optional Combiner function that does partial merging of the data before it is sent over the network to the reduce tasks. Combiner function is executed on each machine that performs map task.

#### Input and Output Types:
Users can add support for a new input type by providing a simple reader interface. A reader can support reading from different sources like a database, data structures mapped in memory.

In a similar fashion we support a set of output types for producing data in different formats.

#### Skipping Bad Records:
Sometimes it is acceptable to ignore a few records, for example when doing statistical analysis on a large dataset.

We provide an optional mode of execution where the MapReduce library detects which records cause deterministic crashes and skips the records in order to make forward progress.

This is achieved by using signal handlers that catch segmentation and bus errors. If the user code generates a signal, the signal handler sends a "last-gasp" UDP packet to the master node which contains the sequence number of the record that caused the signal. This sequence number is saved and is ignored in the re-execution of the task.

#### Local Execution:
To facilitate debugging, profiling and small-scale testing, we have an alternative implementation of MapReduce that sequentially executes the operations on local machine. Users invoke the program with a special flag and can then easily use any debugging tool.

#### Status Information:
The master runs an internal HTTP server and exports a set of status pages. This top-level status page shows all the stats of the computation. This information is useful when attempting to diagnose bugs in the user code.
