
1. **RPC - remote procedure call**:
	- Helps to invoke procedures on a remote server
	- Makes it appear to be like an ordinary procedure call
	- Uses client, client stub, RPCRuntime, server stub and server

2. **RPC Semantics**:
	1. **At-least-once**:
		- Retransmit message: YES
		- Duplicate Filtering: NO
		- Re-execute procedures
		- Suitable for operations that do not update state
		  
	2. **At-most-once**:
		- Retransmit message: YES
		- Duplicate filtering: YES
		- Retransmit Reply

3. **Strict Consistency**:
	- Loads will always see most recent store made by any machine

5. **Linearizablity**:
	- All replicas execute operations in ==some total order that preserves the real time ordering between operations==
	- If operation ==A completes before B begins== then A is ordered before B in real-time
	- If neither A nor B completes before the other begins, then there is no realtime order.

6. **Sequential Consistency**:
	- All replicas execute operations in some total order which ==preserves the process ordering between operations==
	- If process P issues operation A before operation B, then A is ordered before B by the process order (i.e., ==preserves FIFO ordering==) 
	- If operations A and B are done by different processes then there is no process order between them. But there must be some total order.
	- Is ==not network partition tolerant==

7. **Causal Consistency**:
	- ==Partially orders all operations==, does not totally order operations
	- Writes that are causally related must be seen by all processes in same order
	- concurrent writes may be seen in a different order on different processes and thus ==no guarantees that replicas will converge==

8. **Eventual Consistency:**
	- all replicas eventually converge to the same state
	- Tentative reads

10. **MapReduce**:

11. **GFS**:
	- Sequential consistency across replicas for individual chunks
	- For each chunk, Master designates one replica as primary. Primary orders mutations to that chunk; other replicas follow that order.

12. **Commit Point**:
	- We arrange our transactions so that there is an atomic commit write for the whole transaction. This write is called the commit point.
	- If we crash before the commit point we revert the changes. If we crash after the commit point we always commit.

13. **2-Phase-Commit** :
	- Phase 1: All parties log "YES" and reply to TC, 
	- Phase 2: TC logs "COMMIT" if all "YES"
	- Phase 2 is the commit point.

14. **Presumed Abort and Presumed Commit**

15. **Creating a new file**:
	- Mark free blocks as occupied in bitmap
	- Initialise Inode
	- Add Inode to the directory

16. **WAL - Write Ahead Logging**:
	- Before persisting any change in the store, the log records that reflect the changes to the state has to be first persisted

17. **Group Commit**:
	- Non-persistent commit points: When `active = 1` is written on the cache.
	- Persistent Commit Point: When `active = 1` log is persisted on disk

18. **Fail Stop Failures**:
	- Component suddenly stops functioning and does not perform any further actions in the system
	- System can detect the failure reliably

19. **State Machine Replication**:
	- If two deterministic state machines are started in the same initial state and provided the exact same inputs in the same order, then they will go through the same sequences of states and produce the same outputs.
	- Non-deterministic operation breaks this

20. **Output Requirement for RSM**:
	- If the backup VM takes over a failed primary, the backup will continue executing in a way that is entirely consistent with all outputs that the primary VM has sent to the external world
	- **Output rule**:
		- The primary VM may not send an output to the external world, until the backup has received and acknowledged the log entry associated with the operation producing the output
	

22. **ZooKeeper**:
	- Provides a generic API for building a Master service for any application.

23. **Consistent Hashing**:
	- Consistent hashing  is a special kind of hashing technique such that when a hash table is resized, only $n/m$ keys need to be remapped on average where $n$ is the number of keys and $m$ is the number of slots

24. **Sparks RDD - Resilient Distributed Database**:
	- Suitable for batch applications that do coarse grained operations
	- Are read-only partitioned collection of records.
	- Can only be created through deterministic operations on either data in stable storage or on existing RDDs.

25. **Naiad**:
	- Provides an abstraction that is suitable for batch, stream and graph processing altogether
	- `OnNotify` and `NotifyAt` simulate batch operations
	- `SendBy` and `OnSend` simulate stream operations

26. **Test-and-Set**:
	- the test-and-set instruction is an instruction used to write (set) 1 to a memory location and return its old value as a single atomic (i.e., non-interruptible) operation.





