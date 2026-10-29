---
title: "Storage"
weight: 1
---
**Underlying Question:**

How to move 'big' data through a computer system?

**Learning Objective:**

1. Storage Heirarchy
2. Replication
3. Caching
4. Buffering
5. Read and Write Strategies

# Storage Heirarchy:

## Properties of perfect computer Memory:

1. Unlimited Capacity
2. Instant random Access
3. Unlimited Bandwidth for sequential access - Meaning i can read and write a huge amount of data per second
4. No transfer const to the CPU
5. For Free ??
6. Persistent, always, forever

## Storage Heirarchy: The principle idea:

```
            |   Registers       |
 Capacity   |   L1 Cache        |
 Access Time|   L2 Cache        |   Cost/Byte
            |   L3 Cache        |   Bandwidth
            |   Main Memory     |
            V   Flash/Hard Disk V
```

The visualization is correct but deceiving. The rate of increase between certain layers is very very large which is not depicted in the heirarchy and can be overlooked

## Tasks of each storage layer

1. Localization of data objects: If a certain data is requested we have to tell if we have that data. If yes we have to return it
2. Caching of data from lower to higher level (inclusion): The storage layers on the highere layers store the copies of the recently accessed data. There are various ways in how this is acheived called Inclusion Principles
3. Data Replacement Stragtegies: If the storage layer gets full we have to decide which data can be removed to make space for the new data.
4. Writing Modified Data (write through , write back): How do you write data in such a heirarchy? All these storage layers have the same tasks, same problems and same algorithms. So we dont have to think of each layer as seperate and we can study them as one single unit
5. Reading and Writing Data: Also pulling up (reading) and pushing down (writing) data.

The changes to the data are made in the registers which have a very high bandwidth. These changes are not persistent. We need to push this data down to make it persistent. However as we go down the layer the bandwidth decreases.

### So how do we make the writes faster?

We try to leverage the sequential access which is more faster than the random access. And we write logs of the changes that are made.

# DB Buffer:

Most DBMS make use of a Database Buffer which sits between the main memory and the hard disk in the heirarchy. The responsibility of the DB Buffer is to decide which data should be kept in the main memory and which data to evict out when the memory gets full

## DB Buffer API:

1. `get(Px)` - returns a reference to the Px in the main memory
2. `fix(Px)` - Marks the Page that it cannot be evicted
3. `unfix(Px)` - Marks the Page that it can be evicted
4. `page_in_buffer(Px)` - checks if the page exists in the buffer
5. `choose_page()` - Is an algorithm that decides which page should be evicted from the buffer and returns the reference

## Spatial Locality and Temporal Locality:

### Spatial Locality:

*Spatial Locality* means that all those instructions which are stored nearby to the recently executed instruction have high chances of execution.

### Temporal Locality:

*Temporal Locality* means that a instruction which is recently executed have high chances of execution again.

To make the DB Buffer operations efficient we want to design algorithms that exploit the Spatial and Temporal Locality

# Hardware - HardDisks:

## Cost of a Random Access:

1. Seek Time - time required for the head to be at the right cylinder
2. Rotational Delay - time required for the head to be at the right sector
3. Transfer Time - Actual transfer time for the read

Steps 1-3 are repeated every time data is accessed. Steps 1 is costly and we would like to avoid it if we can

## Cost of Sequential Access:

1. Seek Time - time required for the head to be at the right cylinder
2. Rotational Delay - time required for the head to be at the right sector
3. Transfer Time - Actual transfer time for the read

In Sequential Access the costly opeartion 1 is done once

# CPU-Storage Architectures:

## Single-Core Storage Architecture:

- Single CPU w/ Single Core
- Single Main Memory
- Single Hard Disk

## Single CPU Multi Core Storage Architecture:

- Single CPU with multiple Core
- Single Main Memory
- Single Hard Disk

## NUMA - Non Uniform Memory Access:

- Multiple CPUs w/ Multiple Cores
- Each CPU has its own main memory but can access other CPUs memory as well
- Single Hard Disk

## RAID - Redundant Array of Inexpensive Disks:

- Single CPU
- Single Main MEmory
- Multiple Hard Disks

### RAID0 - Striping:

Splitting of blocks accross disks increases sequntial and random access time reduces the load on each disk. One of the disk fails the storage fails. with more number of disks used the expected rate of failure also increases

### RAID1 - Full Replication:

Replication of blocks across multiple disks. Increases parallel sequential and random read operations. Write: No improvement If anyone of the disk fails there is no loss of data

### RAID4 - Striping + Parity Disk:

Requires atleast 3 disks

Allow for failure of only one disk

### RAID5 - Striping + Parity + Round Robin arrangement:

Requires atleast 3 disks

Allow for failure of only one disk
