
## Introduction:
- Replicate state so that the service is always available
- If one replica fails use another one

### Cost of Replication:
- Typically, We keep 1 primary copy and N backup copies
- All copies must be kept synchronised: Each backup has the same state as the primary
- All copies should be physically separated
- Expensive: N times the cost

### Failures handled by replication:
1. Fail-Stop Failures:
	- Component suddenly stops functioning and does not recover
	- System can detect the failure reliably
	  
2. Complete loss of a replica:
	- Natural calamities
	- Assume other replicas are unaffected

### Failure NOT handled by replication:
1. Arbitrary failures:
	- Ex: Hardware defect, software bug, misconfiguration
	- System often cannot detect these failures reliably
	  
2. Byzantine Failures:
	- Component behaves arbitrarily intentionally
	- Ex: Security breach
	  
3. Complete loss of ALL replicas

## Replication Techniques:
### 1. State Transfer:
- Copies the primary state over to al replicas
- Simple but expensive as we must send the copies after every state change

### 2. State Machine Replication:
#### Intuition: 
- Abstract the replicas as state machines
- Clients execute operations on the primary
- Primary sequences and sends operations to backups
- All backup replicas execute operations locally

#### Works Because:
- All replicas start at the same initial state
- All replicas execute the same operations in the same order
- Thus, all replicas are in the same end state as the primary

#### Operations - State transfers:
What kind of operations should be allowed? Are there any operations that can cause problems?
- Non deterministic operations can cause problems
	- Breaks the assumptions of our state machine model
	- ex: Random number generator
	- Each execution will lead to a new state


Read: [[VM - Fault Tolerance]], [[Chain Replication]]


