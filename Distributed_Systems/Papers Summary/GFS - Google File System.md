
## Design Overview:

### Assumptions:
- The system is built from many inexpensive commodity components that often fail.
- Multi-GB files are the common case and should be managed efficiently. Small files must be supported, but we need not optimise for them.
- The workloads primarily consist of two kinds of reads: large streaming reads and small random reads.
- The workloads also have many large, sequential writes that append data to files.
- System must efficiently implement well defined semantics for multiple clients that concurrently append to the same file
- High sustained bandwidth is more important than low latency. 

### Interface:
GFS provides a familiar file system interface though it does not implement a standard API such as POSIX. We support the usual operations to *create*, *delete*, *open*, *close*, *read* and *write*. 

GFS also provides *snapshot* and *record append*. *Snapshot* allows creation of copy of a file or a directory tree at low cost. *Record Append* allows multiple clients to append data to the same file concurrently while guaranteeing the atomicity of each individual clients append.

### Architecture:
- A GFS cluster consists of a single master and multiple chunk servers and is accesses by multiple clients.

> [!info]
> It is easy to run both a chunk server and a client on the same machine, as long as machine resources permit and the lower reliability caused by running possibly flaky application code is acceptable.

- Files are divided into a fixed-size chunks. Each chunk is identified by an immutable and globally unique 64-bit chunk handle assigned by the master at the time of chunk creation.
- Each chunk is replicated on multiple chunk servers. By default, we store three replicas, though users can designate different replication levels for different regions of the file namespace.
- The master maintains all file system metadata. This includes the namespace, access control information, the mapping from files to chunks and the current locations of chunks.
- GFS client code implements the file system API and communicates with the master and chunk servers to read or write data on behalf of the application. 

### Single Master:
Having a single master vastly simplifies our design and enables the master to make sophisticated chunk placement and replication decisions using global knowledge. 

We must however minimise the involvement in reads and writes so that it does not become a bottleneck. Clients never read and write data through the master.
Instead a client asks the master which chunk servers it should contact. It caches this information for a limited time and interacts with the chunk servers directly for many subsequent operations.

### Chunk Size:
Chunk size is one of the key design parameters. We have chose 64MB which is much larger than a typical file system block size.

A large chunk size offers several important advantages:
- Reduces client master interaction as operations on the same chunk requires only one initial request to the master
- reduces the size of the metadata stored on the master.

On the other hand, a large chunk size, even with lazy space allocation, has its disadvantages:
- A small file consists of a small number of chunks possibly only one. The chunk server storing this chunk may become a hotspot if many clients are accessing the same file.
- internal fragmentation.

### Metadata:
Master stores three major types of metadata:
- file and chunk namespace
- mapping from files to chunks
- location of each chunks replicas

The first two types are kept persistent by logging mutations to an *operation log* stored on masters local disk and replicated. Master does not store chunk location information persistently. Instead, it asks each chunk server about the chunks it stores at master startup.

#### In-Memory Data Structures: 
Since metadata is stored in memory, master operations are fast. It is also easy and efficient for the master to periodically scan through its entire state in the background. This scanning is done to implement chunk garbage collection, re replication in the presence of chunk server failures, and chunk migration to balance load and disk space across all servers.

One potential concern for this memory-only approach is that the number of chunks and hence the capacity of the whole system is limited by the size of the memory at the master.  This is not a serious limitation in practice. The master maintains less than 64B if metadata for each 64MB chunk.

#### Chunk Locations:
The master does not keep a persistent record of which chunk servers have a replica of a given chunk. It simply polls chunk servers for that information at startup. The master can keep itself up-to-date thereafter because it controls all chunk placement and monitors chunk server status with regular HeartBeat messages.

#### Operation Log:
Log contains a historical record of critical metadata changes. Operation log is critical and is stored reliably. We replicate the logs on multiple remote machines and respond to a client operation only after flushing the corresponding log record to disk both locally and remotely.

The master checkpoints its state whenever the log grows beyond a certain size so that it can recover by loading the latest checkpoint from local disk and replying only the limited number of log records.

### Consistency Model:

#### Guarantees by GFS:
- A file region is ==consistent== if all clients will always see the same data, regardless of which replicas they read from.
- A region is ==defined== after a file data mutation it is consistent and clients will see what the mutation writes in its entirety. When a mutation succeeds without interference from concurrent writers, the affected region is defined.
- A failed mutation makes the region ==inconsistent== and hence ==undefined==

Data mutations may be *write*s or *record_append*s. After a sequence of successful mutations, the mutated file region is guaranteed to be defined and contain the data written by the last mutation. GFS achieves this by 
1. Applying mutations to a chunk in the same order on all its replicas
2. using chunk version numbers to detect any replica that becomes state because its chunk server was down. These replicase will not be involved in mutation and will be garbage collected.

Since clients cache location they may read from a state replica. This is limited by the cache entry's timeout. Component failures can also corrupt data. These failures are detected by GFS using regular handshakes. Once an inconsistency is detected, the data is restored from a valid replica. 

#### Implications for Applications:
GFS applications can accommodate the relaxed consistency model with a few simple techniques: 

Replying on appends rather than overwrites: Practically all the GFS applications mutate a file by appending rather than overwriting.

Checkpointing: This may also include application-level checksums. Readers only read the file region up to the last checkpoint. Checkpointing allows writers to restart incrementally and keeps readers from processing successfully written data that is still incomplete from the applications perspective,


## System Interactions:
We now describe how the client, master and chunk servers interact to implement data mutations, atomic record append and snapshot.

![[GFS_Operation_Flow.png]]

### Leases and Mutation Order:
A ==mutation== is an operation that changes the contents or metadata of a chunk such as a write or an append operation. Each mutation is performed at all replicas.

The master grants a chunk lease to one of the replicas which we call the ==primary==. The primary picks a serial order for all mutations to the chunk. All replicas follow this order when applying mutations. 

Thus, the global mutation order is defined first by the lease grant order chosen by the master, and within a lease by the serial numbers assigned by the primary.

If a write by application is large or straddles a chunk boundary, GFS client code breaks it down into multiple write operations which may be interleaved with other concurrent writes. Therefore, the file region is consistent but undefined as mentioned in [[#Guarantees by GFS]] 

### Data Flow:
We ==decouple the flow of data from flow of control== to use the network efficiently. While control flows from the client to the primary and then to all secondaries, data is pushed linearly along a carefully picked chain of chunk servers.

To avoid bottlenecks and high-latency links as much as possible, each machine forwards the data to the closest machine in the network topology that has not received it.

### Atomic Record Appends:
GFS provides an atomic append operation called *record_append*. In a record append, GFS appends the record to the file ==at least once== atomically at an offset of GFS's choosing and returns that offset to the client. Clients would need additional complicated and expensive synchronisation for example through a distributed locking, if they do so with a traditional writes.

Record append is a kind of mutation and follows the control flow described in the above figure with only a little extra logic at the primary. The primary checks to see if appending the record to the current chunk would cause the chunk to exceed the maximum size (64 MB). If so, it pads the chunk to the maximum size, tells secondaries to do the same, and replies to the client indicating that the operation should be retried on the next chunk.

If a record append fails at any replica, the client retries the operation. As a result, replicas of the same chunk may contain different data possibly including duplicates of the same record in whole or in part.

### Snapshot:
The snapshot operation makes a copy of a file or a directory tree (the “source”) almost instantaneously, while minimising any interruptions of ongoing mutations.

We use standard copy-on-write techniques to implement snapshots:
- When the master receives a snapshot request, it first revokes any outstanding leases on the chunks in the files it is about to snapshot. This ensures that any subsequent writes to these chunks will require an interaction with the master to find the lease holder.
- After the leases have been revoked or have expired, the master logs the operation to disk. It then applies this log record to its in-memory state by duplicating the metadata for the source file or directory tree. The newly created snapshot files point to the same chunks as the source files.
- The first time a client wants to write to a chunk C after the snapshot operation, it sends a request to the master to find the current lease holder. 
- The master notices that the reference count for chunk C is greater than one. It defers replying to the client request and instead picks a new chunk handle C’. It then asks each chunk server that has a current replica of C to create a new chunk called C’
- From this point, request handling is no different from that for any chunk

> [!info]
> By creating the new chunk on the same chunk servers as the original, we ensure that the data can be copied locally, not over the network

## Master Operation:
### Namespace Management and Locking:
GFS logically represents its namespace as a lookup table ==mapping full pathnames to metadata==. With prefix compression, this table can be efficiently represented in memory.

Each node in the namespace tree has an associated read/write lock. Each master operation acquires a set of locks before it runs. Typically, if it involves $\texttt{/d1/d2/.../dn/leaf}$, it will acquire read-locks on the directory names $\texttt{/d1}$, $\texttt{/d1/d2}$, ..., $\texttt{/d1/d2/.../dn}$, and either a read lock or a write lock on the full pathname $\texttt{/d1/d2/.../dn/leaf}$.

Since the namespace can have many nodes, read-write locks are allocated lazily and deleted once they are not in use. also, locks are acquired in a consistent total order to prevent deadlock: are first ordered by the level in the namespace and lexicographically within the same level.

### Replica Placement:
The chunk replica placement policy serves two purposes: maximise data reliability and availability, and maximise network bandwidth utilisation.

We must then spread chunk replicas across racks. This ensures that some replicas of a chunk will survive and remain available even if an entire rack is damaged or offline.

It also means that traffic, especially reads, for a chunk can exploit the aggregate bandwidth of multiple racks. On the other hand, write traffic has to flow through multiple racks, a tradeoff we make willingly.


### Creation, Re-replication and Rebalancing:
Chunk replicas are created for three reasons: chunk creation, re-replication, and rebalancing.

When the master creates a chunk it chooses where to place the replicas. It considers several factors:
1. Choose chunk servers with below average disk utilisation
2. Limit number of "recent" creations on each chunk server
3. Spread replicas of a chunk across racks

Master re-replicates a chunk as soon as the number of available replicas falls below a user-specified goal. Each chunk that needs to be replicated is prioritised based on several factors. The master picks the highest priority chunks and clones it by instructing some chunk server to copy the chunk data directly from an existing valid replica.

Finally the master rebalances replicas periodically: it examines the current replica distribution and moves replicas for better disk space utilisation and load balancing.

### Garbage Collection:
When a file is deleted by the application, the master logs the deletion immediately just like other changes. However instead of reclaiming resources immediately, the file is just renamed to a hidden name.

During the master’s regular scan of the file system namespace, it removes any such hidden files if they have existed for more than three days (configurable). When the hidden file is removed from the namespace, its in- memory metadata is erased. This effectively severs its links to all its chunks.

In a similar regular scan of the chunk namespace, the master identifies orphaned chunks (i.e., those not reachable from any file) and erases the metadata for those chunks. In a HeartBeat message regularly exchanged with the master, each chunk server reports a subset of the chunks it has, and the master replies with the identity of all chunks that are no longer present in the master’s metadata. The chunk server is free to delete its replicas of such chunks.

### Stale Replica Detection:
For each chunk, the master maintains a chunk version number to distinguish between up-to-date and stale replicas. Whenever the master grants a new lease on a chunk, it increases the chunk version number and informs the up-to-date replicas. The master removes stale replicas in its regular garbage collection.

As another safeguard, the master includes the chunk version number when it informs clients which chunk server holds a lease on a chunk or when it instructs a chunk server to read the chunk from another chunk server in a cloning operation. The client or the chunk server verifies the version number when it performs the operation so that it is always accessing up-to-date data.

## Fault Tolerance and Diagnosis:
### High Availability:
We keep the overall system highly available with two simple yet effective strategies: fast recovery and replication.

### Data Integrity:
Each chunk server uses checksumming to detect corruption of stored data. We can recover from corruption using other chunk replicas, but it would be impractical to detect corruption by comparing replicas across chunk servers.

Moreover, divergent replicas may be legal: the semantics of GFS mutations, in particular atomic record append as discussed earlier, does not guarantee identical replicas. Therefore, each chunk server must independently verify the integrity of its own copy by maintaining checksums.

A chunk is broken up into 64 KB blocks. Each has a corresponding 32-bit checksum. Like other metadata, checksums are kept in memory and stored persistently with logging, separate from user data.

For reads, the chun kserver verifies the checksum of data blocks that overlap the read range before returning any data to the requester.

