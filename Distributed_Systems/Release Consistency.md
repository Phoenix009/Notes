The use of global locks for all address space can attain sequential consistency if the locks are used properly.

However, use of global locks leads to no concurrency at all. Only one machine can access memory at a time. This issue can be addressed by controlling the lock granularity.

==Variable lock granularity== approach uses one lock per logical object. 

Locks alone dont provide consistency. ==Updates must propagate when a lock is acquired or release by another party==. 

### Release Consistency - Eager Update Propagation:
==Push== updates to all threads when ==releasing== the lock.

Release consistency model permits a processor to delay making its changes to shared data visible to other processors until certain synchronication accesses occur.

### Lazy Release Consistency - Lazy Update Propagation:
==Pull== updates from the previous owner when ==acquiring== lock.

When propagating updates all the updates to all objects are propagated. This propagation of updates can be optimized.

***On Demand Data Transfer - Invalidation***:
Why pull pages that we dont require?

At lock acquire, propagate only list of updates page ids, called ==invalidate set==. The acquirer invalidates these pages in its page table.

Subsequent access on invalid pages triggers a page fault that causes pulling the updates to this particular page.

***Minimal Data Transfer - diffs***:
Why pull entire page when updates are small?

On fault, pull only the parts of the page that were modified. This is implemented using diffs. Only diffs are sent by the previous owner on a pull request.

***Transitivity***:
==Rule==: When pulling updates from the previous owner. All the updates that the previous owner has seen has to be pulled as well.

How do we know which of these updates are new and which are already applied?
Requires a timestamp.

Read: [[TreadMarks]]