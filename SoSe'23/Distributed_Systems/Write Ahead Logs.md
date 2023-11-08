*How to maintain ==CONSISTENCY== of persistent data in the face of failures.*

## Key Idea for Storage Crash Tolerance:
We order our transactions in such a way that we have a atomic commit for the whole transaction.

- crash before atomic commit: ROLLBACK the transaction. 
- crash after atomic commit:  recover the transaction.


## Write Ahead REDO logs

### Design 1: One transaction at a time:
#### Execution:
- transaction writes changes to cache.

#### Commit:
- Write REDO log records to the disk
- Write the end-log-record (==COMMIT POINT==)
	Contains:
	- Transaction ID
	- \# log blocks
	- Checksum
	- active = 1
- Write transaction blocks to the disk
- REWRITE end-log-record: set active = 0

#### Recovery:
REDO all the log records that have ==active = 1==. 
AFTER flushing the transaction blocks to the disk set active = 0
- It is important to set active = 0 only after flushing the changes to ensure recovery after yet another failure.

#### Challenges:
Duplicate Writes: first to the log, then to the actual locations

### Design 2: Delayed Writes
Idea: Flush transaction blocks to the disk in epochs of N transactions.
If multiple transactions modify the same blocks then we have to write it only once during the flush.

### Design 3: Group commit:
Idea: Commit transactions in a group

#### Execution:
- transaction writes changes to cache.
- log records written to the CACHE

#### Commit:
- Write end-log-record: active = 1

#### Log Flush:
- Periodically flush log records to the disk

#### Transaction Flush:
- Flush the remaining log records of the transactions
- Flush transaction blocks to the disk
- REWRITE the transactions end-log-record. set active = 0

#### Recovery:
REDO all the log records that have ==active = 1==.

#### Challenges:
Duplicate Writes: We are still writing data twice. Once on the log and once on the actual blocks


### Design 4: Copy-on-Write REDO Logs

![[COW.svg]]

The actual data blocks are pointed to from meta data blocks and can be modified to point to the new blocks.

Read: [[Cedar file System - Group commit]]