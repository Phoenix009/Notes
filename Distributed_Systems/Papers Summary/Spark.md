- Resilient Distributed Datasets (RDDs), a distributed memory abstraction that lets programmers perform in-memory computations on large clusters in a fault-tolerant manner.


## Introduction:
Cluster computing frameworks like MapReduce and Dryad have been widely adopted for large-scale data analytics.

Unfortunately, in most current frameworks, the only way to reuse data between computations (e.g., between two MapReduce jobs) is to write it to an external stable storage system, e.g., a distributed file system

we propose a new abstraction called ==resilient distributed datasets (RDDs)== that enables efficient data reuse in a broad range of applications.

The main challenge in designing RDDs is defining a programming interface that can provide fault tolerance efficiently.

RDDs provide an ==interface based on coarse-grained transformations== that apply the same operation to many data items.

This allows them to efficiently provide fault tolerance by ==logging the transformation used to build a dataset rather than the actual data==.


## Resilient Distributed Dataset (RDDs):
### Abstraction:
RDD is a ==read-only, partitioned collection of records==. 

RDDs can only be created through deterministic operations on either:
1. data in stable storage or
2. other RDDs. 
We call these operations ==transformations==

### Spark Programming Interface:
Spark exposes RDDs through a language integrated API, where each dataset is represented as an object and ==transformations== are invoked using methods on these objects.

Programmers start by defining one or more RDDs through transformations on data in stable storage.

They can then use these RDDs in ==actions==, which are operations that return a value to the application or export data to a storage system. 

Programmers can call a ==persist== method to indicate which RDDs they want to reuse in future operations. These RDDs are persisted in memory by default.

Spark ==computes RDDs lazily== the first time they are used in an action, so that it can pipeline transformations.


## Representing RDDs:
One of the challenges in providing RDDs as an abstraction is ==choosing a representation for them that can track lineage across a wide range of transformations.

We propose a simple ==graph-based representation for RDDs== that facilitates these goals

In a nutshell, we propose representing each RDD through a common interface that exposes five pieces of information: 
1. a set of partitions, which are atomic pieces of the dataset; 
2. a set of dependencies on parent RDDs; 
3. a function for computing the dataset based on its parents; 
4. metadata about its partitioning scheme 
5. data placement.

| operations | Meaning |
|:----------|:-------:|
|$\texttt{partitions()}$ | Returns a list of partition objects|
|$\texttt{preferredLocations(p)}$ | List nodes where partition p can be accessed faster due to data locality|
| $\texttt{dependencies()}$ | Returns a list of dependencies
| $\texttt{iterator(p, parentIters)}$ | Compute the partition p given iterators for its parent partitions|
|$\texttt{partitioner()}$| Returns metadata|


*How to represent dependencies between RDDs?*
We classify dependencies into two types:
1. ==Narrow== dependencies: each partition of the parent RDD is used by at most one partition of the child RDD
2. ==Wide== dependencies: ==Multiple== child partitions may depend on a parent RDD partition
Ex: *map* leads to a narrow dependency, while *join* leads to to wide dependencies

## Implementation:

### Job Scheduling:
Whenever a user runs an action (e.g., *count* or *save*) on an RDD, the scheduler examines that RDD’s lineage graph to build a DAG of stages to execute

Each stage contains as many pipelined transformations with ==narrow dependencies== as possible. The boundaries of the stages are the shuffle operations required for ==wide dependencies==, or any already computed partitions that can short-circuit the computation of a parent RDD.

The scheduler then launches tasks to compute the missing partitions from each stage until it has computed the target RDD.

==For wide dependencies we materialise intermediate records== on the nodes holding parent partitions to simplify fault recovery. 

We do not yet tolerate scheduler failures, though replicating RDD lineage would be straight-forward.

### Memory Management:
Spark provides three options for storage of persistent RDDs:
1. in memory storage of deserialised JAVA objects
2. in memory storage as serialised data
3. on disk storage

To manage the limited memory available, we use an ==LRU eviction policy at the level of RDDs==. We evict a partition from the least recently accessed RDD, unless this is the same RDD as the one with the new partition.

### Support for Checkpointing:
Although lineage can always be used to recover RDDs after a failure, such recovery may be time-consuming for RDDs with long lineage chains.

Checkpointing is useful for RDDs with long lineage graphs containing wide dependencies.

Spark currently provides an API for checkpointing (a REPLICATE flag to *persist*), but ==leaves the decision of which data to checkpoint to the user==.

**We are also investigating how to perform automatic checkpointing.**

