- ZooKeeper, a service for coordinating processes of distributed applications.
- The ZooKeeper interface enables a high-performance service implementation. 
- In addition to the wait-free property, ZooKeeper provides a per client guarantee of FIFO execution of requests and linearisability for all requests that change the ZooKeeper state.

## Introduction:

Large-scale distributed applications require different forms of coordination. 
- Configuration is one of the most basic forms of coordination
- Group membership and leader election are also common in distributed systems
- Locks constitute a powerful coordination primitive that implement mutually exclusive access to critical resources.

One approach to coordination is to develop services for each of the different coordination needs.

Insight:
Services that implement more powerful primitives can be used to implement less powerful ones.

When designing ZooKeeper coordination service, we moved away from implementing specific primitives on the server side, and instead we opted for exposing an API that enables application developers to implement their own primitives. 

Such a choice led to the implementation of ==a coordination kernel== that enables new primitives without requiring changes to the service core.


## ZooKeeper Service:

### Overview:
ZooKeeper provides to its clients the abstraction of a set of data nodes called ==znodes==, organised according to a hierarchal namespace.

**Znodes**:
There are two types of znodes:
	1. Regular: Clients manipulate regular znodes by creating and deleting them explicitly
	2. Ephemeral: Clients can delete them explicitly or are automatically deleted when the session ends.

All znodes store data and can have children except for ephemeral znodes

Znodes are not designed for general data storage, but instead map to abstraction of the client application.
Ex: 
The subtree for Application 1 implements a simple group membership protocol: each client process $p_i$ creates a znode $\texttt{p\_i}$ under $\texttt{/app1}$, which persists as long as the process is running.

ZooKeeper does allow clients to store some information that can be sued for meta-data or configuration in a distributed computation.
Ex:
In a leader based application it is useful for an application server that is just starting to learn which other server is currently the leader. This is stored in a known znode.

**Sessions**:
- A client connects to ZooKeeper and initiates a session. Sessions have an associated timeout.
- ZooKeeper considers a client faulty if it does not receive anything from its session for more than that timeout
- A session ends when clients explicitly close a session handle or ZooKeeper detects that a clients is faulty.

### Client API:
refer the paper :p

### ZooKeeper Guarantees:
Has two basic ordering guarantees
1. **Linearisable writes**: All requests that update the state of ZooKeeper are serialisable and respect precedence
2. **FIFO Client Order**: All requests from a given client are executed in the order that they were sent by the client.

We use ==A-linearisability==:
Clients can have multiple outstanding operations, and we guarantee FIFO order for the outstanding operations for the same client.



### Examples of Primitives:
*How to use the Zookeeper API to implement powerful primitives.*

ZooKeepers ordering guarantees allow efficient reasoning about system state and ==watches== allow for efficient waiting

#### Configuration Management:
Configuration is stored in a znode $z_c$. Process start up with the full pathname of $z_c$. 
- Starting process obtain their configuration by reading $z_c$ with the watch flag = true.
- If the configuration is ever updated, the processes are notified which again read the new configuration with watch flag = true

#### Rendezvous:
It is not always clear apriori what the final system configuration will look like.

- Client want to start a master process and several worker processes. 
- Client does not know ahead of time information such as the address and port# for connecting to the master
- We use a rendezvous node $z_r$, which is a node created by the client
- When the master starts it fills in $z_r$ with information
- Workers read the node with watch flag. If the node is not updated yet, workers wait for the notification.

#### Group Membership:
We take advantage of ephemeral nodes to implement group membership

- We start by designating a znode $z_g$ to represent the group.
- When a process member of the group starts, it creates an ephemeral child znode under $z_g$
- If the process fails or ends, the znode that represents it under $z_g$ is automatically removed
- Processes can obtain group information by simple listing the children of $z_g$.

#### Simple Locks:
The simplest lock implementation uses “lock files”. The lock is represented by a znode. 

- To acquire a lock, a client tries to create the designated znode with the EPHEMERAL flag. 
- If the create succeeds, the client holds the lock. Otherwise, the client can read the znode with the watch flag set to be notified if the current leader dies. 
- A client releases the lock when it dies or explicitly deletes the znode

Implementation has problems:
- **Herd effect**: All waiting clients will vie for the lock when it is released even though only one client can acquire the lock.
- Implements only exclusive locks

##### Locks Without Herd Effect:
Lock:
```
n = create(l + “/lock-”, EPHEMERAL|SEQUENTIAL)
C = getChildren(l, false)  
if n is lowest znode in C, exit  

p = znode in C ordered just before n
if exists(p, true) wait for watch event
goto 2
```

Unlock:
```
delete(n)
```

##### Read Write Locks:
Write Lock:
```
n = create(l + “/write-”, EPHEMERAL|SEQUENTIAL)

C = getChildren(l, false)  
if n is lowest znode in C, exit  
p = znode in C ordered just before n
if exists(p, true) wait for event
goto 2
```

Read Lock:
```
n = create(l + “/read-”, EPHEMERAL|SEQUENTIAL)

C = getChildren(l, false)  
if no write znodes with number less than n, exit
p = write znode in C ordered just before n
if exists(p, true) wait for event
goto 2
```

Unlock:
```
delete(n)
```


## ZooKeeper Implementation:
![[ZooKeeper-Service.svg]]
Upon receiving a request:
- a server prepares it for execution (re- quest processor). 
- If such a request requires coordination among the servers (write requests), then they use an agreement protocol (an implementation of atomic broad- cast), and 
- servers commit changes to the ZooKeeper database fully replicated across all servers of the ensemble.

Every ZooKeeper server services clients. Clients connect to exactly one server to submit its requests. As we noted earlier, read requests are serviced from the local replica of each server database. Requests that change the state of the service, write requests, are processed by an agreement protocol


### Request Processor:
When the leader receives a write request, it calculates what the state of the system will be when the write is applied and transforms it into a transaction that captures this new state. 

The transactions are thus ==idempotent==.


### Atomic Broadcast:
The leader broadcasts the change to the ZooKeeper state through ZAB, an atomic broadcast protocol

ZAB by default uses simple majority quorums to decide on a proposal, so ZAB and thus ZooKeeper can only work if a majority of servers are correct.

ZAB guarantees that changes broadcast by a leader are delivered in the order they were sent and all changes from previous leaders are delivered to an established leader before it broadcasts its own changes.


### Replicated Database:
Each replica has a copy in memory of the ZooKeeper state. When a ZooKeeper server recovers from a crash, it needs to recover this internal state.

ZooKeeper uses periodic snapshot and only requires redelivery of messages since the start of the snapshot. 

We call ZooKeeper snapshots ==fuzzy snapshots== since we do not lock the state to take the snapshot. This results in the snapshot having some subset of changes during the generation of snapshot. Since the state changes are idempotent, we can apply them twice as long as we apply the state changes in order.

