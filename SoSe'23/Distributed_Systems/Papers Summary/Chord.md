## Base Chord Protocol:
Specifies:
- How to find the location of keys
- How new nodes join the system
- How to recover from the failure of nodes

### Overview:
Chord provides fast distributed computation of a hash function mapping keys to the nodes that are responsible for them.

Uses consistent hashing:
- Balances load - all. nodes receive roughly the same number of keys
- When a Nth node joins or leaves the network only a $O(1/N)$ keys are moved

A Chord node needs only a small amount of routing information about other nodes. Because this information is distributed a node resolve the has function by communicating with a few other nodes.

### Consistent Hashing:
The consistent hash function assigns each node and key an m-bit identifier using a base hash function such as SHA-1

A nodes identifier is obtained by hashing its IPAddress
A keys identifier is obtained by hashing the key itself

the identifier length m must be long enough to make the probability of two nodes or keys having the same identifier negligible.

- Identifiers are ordered in an identifier circle modulo $2^m$
- Key $K$ is assigned to the first node whose identifier is equal to or follows the identifier of $k$ in the identifier space
- This node is called the successor node denoted $successor(k)$

To maintain consistent hashing mapping when a node $n$ joins the network, certain keys previously assigned to $n$'s successors now become assigned to $n$

When node $n$ leaves the network, all of its assigned node are reassigned to its successor

### Scalable Key Location:
Each node need only be aware of its successor node on the circle. Queries for a given identifier can be passed around the circle via these successor pointers until they first encounter a node that succeeds the identifier; this is the node the query maps to.

This resolution scheme is inefficient: it may require traversing all $N$ nodes to find the appropriate mapping.

Chord maintains additional routing information to accelerate this process

- let $m$ be the number of bits in the key/node identifiers. Each node, $n$, maintains a routing table with (at most) $m$ entries, called the ==finger table==.
- The $i$th entry in the table at node $n$ contains the identity of the first node, $s$, that succeeds $n$ by at least $2^{i-1}$ on the identifier circle, i.e., $s = successor (n + 2^{i-1} )$, where $1\le i\le m$ (and all arithmetic is$\mod 2^m$ ).
- Finger table contains both the identifier and the IPAddress of the relevant node.

If node $n$ contacts a node $n^\prime$ such that $id$ falls between $n^\prime$ and $successor(n^\prime)$, find_predecessor is done and returns $n^\prime$. Otherwise node $n$ asks $n^\prime$, for the node $n^\prime$ knows about that most closely precedes $id$. Thus the algorithm always makes progress towards the predecessor of $id$.

### Node Joins:
In a dynamic network, nodes can join (and leave) at any time. 

The main challenge in implementing these operations is preserving the ability to locate every key in the network. To achieve this goal, Chord needs to preserve two invariants:
1. Each nodes successor is correctly maintained
2. For every key $k$, node $successor(k)$ is responsible for $k$

To preserve the invariant stated above, Chord must perform three tasks when a node $n$ joins the network:
1. Initialise the predecessor and finger table of new node $n$
2. Update the finger table and predecessors of the existing nodes
3. Notify the higher layer software so that it can transfer state associated with keys that node $n$ is now responsible for
