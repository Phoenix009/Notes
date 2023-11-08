
### Problem and Motivation:

For constant propagation analysis, we have to store the mapping $\text{Vars} \mapsto Z^{\top}_{\bot}$ for every program point $V$. Thus the space requirement is $|\text{Vars}| \times V$.

To reduce this storage space we can do flow-insensitive constant propagation where instead of storing the analysis for each program point we do it for the entire CFG.

So we solve the single equation that combines the program analysis for each program point into one

$$
d \sqsupseteq \bigsqcup_{i}f_{i}^{\#}(d)
$$

The above equation joins the analysis information for each program point into a single combined analysis information.

This leads to the loss of precision.

SSA - Single Static Assignment provides a hack to work around the imprecision.

### SSA - Single Static Assignment:

In flow insensitive analysis. Each variable has a single static assignment.

So every occurence of an assignment to the same variable ($x \leftarrow \text{val}$) is changed to an assignment to a numbered variable. ($x_{1} \leftarrow \text{val}$)

In expression where the variable is used is replaced with a $\phi(x_{1}, x_{2},..., x_{n})$ that selects the appropriate version of the variable for the expression

### Unconditional Constant Propagation in SSA:

**Domain**: $\mathbb{C} := (\text{Vars} \rightarrow \mathbb{Z}^{\top}_{\bot})$

**Transfer Functions**:

$$
[;]^{\#}C := C
$$
$$
[x \leftarrow e;]^{\#}C := C[x \mapsto [e]^{\#}C]
$$
$$
[x \leftarrow M[e];]^{\#}C := C[x \mapsto \top]
$$
$$
[M[e] \leftarrow x;]^{\#}C := C
$$
$$
[x_{0} \leftarrow \phi(x_{1}, x_{w}, ..., x_{n})]^{\#}C := C[x_{0} \mapsto \bigsqcup_{1 \le i \le n}C(x_{i})]
$$

**Solve Single Inequality by fixpoint iteration**:

$$
C \sqsupseteq \bigsqcup_{i} f^{\#}_{i}(x_{i})
$$


### Conditional Constant Propagation on SSA:

Considers control flow as well. Perform two analysis in parallel.

Cooperation between two domains:

$$
\mathbb{C}:\text{Vars} \rightarrow \mathbb{Z}^{\top}_{\bot} \\

\mathbb{U}: \text{Blocks} \rightarrow \{ \top_{U}, \bot_{U} \}
$$

Two transfer functions per program point:

$$
f^{\#}_{i}: \mathbb{C} \times \mathbb{U} \mapsto \mathbb{C}\\

g^{\#}_{i}: \mathbb{C} \times \mathbb{U} \mapsto \mathbb{U}
$$

Solve system of equations:

$$
x \sqsupseteq \bigsqcup_{i}f^{\#}_{i}(x_{i}) \\

y \sqsupseteq \bigsqcup_{i}g^{\#}_{i}(y_{i})
$$

This analysis is also known as SCCP - Sparse Conditional Constant Propagation.


### $\phi$ function semantics:

$\phi$-functions are:

1. evaluated simultaneously \ in parallel, 
2. always placed at the beginning of the block 
3. are non-strict, meaning that it does not require all the inputs to be defined. 

If some of the inputs are unavailable to the $\phi$ function then it simply does not consider it for the evaluation

Consider the following code example (left) that can be transformed into code blocks in SSA form (right)

```cpp
x = ...
y = ...

while(some_condition):
	t = x;
	x = y;
	y = t;

...

```

```cpp
x1 = valx1
y1 = valy1

while(condition)
	x3 = phi(x1, x2)
	y3 = phi(y1, y2)
	t = x3
	x2 = y3
	y2 = t

...

```

After using copy propagation analysis we can reduce the SSA form to following

```cpp
x1 = valx1
y1 = valy1

while (condition)
	x3 = phi(x1, y3)
	y3 = phi(y1, x3)

...
```

The value that the $\phi$ function evaluates to depends on the edge that the control takes to enter the block.

The above code can be understood as following

```cpp
g() {
	x = val;
	y = val;
	f(x, y)
}

f(x, y) {
	t = x;
	x = y;
	y = t;
	if (condition)
		f(x, y)
}
```

So here depending which function calls the method the input for th x and y arguments is decided. This in essence describes the working of the $\phi$ function.

#### Where to place $\phi$  functions?

1. SSA program $P'$ has the same semantics as the original program $P$
2. Every variable has a single program point where it is defined
3. Every use of an SSA variable is dominated by its definition


#### Join Points:

Definition:

To paths $p: X_{0} \rightarrow^+ X_j$ and $q: Y_{0} \rightarrow^+ Y_k$ converge at a program point $Z$ if

1. $X_0 \neq Y_0$
2. $X_j = Y_k$
3. The path is disjoint at all other points

The above points mean: Starting from two distinct points if i reach at a common point with no other intersection occuring before than this common point is the join point.

A program point $Z$ needs a $\phi$ function for a variable $a$ if it is the convergence point of two program points $X_0$  and $Y_0$ each having a definition for variable $a$

Formally, $J(S) = \{Z | X, Y \in S, \text{converge at } Z\}$

$J(\text{defs}(a))$ is the set of program points where $\phi$ functions have to be placed for non SSA variable $a$


**Now we know where to place the $\phi$ functions. but how to efficiently find such program points?**

#### Dominance Frontiers:

**Definition**:

the dominance frontier for a program point $x$ is defined as the set of all program points $y$ such that $y$ is not dominated by $x$ but all the predecessors of $y$ are.

**Theorem**:

$$
\text{DF}^+(X) = J(X \cup root)
$$

Note: $\text{DF}^+$ is the iterative dominance frontier. We start with a program point that has a dominance frontier. We take one node from the frontier and find its dominance frontier, eventually we will 

There is a proof for the above stated theorem which can be referred here: [https://www.youtube.com/watch?v=UjXTrWjikBQ&list=PLSTUeTS8gQK6eExwf5AMvkxYD6pIWto0w&index=12](https://www.youtube.com/watch?v=UjXTrWjikBQ&list=PLSTUeTS8gQK6eExwf5AMvkxYD6pIWto0w&index=12)

#### Computing Dominance Frontier:

Efficiently computed by a bottom up traversal over the dominance tree:

1. Each control flow successor $Z$ of $X$ is either dominated by $X$ or not
2. If not dominated then it belongs to the $\text{DF}(X)$
3. If yes then we look at the dominance frontier of $Z$, for nodes that belong to $\text{DF}(Z)$ and are not dominated by $X$ are also in $\text{DF}(X)$

#### SSA Construction Algorithm:

1. Compute Dominance Tree
2. Compute Dominance frontiers for each block.
3. Compute iterated dominance frontiers $\text{DF}^+(X)$ for all definitions of each variable. We place $\phi$ functions for variable at their respective join points.
4. Rename variables:For every use of a variable we find the lowest definition in the dominance tree and use that definition for the variable.