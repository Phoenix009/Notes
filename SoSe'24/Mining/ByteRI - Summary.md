
# Introduction:

## RSM
- RSM is an ordered collection of state machines where a state machine is identified by its position.
- Digressing from classic state machines, the nodes of a state machine in RSM are classified into two types: atomic or nested
- An atomic node is analogous to nodes in a classic state machine. On the other hand, a nested node is mapped to one of the state machines in the RSM.
- Transition into a nested node amounts to transitioning into the associated state machine and is analogous to invoking the procedure associated with a call site.
- The state of an RSM is given by a pair of a stack and an atomic node, a stack of nested nodes invoked prior to reaching an atomic node. 

We make two changes to the RSM.
1. Transitions are guarded by regular expressions.
2. All edges transitioning into a node have the same guard.

A complete path in an RSM is a sequence of states, starting at the initial state and ending at a terminating state, where all pairs of adjacent states in the sequence are connected by valid transitions. 

The sequence of regular expressions along a complete path represents the set of strings accepted by the corresponding sequence of transitions.


# Approach Overview:
In general current solutions try to answer two core questions regarding the input format:
- Identifying values
- Identifying fields

Previous works have relied on heuristics based on the structure of the program and from well known access patterns making them limited to those choices.

We use heuristics that do not rely on any such patterns.
1. Two contiguous bytes of the input constitute the same value if there exists an instruction that uses those bytes simultaneously
2. Two bytes of the input constitute different values of the same field if those bytes are used by the same set of instructions.

To construct the graph structure, we first partition the input into a sequence of values with the field it belongs to i.e set of instructions.

Now we create a graph with a node for each field and edges reflecting the consecutive ordering in the field sequence.

To derive the token of a field, we take the set of field values and create a regular expression that recognises all of the field values.

# Algorithm:
The algorithm consists of several key steps, each contributing to the construction of an accurate RSM. 

## 1.⁠ ⁠Track Taint
- Dynamic Taint Tracking: 
	- The algorithm begins by using a dynamic taint tracking tool to monitor how input bytes propagate through the program during execution. 
	- Each byte in the input is tainted with its offset, and the propagation is tracked through various CPU instructions and library functions.

- Taint Trace Generation: 
	- The tool produces a taint trace, a sequence of tuples `<Instruction Address, Calling Context, Set of Taints>` that records how tainted bytes are used throughout the program.


## 2.⁠ ⁠Identify Field Values

- Finding Values: 
	- The taint trace is analysed to identify taint intervals, which represent ranges of offsets in the input. 
	- Each taint interval corresponds to a value used by the program.

- Grouping Values: 
	- Values are grouped into fields based on their `Source Index`
	  (`SI`), which is the set of instructions that use those values. Values with the same `SI` are considered part of the same field.

## 3.⁠ ⁠Construct Taint Interval Tree (TIG)
- TIG Construction: 
	- A Taint Interval Graph (TIG) is constructed by performing a transitive reduction of the subset relation among taint intervals. 
	- This graph is then converted into a tree by introducing new intervals to make each parent node complete, covering all offsets in its range.
- Gap Intervals: 
	- Intervals not covered by existing nodes are filled with gap intervals to ensure completeness.

## 4.⁠ ⁠Generate Field Tokens
- Tokenization: 
	- The raw byte values of each field are generalized into tokens, represented as regular expressions. 
	- The `JoinTokens` algorithm is used to combine tokens from different values of the same field.

- Token Joining: 
	- The pairwise joining of tokens creates a representative token for each field, capturing its possible values.

## 5.⁠ ⁠Infer Field Type and Encoding
- Field Type Identification: 
	- The type of each field (e.g., numeric, string) is inferred based on the types of instructions in its `SI`. For example, fields used in arithmetic instructions are identified as numeric.

- Encoding Detection: 
	- For numeric fields, the data encoding (e.g., ASCII, hex) is determined based on the tokens characteristics.

## 6.⁠ ⁠Construct State Machine
- Frontier Identification: 
	- The TIG is analyzed to identify frontiers, which are sets of nodes whose intervals span the entire input range without overlap. 
	- The frontier with the topmost repeating `SI`'s is particularly important for capturing arrays and recursive structures.

- State Machine Nodes and Edges: 
	- Nodes are created for each unique `SI` in the frontier, and edges are added to reflect the ordering of fields. 
	- A start node is introduced, and the `SI` of the last node in the frontier is marked as the final node.

- Recursive Application: 
	- The algorithm may be applied recursively to decompose frontiers with coarse values into finer-grained state machines.

- State Machine Joining: 
	- Multiple state machines inferred from different inputs are joined to create a comprehensive RSM. The `JoinTokens` algorithm is used to combine tokens for corresponding nodes.
