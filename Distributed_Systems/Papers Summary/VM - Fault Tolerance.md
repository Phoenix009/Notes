
Provide fault tolerant virtual machines, based on the approach of replicating the execution of a primary virtual machine via a backup virtual machine on another server.

## Introduction
- Primary and backup is a common approach to implement fault-tolerant servers.

- One way of replicating the state on the backup server is to ship changes to entire state of the primary, including CPU, memory and I/O devices to the backup nearly continuously.
- Bandwidth requirement can be==== very large.

- A different method for replicating servers is referred to as state machine approach
- Idea is to model the servers as deterministic state machines that are kept in sync by starting them from the same initial state and ensuring that they receive the same input requests in the same order
- Since some operations are non-deterministic, extra coordination is required to sync the primary and backup

- A VM running a top of a hypervisor is an excellent platform for implementing the state machine approach. 
- Hypervisor has full control over the execution of a VM, including delivery of all inputs, the hypervisor is able to capture all necessary information about non-deterministic operations on the primary and replay these operations on the backup.

- Protocol supports only uni-processor systems
- Similar to most other practical systems, we only attempt to deal with fail-stop failures

## Basic FT Design:


![[VM-Ft.svg]]


### Deterministic Replay Implementation:
- If two deterministic state machines are started in the same initial state and provided the exact same inputs in the same order, then they will go through the same sequence of states and produce the same outputs.

- Non-deterministic events and operations affect the VM's state which provides three challenges for replication:
	1. correctly capturing all the input and non-determinism necessary to ensure deterministic execution on a backup
	2. correctly applying inputs and non-determinism to the backup
	3. do this without degrading the performance.

- ==Deterministic Replay== records the inputs of a VM and all possible non-determinism associated with the VM execution in a stream of log-entries written to a log file
- The VM execution may be exactly replayed later by reading the log entries from the file.

### FT Protocol:
- We use deterministic replay to produce the necessary log entries to record the execution of the primary VM, but instead of writing the logs to disk, we send them to the backup via the ==logging channel==.

- **OUTPUT REQUIREMENT**:
	If the backup VM takes over a failure, the backup VM will continue executing in a way that is entirely consistent with all outputs that primary VM has sent to the client

- Output requirement can be ensured by delaying any external output until the backup VM has received all information that will allow it to replay execution at least to the point of the output operation.
- One necessary condition is that the backup VM must have received all log entries generated prior to the output operation.

- **OUTPUT RULE**:
	the primary VM may not send an output to the client, until the backup has received and acknowledged the log entry associated with the operation producing the output.

- If the backup VM has received all the log entries including the logs entry for the output producing operation, then the backup VM will be able to exactly reproduce the state of the primary VM at that output point.

given the output rule is followed, the backup VM will be able to take over in a state consistent with the primary's last output


### Detecting and Responding to Failure:
#### Responding to Failures: 
If the backup VM fails:
- Primary VM goes live - Stops recording mode

If Primary VM fails:
- Continue replaying the execution from the log entries until the end
- backup VM goes live

When backup VM becomes primary all the output will be sent to the client. This requires device specific operations like advertising the MAC address to update the primary address on the physical layer.

#### Detecting Failures:
- FT uses UDP heart-beating and monitoring log traffic between servers to detect when a server may have crashed
- A failure is declared if heart-beating or logging traffic has stopped for longer than a specific timeout.

Such a failure detection method is ==susceptible for a split-brain problem== if network connectivity between still functioning servers is down.

We must ensure that only one of the VM goes live when a failure is detceted.

When either a primary or backup wants to go live, it executes an ==atomic test-and-set== operation on shared storage.
- If the operation is successful, VM is allowed to go live
- If unsuccessful then the other VM must have already gone live. So VM halts itself.

Using shared storage to remove split-brain situations does not introduce any extra unavailability.



## Practical Implementation:
### Starting and Restarting FT VMs:
- Biggest additional component that must be designed is the mechanism for starting a backup VM in the same state as a primary VM.
- We adapted the existing VMotion functionality which allows the replication of a running VM from one server to another server with minimal disruption. 
- It ==also sets up the logging channel== and causes the source VM to enter the ==logging mode== and the destination VM to enter the ==replay mode==


### Maintaining the Logging Channel:
- Our implementation ==must be designed to minimise the possibility that the primary log buffer fills up==. Since the primary VM must be completely stopped and unresponsive until it can log its entry and continue execution.
- Beyond expecting unexpected pauses, If the primary fails, the backup VM must catch up by replaying all the log entries that it has already acknowledged.
- Time to finish replaying is the execution lag time at the point of failure
- Time for backup to go live equals the failure detection time plus the current execution lag.

- We have a mechanism to slow down primary VM to prevent backup VM from getting too far behind.
- In our protocol along with ACKs we send additional information to determine the execution lag. 
- This allows dynamically slowing and speeding the primary Vm to maintain a small execution lag.

### Implementation Issues for Disk IOs:
- our implementation of disk IO uses DMA to directly access memory of the VM.
- Simultaneous disk operations that access the same memory pages can lead to non-determinism
- Solution is to detect any such IO races and force such racing disk operations to execute sequentially in the same way on the primary and backup

- Disk operations of DMA can race with a memory access by an application. 
- One solution is to set up page protection that results in a trap if a VM happens to make an access to a page that is also a target of an outstanding disk operation.
- Changing MMU protections on pages is an expensive operation, we instead use ==bounce buffers==
- All the write operations are done to the bounce buffers, and the disk is modified to write data from the bounce buffers.


