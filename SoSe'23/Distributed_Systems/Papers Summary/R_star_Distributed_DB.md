- Paper deals with the transaction management aspect of the R* distributed database system.  
- Concentrates primarily on description of commit protocols
- PA - Presumed Abort and PC - Presumed commit are extensions of 2PC
- Paper also discusses approach towards distributed deadlock detection and resolution


## Introduction:
In a distributed database system, the actions of a transaction (an atomic unit of consistency and recovery) may occur at more than one site.

A distributed transaction commit protocol is required in order to ensure either that all the effects of the transaction persist or that none of the effects persist, despite intermittent site or communication link failures.

Guaranteeing uniformity requires that certain facilities exist in the distributed database system. We assume that each process of a transaction is able to provisionally perform the actions of the transaction in such a way that they can be undone if the transaction is or needs to be aborted.

Each database of the distributed database system has a log that is used to recoverably record the state changes of the transaction during the execution of the commit protocol and the transaction’s changes to the database (the UNDO/REDO log)

When a log record is written, the write can be done synchronously or asynchronously. 
- In the former case, called ==forcing a log record==, the forced log record and all preceding ones are ==immediately moved from the virtual memory buffers to stable storage==.
- in the asynchronous case, the record gets written to virtual memory buffer storage and is allowed to migrate to the stable storage later on
An important point to note is that ==a synchronous write increases the response time of the transaction== compared to an asynchronous write.


Some of the desirable characteristics in a commit protocol are:
1. guaranteed transaction atomicity always
2. ability to “forget” outcome of commit processing after a short amount of time
3. minimal overhead in terms of log writes and message traffic
4. optimised performance in the no-failure case
5. exploitation of completely or partially read-only transactions
6. maximising the ability to perform unilateral aborts

Some of the desirable characteristics in a distributed deadlock detection:
1. all deadlocks are resolved in spite of site and link failures
2. each deadlock is detected only once
3. overhead in terms of messages exchanged is small
4. once a distributed deadlock is detected the time taken to resolve it (by choosing a victim and aborting it) is small.

## The 2 Phase Commit Protocol:
The model of a distributed transaction execution is such that there is one process, called the **coordinator**, that is connected to the user application and a set of other processes, called the **subordinates**.

![[2PC-model.svg]]
### 2P Normal Operation: /WO Failure

#### I. First Phase:
1. The coordinator receives a commit-transaction command from the user
2. coordinator sends ==PREPARE== messages, in parallel, ==to the subordinates== to determine whether they are willing to commit the transaction
3.a. subordinates that are willing to commit:
	1. force-write a prepare log record
	2. sends a YES VOTE to the coordinator
	3. wait for the final decision from the coordinator.

3.b. subordinates that are not willing to commit:
	1. force-writes an abort record
	2. sends a NO VOTE to the coordinator -> Transaction is definitely aborted
	3. aborts the transaction, releases its locks

After the coordinator receives the votes from all its subordinates, it initiates the second phase of the protocol.

#### Second Phase:
If all the votes were YES VOTES:
1. force-write a commit record (==commit point==)
2. send COMMIT messages to all the subordinates.

If at-least one NO VOTE received:
1. force-write a abort record
2. send ABORT messages to all the subordinates that said YES or still haven not replied to PREPARE

### 2P Operation: With Failure

**Sub-ordinates Timeout**:
timeout while waiting for a decision (commit/abort) from the TC
- replied with NO: ABORT
- replied with YES: hand over to the recovery process

**TC Timeout**:
- timeout while waiting for a vote: abort the transaction 
- timeout while waiting for an ACK: hand over to the recovery process.

**Recovery Process:**
- In prepared state: try to contact the coordinator site to find out how the transaction should be resolved
- In committing state: periodically try to send the COMMIT to all the subordinates that have not acknowledged and awaits their ACKs.
- In aborting state: periodically try to send the ABORT to all the subordinates that have not acknowledged and awaits their ACKs.
- If no state known: ABORT


If any other subordinate enquires about the status of the transaction to the recovery process then:
- if committing/aborting: Reply accordingly
- if no information: ABORT (==This is the scenario that influences the PA and PC protocol==)

### Hierarchical 2P:
In the hierarchical version of 2P:
- the root process that is connected to the user/application acts only as a coordinator 
- the leaf processes act only as subordinates
- the non-leaf, non-root processes act as both coordinators (for their child processes) and sub-ordinates (for their parent processes)

## PA - Presumed Abort:
If no state is known for a transaction then we reply to an enquiring process with an ABORT. Then, it becomes safe for a coordinator to “forget” a transaction and write an abort record immediately after it makes the decision to abort it.
- abort record need not be forced written
- NO ACKs are required for the ABORT

## PC - Presumed Commit:
Since most transactions are assumed to commit, could we make commits cheaper. 

We modify recovery such that if no information is known about the transaction then we reply to an enquiring process with COMMIT instead of ABORT.

Thus:
- commit record need not be forced written
- NO ACKs are required for the COMMIT

However, there is a problem:
- a root process has sent the PREPARES
- one subordinate has gone into the prepared state
- before the root process is able to collect all the votes and make a decision, the root process crashes.
- When the crashed root process’s site recovers, its recovery process will abort this transaction and “forget” it without informing anyone, since no information is available about the subordinates. 
- When the recovery process of the prepared subordinate’s site then inquires the root process’s site, the latter’s recovery process would respond with a COMMIT, causing an unacceptable inconsistency.

The way out of this problem is for each coordinator to ==record the names of its subordinates safely before any of them could get into the prepared state==. 

Thus, when the coordinator site aborts on recovery from a crash that occurred before the coordinator moved into the prepared state, the restart process will know who to inform (and get ACKs) about the abort.


## Partially or completely READ-only Transactions:
We do not need to know before the transaction starts whether it is read-only or not. If a leaf process receives a PREPARE and it finds that it has not done any updates, then it sends a ==READ VOTE==, releases its locks, and “forgets” the transaction.

The subordinate ==writes no log records==. As far as it is concerned, it does not matter whether the transaction ultimately gets aborted or committed. So the subordinate, who is now known to the coordinator to be read-only, does not need to be sent a COMMIT/ABORT by the coordinator.

A non-root, non-leaf sends a READ VOTE only if its own vote and those of its subordinates’ are also READ VOTES. Otherwise, as long as none of the latter is a NO VOTE, it sends a YES VOTE.

There will not be a second phase of the protocol if the root process is read- only and it gets only READ VOTES.

