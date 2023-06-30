Class: Compiler Construction
Created: December 1, 2022 12:15 PM
Date: March 5, 2023 11:31 AM
Semester: WiSe'23

**Control Flow Graphs:**

Each instruction in a function has a set of possible successor instruction in that function

$\text{CFG}$ is a Directed Graph $(V, E, r)$

$V$: instructions of the function

$E$: $(v, w) \in E$ iff $v$ is a possible successor of $v$

$r$: is the root node with no predecessor

IR’s typically are intra-functional and control flow between functions is not modelled by CFGs

**Basic Blocks:**

Is a sequence of instructions with the following properties.

1. the first node has multiple predecessors
2. the last node has multiple successors
3. the nodes in the between have exactly one predecessor and one successor

**Critical Edges:**

A edge in the control graph that goes from a basic block with multiple successor to a node with multiple predecessor

**Dominance:**

For a connected $CFG$, a node $v$ dominates node $w$ if for every path from the root node $r$ to the node $w$ passes through node $v$

That is to say if we want to reach $w$   from the root $r$, we have to pass through $v$.

**Theorem1:** Dominance is a Partial Ordered Relation.

Reflexive: Every basic block/node dominates itself

AntiSymmetric: If $v \succeq w$  and $w \succeq v$ then $v = w$

Transitive: if $u \succeq v$ and $v \succeq w$ then $u \succeq w$

**Theorem2:** If $u \succeq w$ and $v \succeq w$ $\Longrightarrow$ either $u \succeq v$ or $v \succeq u$

If there are two different nodes that are dominating a third node, then one has to dominate the other.

**Corollary:**

1. All dominators of a particular node have a total ordering among them
2. Except for the root node $r$ that has no dominators, for every node $v$ there is an immediate dominator $\text{idom}(v)$.
3. $T = (V, \{\text{idom}(v) \ |\ v \in V\}, r)$ is a tree. 

**Post Dominance:**

Dual of the dominance relation.

Consider a CFG with a unique exit node $e$.

A node $w$ post dominates a node $v$ if all paths from $v$ to $e$ have to go through $w$

**SESE - Single Entry Single Exit:**

A set of nodes that are dominated by a node $u$ and are post dominated by a node $v$ form a set of single entry and single exit nodes.

The notion of dominance is important as we want to identify set of nodes that are executed under same control flow conditions.

 

**Control Flow Dependence:**

A node $w$ is control dependent on an edge $(u, v)$ if

1. node $w$ post dominates $v$: Thus if $v$ is executed then $w$ has to be executed.
2. $w \neq u$: Thus, $u$ is not post dominated by $w$: Thus the control condition at node $u$ determines the execution of the node $w$

**Depth First Search:**

Using DFS we can classify each edge $(u, v) \in E$ as:

1. forward edge: $u$  is an ancestor of $v$
2. retreat edge: $v$ is an ancestor of $u$
3. cross edges: otherwise

A DAG form a CFG is obtained by removing all the retreat edges.

**Reducibility:**

A class of practically important CFGs are **Reducible CFG**s

A graph G is called reducible if for every retreat edge $(v, h)$, $h \succeq v$

such an edge is called a back edge and the node $h$ is called header.

Presence of a retreat edge in CFG means that there is a loop. Now if retreat edge is a back edge then the only entry in the loop is through the header node $h$. 

**Theorem:** The following three statements are equivalent.

1. G is reducible
2. G has a unique DAG: To not have a unique DAG there has to be multiple entry in the loop. but here since the header dominates all the nodes in a cycle there is a single entry in the loop.
3. for all the cycles in G, all the nodes in the cycle are dominated by a single node. i.e. header $h$

**Loop Nesting:**

The natural loop of a back edge $(v, h)$ is the set of all the nodes that reach $v$ without going through $h$

Natural loop is a set of the nodes that follow the above condition

**Theorem:**

For two loops $L_{1}$ and $L_{2}$ one of the following conditions hold:

1. $L_{1} = L_{2}$
2. $L_{1} \sube L_{2}$ or $L_{2} \sube L_{1}$
3. $L_{1} \cap L_{2} = \phi$

