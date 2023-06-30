
### Introduction:
TreadMarks is a distributed shared memory system for standard unix systems. TreadMarks goes to great lengths to reduce the amount of communication performed to maintain memory consistency. 

It ==uses lazy implementation of release consistency==, and it allows ==multiple concurrent writers to modify a page==, reducing the impact of false sharing.

Earlier implementation of DSM had the following problems:
- were not available on general operating systems
- required kernel modifications
- had performance problems
- false sharing

TreadMarks implementation relies on user-level memory management techniques. Uses lazy release consistency and multiple-writer protocols to reduce the impact of false sharing. Focuses on minimizing the amount of communication to keep memory consistent.


### Design:
#### [[Release Consistency]]:
Permits a processor to delay making its changes to shared data visible to other processors until certain synchronization access occur.

#### [[Release Consistency#Lazy Release Consistency - Lazy Update Propagation:|Lazy Release Consistency]]:
In LRC the propagation of modifications is postponed until the time of the ==acquire==.

The pulling of the updates is transitive, when pulling the udpates from the previous owner, all the updates that the previous owner has seen, have to be pulled.

How do we know which of the pulled updates are new and which are already applied?

LRC uses a local counter. Everytime the process does synchronization operation the counter is incremented. The operations of the processres are partially ordered:
1. intervals on single processor are totally ordered by the counter value
2. an operation on processor p precedes an operation on processor q if the acquire on process q corresponds to the release on processor p.

This partial order can be represented by a vector timestamp.
```python
timestamp = [node.counter for node in nodes]
```

A processor computes a new vector timestamp at an acquire according to the pairwise maximum of its vector timestamp and the vector timestamp received from the previous owner.
```python
new_timestamp = max(curr_time[node], prev_time[node]) for every node
```

RC requires that before a processor may continute past an acquire, the updates of all intervals with smaller vectire timestamp must be visible.

Thus, the previous owner sends ==invalidation-set== for all the new operations. This causes the processor to invalidate its copy of pages in the invalidation-set. A subsequent access to that page causes a page fault, which propagaes the modifications.

#### Multiple-Writer Protocols:
==False Sharing==: Occurs when two or more processors access different variables within a page, with at least one of the accesses being a write.

#### Lazy Diff Creation:
In order to capture the modifications to a shared page, it is initially write-protected. At the first write


### Implementation:
#### Data Structures:
- Page Array:
	For each page we store:
	1. current access: no_access, read, write
	2. copy_set: set of proc that have cached this page
	3. an array with an entry for each proc. The array values is a head pointer to the write_notice records received from that processor.

- Proc Array:
	contains for each proc, a pointer to the head of the intervals_records list. This list represents the intervals that the local processor knows about.

- Interval Records:
	contains a pointer to a list of write_notice records for that interval
	
- Write Notice Records:
	contains a pointer to the interval record it belongs to

- Diff Pool:

#### Interval and Diff Creation:
When a lock is released to another processor, or arrival at a barrier, a new interval is created containing a write notice for each page that was twinned since the last synchronization operation.

With lazy diff creation these pages remain writable until a diff request or a write notice arrives for that page. At this time, the actual diff is created, the page is read protected and the twin in discarded.


#### Locks:
All locks have a statically assigned manager. Lock managements is assigned in a round robin fashion. All lock acquire requests are directed to the manager.

##### Lock Acquire:
Request arrives at the processor that holds the lock or did the last release.

When the lock is released, the releaser informs the acquirer of all intevals between the vector timestamp in the acquirers lock request message and the releasers current vector timestamp.

the message contains:
	1. the processor id
	2. vector timestamp
	3. write notices

The write notices can be obtained on the releasers proc by using its ProcArray.


After receiving the message the acquirer updates its data structures, for each interval in the message.
For each interval:
1. appends an interval record to that interval record list
2. For each write notics:
	1. appends a write notice record to the pages write noticee record list
	2. invalidate that page

#### Barriers:
#### Access Misses:
If the faulting processor does not have a copy of the page, it requests a copy from a memober of that pages copyset.

If write notices are present for a page, the faulting processor obtains the missing diffs and applies them. The diffs can be found using the PageArray.

If processor *p* has modified a page during interval *i*, then *p* must have all diffs of all intervals that have a smaller vector timestamp than *i*. 

The message requesting diff needs to be sent only to those processors for which we have a write notices, and for which the timestamp of their most recent interval is not dominated by the timestamp of another processors most recent interval.


#### Garbage Collection:


