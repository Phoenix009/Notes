Chain replication is a new approach to coordinating clusters of fail-stop storage servers. The approach is intended for supporting large-scale storage services that exhibit ==high throughput and availability without sacrificing strong consistency== guarantees


## Introduction:
This paper is concerned with storage systems that sit somewhere between file systems and database systems. 

In particular, we are concerned with storage systems, henceforth called ==storage services==, that:
- store objects
- support query operations to return a value derived from a single object
- support update operations to atomically change the state of a single object according to some preprogrammed, possibly non-deterministic, computation involving the prior state of that object.

Strong consistency asserts:
1. Operations to query and update individual objects are executed in some sequential order.
2. Effects of update operations are necessarily reflected in results returned by subsequent query operations

Strong consistency guarantees are often thought to be in tension with achieving high throughput and high availability.

Chain replication approach to coordinating fail-stop servers, which is the subject of this paper, simultaneously supports high throughput, availability, and strong consistency.

## Storage Service Interface:


## Chain Replication Protocol:
Servers are assumed to be Fail-Stop:
- Each server halts in response to a failure rather than making an erroneous state transitions
- A servers failure can be detected by the environment

With an object replicated on t servers, as many as t−1 of the servers can fail without compromising the object’s availability.

![[Chain-Replication.svg]]

In chain replication, ==the servers replicating state are linearly ordered to form a chain.== The first server in the chain is called the head, the last server is called the tail, and request processing is implemented by the servers roughly as follows:
- **Reply Generation**: The reply for every request is generated and sent by the tail.
- **Query Processing**: Each query request is directed to the tail of the chain and processed there atomically
- **Update Processing**: Each update request is directed to the head of the chain. The request is processed there atomically, then state changes are forwarded along a reliable FIFO link to the next element of the chain, and so on until the request is handled by the tail.

Strong consistency thus follows because query requests and update requests are all processed serially at a single server.

Note that some redundant computation associated with the t-1 servers is avoided in chain replication because the new value is computed once by the head and then forwarded down the chain. 

This forwarding of state changes also means update can be a non-deterministic operation -  the non-deterministic choice is made once by the head.

## Protocol Details:
- `HistobjID` stores all the updates to the state and is defined to be, the value of `HistobjID` stored by tail T of the chain,
- `PendingobjID` is defined to be the set of client requests received by any server in the chain and not yet processed by the tail.


### Processing Requests:
Given the descriptions above for how `HistobjID` and `PendingobjID` are implemented by a chain, the only server transitions that affect the state are:
#### 1. Client Request arrives at Chain:
- Clients send requests to either the head (update) or the tail (query).
- Thus, receipt of r by either adds r to `PendingobjID`


#### 2. Request processed by Tail:
- Execution causes the request to be removed from the set of requests received therefore it deletes the request from `PendingobjID`
- Moreover, the processing of that request by tail T updates `HistobjID`


### Coping with Failures:
We employ a service, called the ==master==, that:
- detects failures of servers,
- informs each server in the chain of its new predecessor or new successor in the new chain
- informs clients which server is the head and which is the tail of the chain


The handling of failures, depends on the following insight about how updates are propagated in a chain.

The sequence of updates received by each server is a prefix of those received by its successor.

**Update Propagation Invariant**: For servers labeled i and j such that $i \le j$ holds (i.e., i is a predecessor of j in the chain) then:
$$Hist^j_{objID} ≼ Hist^i_{objID}$$


#### Failure of the Head:
- This case is handled by the master removing H from the chain and making the successor to H the new head of the chain.
- Deleting server H from the chain has the effect of removing from `PendingobjID` those requests received by H but not yet forwarded to a successor.
- This is consistent with transition T2, and is thus consistent with the clients object view.

#### Failure of the Tail:
- This case is handled by removing tail T from the chain and making its predecessor the new tail of the chain.
- This is again consistent with the clients object view


### Failure of other servers:
- Failure of a server S internal to the chain is handled by deleting S from the chain.
- The master informs both $S$’s successor ($S^+$) and predecessor ($S^-$) of the new chain configuration

This causes the update propagation invariant to fail unless some means is employed to forward update requests received by $S$ to the rest of the chain. The obvious candidate is $S^-$

Each server maintains a list $Sent_i$ of update requests that $i$ has forwarded to its successor but might have not been executed by the tail.

Operations on $Sent_i$ are as follows:
1. When a server forwards a request $r$ to its successor it appends $r$ to $Sent_i$
2. Tail sends $ack(r)$ to its predecessor. Upon receipt of the $ack(r)$, server deletes $r$ from $Sent_i$ and forwards $ack(r)$ to its predecessor

Thus, we can define
**In-process Request Invariant:** if $i \le j$
$$ Hist^i_{objId} = Hist^j_{objId} \oplus Sent_i $$
Thus, the Update Propagation Invariant will be maintained if 
- $S^−$, upon receiving notification from the master that $S^+$ is its new successor, first forwards the sequence of requests in $Sent_{S^−}$ to $S+$. 
- Moreover, there is no need for $S^−$ to forward the prefix of $Sent_{S^−}$ that already appears in $Hist^{S^+}$ .


### Extending a Chain:
A new server could, in theory, be added anywhere in a chain. Adding a server at the tail seems simple.

For a tail $T^+$ the value of $Sent_{T^+}$ is always empty. all that remains is to initialise $Hist^{T^+}_{objID}$ in a way that satisfies the Update Propagation Invariant.

This is accomplished by having $T$ forward the its $Hist^{T}_{objID}$ to $T^+$. This is done concurrently while processing updates.
Once, In-process requests invariant satisfies i.e. 
$$ Hist^T_{objId} = Hist^{T^+}_{objId} \oplus Sent_T $$
$T^+$ can begin serving as the chains tail


