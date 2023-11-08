Sequential consistency and Release Consistency can totally order all operations but need some central coordination. eg: with locks

This does not work with network partitioning, what is the node holding the lock dies?

Causal consistency allows a replica to make state updates without pulling updates from other replicas. However if we did derive from some other replicas stats, we should pass this information on when someone pulls from us.

A system is causally consistent if any two causally related events are seen by all the nodes in their causal order.

Using vector timestamp enforces causal consistency. 

A sync of two causally unrelated nodes always results in a conflict. 


Read: [[File Synchronization with Vector Time Pairs]]