
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
