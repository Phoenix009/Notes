### Introduction:

The Bayou replicated storage system was designed to support a variety of non-real-time collaborative applications.

Bayou storage system provides an infrastructure for collaborative applications that manages the conflicts that arise due to concurrent activity while relying on weak connectivity.

Bayou design requires only occasional, pair-wise communication. Groups of computers may be partitioned away from the rest of the system yet remain connected to one another. supporting disconnected workgroups is a central goal of the Bayou system.

A weak connectivity networking model can be accomodated only with weakly consistent, replicated data. Bayou adopts a model in which clients can read and write to any replica without the need for explicit coordination with other replicas. Every computer eventually receives updates from every other, either directly or indirectly, through a chain of pair wise interactions.

Applications must be aware that they may read weakly consistent data and also that their write operations may conflict with those of the users and applications. Moreover, applications must be involved in the detection and resolution of conflicts since these naturally depend on the semantics of the application.

Bayou’s mechanisms extend this work by letting applications exploit domain-specific knowledge to achieve automatic conflict resolution at the granularity of individual update operations without compromising security or eventual consistency.

Automatic conflict resolution is highly desirable because it enables a Bayou replica to remain available. Bayou does not take the approach of systems that mark conflicting data as unavailable until a person resolves the conflict


### System Model:
Each *data collection* is replicated in full at a number of servers. Access to one server is sufficient for a client to perform useful work. The client can read the data held by that server and submit writes to the server.

Clients need not confine themselves to interacting with a single server. Bayou provides session guarantees to reduce client observed inconsistencies when accessing different servers.

The storage system at each server conceptually consists of an ordered log of writes plus the data that results from the execution of these writes.

Bayou servers propagate Writes among themselves dur- ing pair-wise contacts, called ==anti-entropy sessions==. In the absence of new writes, all servers will eventually converge.

### Conflict Detection and Resolution:
A basic tenet of our work is that storage systems must provide means for an application to specify its notion of a conflict along with its policy for resolving conflicts.

The Bayou system includes two mechanisms for automatics conflict detection and resolution that are intended to support arbitrary applications: ==dependency checks== and ==merge procedures==

#### Dependency Checks:
Application specific conflict detection is accomplished in the Bayou system through the use of a dependency check. Dependency checks can be used to detect when two users update the same data item without one of them observing the others update.

Bayous dependency checking mechanism is more powerful than the traditional use of version vectors since ==it can also be used to detect read-write conflicts==. 

Each write operation can explicitly specify the expected values of any data items on which the update depends, including data items that have been read but are not being updated. thus dependency checks can enforces arbitrary multiitem integrity constraints on the data.

#### Merge Procedures:
Once a conflict is detected, a merge procedure is run by the Bayou server in an attempt to resolve the conflict.

The merge procedure associated with a Write is responsible for resolving any conflicts detected by its dependency check and for producing a revised update to apply.

### Replica Consistency:
While the replicas held by two servers at any time may vary in their contents because they have received and processed different Writes, a fundamental property of the Bayou design is that all servers move towards eventual consistency.

Two important features of the Bayou system design allows servers to achieve eventual consistency. First, Writes are performed in the same, well-defined order at all servers. Second, the conflict detection and merge procedures are deterministic so that servers resolve the same conflicts in the same manner.

When a Write is accepted by a Bayou server from a client, it is initially deemed ==tentative==. Tentative Writes are ordered according to timestamps assigned to them by their accepting servers. Eventually, each Write is committed. Committed Writes are ordered according to the times at which they commit and before any tentative Writes.

The only requirement placed on timestamps for tentative Writes is that they be monotonically increasing at each server so that the pair <timestamp, ID of server that assigned it> produce a total order on Write operations.

Enforcing a global order on tentative, as well as committed, Writes ensures that an isolated cluster of servers will come to agreement on the tentative resolution of any conflicts that they encounter.

Servers must also be able to undo the effects of some previous tentative execution of a Write operation and reapply it in a different order.

### Write Stability and Commitment:
A write operations becomes stable when the set of writes that preced it in the servers write log is fixed. 

In many cases an application can be designed with a notion of confirmation or commitment that corresponds to the Bayou notion of stability.

A Write becomces stable when itis explicitly commited. WE use a primary commit scheme. One server is designated as a primary takes responsibility for commiting updates.

The Bayou design readily accomodates the temporary unavailability of the primary. The inability of a client to communicate with the primary server, for instance if the primary crashes or is disconnected, does not prevent it from performing useful read and write operations. Weites accepted by other servers simply remain tentative until they eventually reach the primary.


### Storage System Implementation Issues:
