Read: https://jepsen.io/consistency


### Operations:
An operation is a transition from state to state. A single variable system might have operations like `read` and `write`.

### Consistency:
The rules of what operations can run in parallel and how to combine the results of these operations.

### History:
A History is a collection of operations. We represent history as an ordered list of invocation and completion operations, splitting each operation into two. 

### Consistency Models:
A consistency model is a set of histories. We use consistency models to define which histories are "good" or "legal" in a system. 

Set of rules that the system will enforce which defines how the system behaves when operating on the state.

Consistency Models:
1. [[Strict Consistency]]
2. [[Sequential Consistency]]
3. [[Release Consistency]]
4. [[Causal Consistency]]
5. [[Eventual Consistency]]
