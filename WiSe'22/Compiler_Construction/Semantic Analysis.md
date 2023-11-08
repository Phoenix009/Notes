
### Parse Trees and AST:

Parse trees contains terminals that are used to make the grammar non-ambiguous. Ex: semicolons are used to make the grammar unambiguous.

We also remove non-terminals for chain productions. 
$A \rightarrow B \cdots \rightarrow C \rightarrow a \Longrightarrow A \rightarrow a$

 

### Semantic Analysis:

1. Refute at compile time the programs that potentially get stuck
2. Infer information about program helpful for compiler

### Name Analysis:
Ensures each occurrence of an entity refers to a unique defining occurrence.

Every entity (variable, function, data-type) has a defining occurrence which defines the name, properties and scope (lifetime) / validity of that entity.

#### Validity:
The validity/scope of a definition of an identifier is the part of the program in which an applied occurrence of that identifier refers to it.

#### Qualification:
Some entities are composed of other entities. Their definition opens scope for other entities.

WE use qualification (.) to refer to the constituents.

#### Multiple NameSpaces:

Programming languages allow multiple namespaces where more than one definition of an identifier can be valid and visible at a time. Must be cleared from the context which occurrence is used.

#### Symbol Definition Table:

Typically, a stack of environments is used to handle visibility and detect valid occurrences for applied occurrences.

### Type Inference:

Often types of variables are uniquely identified by the way the variable are used. We use this information to infer types and avoid declarations.

We define a relation:

$$
\Gamma \vdash e:t \subseteq (\text{Id} \rightarrow \text{type}) \times \text{Expr} \times \text{Type}
$$

this indicates that under the environment $\Gamma$ which maps identifiers to types, an expressions $\text{Expr}$ maps to type $\text{Type}$

We then define the type rules over the Syntax that we can use inductively to infer the types of the expressions.

Ex:
$$\Gamma \vdash c : t_{c} $$
$$\Gamma \vdash []: t \ \text{list}$$
$$\Gamma \vdash x: (\Gamma x) \ \ \ \text{lookup the type of }x \text{ in } \Gamma$$

Given an environment $\Gamma$, the rules can be used to check if the environment leads to correct typing

### How do we find all valid types for an expression?

We build an equation system to describe all valid types for an expression.

We introduce type variable ($t_{b}$) for the types of expression with unknown types.

Let $\alpha[e]$ be the type variable for expression $e$.

A solution $\sigma$ is a substitution/unifier (map of type variable to concrete types) 

#### Correctness:

Let $e$  be an expression, $V$ be the set of variables in that expression and $E$ be the equation system for $e$, then

1. If $\sigma$ is a solution of $E$, then there is a derivation 
$\Gamma \vdash e:t$ with
$$\Gamma = \{ x\mapsto \sigma(\alpha[x]) | x \in V \}$$


#### Computing the most general unifier: (Using Algorithm $W$):

The algorithm $W$ traverses the expression tree in a post-order fashion adding entries to the candidate unifier for the subexpressions that it visits.