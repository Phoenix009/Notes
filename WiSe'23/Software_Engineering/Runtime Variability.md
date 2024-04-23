# 1. Runtime Parameters

A simple way to implement variability is to use conditional statements (such as if and switch) to alter the control flow of a program at run time. 

==In our context, conditional statements are typically controlled by configuration parameters passed to a method or a module, or set as global variables in a system.==

There are many ways to set configuration parameters:
- **command-line parameters** (such as in `ssh -v`) or **configuration files** (such as `system.ini` or `httpd.conf`) are read at startup and stored in global variables
- users can change parameters in **preference dialogs**, sometimes with immediate effects, other times requiring a restart.
- values of variables can also be **hard-wired** in source code, so changing them requires recompilation.

## Discussion:
- Implementing variability with parameters is straightforward.
- Variation is evaluated at run time when conditional statements are executed. 
- This approach shares the usual benefits and drawbacks of runtime binding.

| Criteria               | Comment                                               |
| ---------------------- | ----------------------------------------------------- |
| Preplanning Effort     | LOW                                                   |
| Feature Tracability    | Non trivial due to scattering and lack of cohesion.   |
| Separation of Concerns | Crosscutting code. Code scattering and Code Tangling  |
| Information Hiding     | Low. As variability code cross cuts the feature code. |
| Granularity            | FINE                                                  |
| Uniformity             | not applicable to non code artefacts                  |

### Advantages:
- Simple implementation, no extra technologies or tools needed.
### Disadvantages:
- Feature code scattered across the entire program
- Global variables or bloated parameter lists
- Unclear which code belongs to which feature
- No generator is used -> Always all feature delivered
	- Code size, memory consumption, performance
	- Unused features pose a security risk

---
## Presence Conditions and Taints
Taint Analysis done to understand which part of code is dependent on which configuration variables.

Taints = $(w, v, c)$
- $w$ - variable
- $v$ - value of the variable
- $c$ - constraint on the variable

---
## Feature Modularity

### Prospects of Modularity
- Enables modular reasoning
- Reduces complexity (Divide and Conquer)
- Reuse and compose pieces

### Measuring Cohesion and Coupling:
#### Basic Definitions
| | |
|-|-|
|$\text{intdeps}(f)$|Set of dependencies among program elements of feature $f$|
|$\text{extdeps}(f)$| Set of dependencies among program elements of feature $f$ with elements from other features|
|$\text{elements}(f)$| Set of program elements of feature $f$|

$$
\begin{aligned}

\text{IFD} &= \frac{|\text{intdeps}(f)|}{|\text{elements}(f)| \cdot (|\text{elements}(f)|-1) / 2} \\

\\

\text{EFD} &= \frac{|\text{extdeps}(f)|}{|\text{intdeps}(f) \cup \text{extdeps}(f)|}

\end{aligned}
$$
---
## Runtime Parameters are anti-modular

**Scattered Code**:
	Code belonging to a single feature is scattered across multiple modules.

**Tangled Code**:
	Code belonging to different features is intermixed in a single modules

**Crosscutting Feature**:
	feature whose code is scattered and tangled

**Problems of Crosscutting features**:
- Features tend to vanish in the codebase.
- difficult division of work.
- Lower productivity, Difficult evolution.

| | |
|-|-|
|$\text{M}$|Set of modules of a program|
|$\text{locations}(f)$| Set of code locations of feature $f$|
|$\text{locations}(m)$| Set of code locations of module  $m$|

**Feature Scattering Degree**:
$$SD(f) = |\{m \mid \text{locations}(f) \cap \text{locations}(m) \ne \emptyset \}|$$

**Module Tangling Degree**:
$$TD(m) = |\{f \mid \text{locations}(f) \cap \text{locations}(m) \neq \emptyset \}|$$

---
# 2. Design Patterns
==**Design patterns** are descriptions of collaborating objects and classes that are customised to solve a general design problem in particular context.==

We will discuss a few patterns that are well suited for variability implementations.

## Observer Pattern
- The observer pattern describes a common way to implement distributed event handling, in which a subject notifies all registered observers of change to its state. 
- In product-line development, the observer pattern makes it easy to add and remove features, provided that a feature can be implemented as an observer.
- ==Variability is achieved by registering or not registering observers.==
- Code of different features can be separated into distinct observer classes. This way additional features can be added without changing the implementation of a subject.
- ==The observer pattern requires preplanning==. A developer needs to decide upfront where variation will be needed and to prepare the code by providing a registration facility and exposing relevant information through the observer interface.
---
## Template Method Pattern
- ==The **template-method** pattern defines a skeleton of an algorithm in an abstract class, but leaves certain steps of the algorithm to be specified by a subclass.==
- Different subclasses can provide different implementations of these steps by overriding one or multiple methods, and can thus influence program behaviour.
- In product-line development, we can exploit this pattern and implement behaviour of alternative features by means of different subclasses. 
- Especially, if the algorithm differs only in minor details in each feature, we can share the common parts of the algorithm in a common abstract class.
- ==The template-method pattern is best suited for alternative features that is, when only one feature out of a set of features can be selected at a time.==
- ==However, template method is not suitable for combining multiple features, due to limitations of inheritance==
---
## Strategy Pattern
- ==The strategy pattern aims at variability in algorithms. the strategy pattern uses **delegation instead of inheritance**.==
- the strategy pattern consists of three roles
	- context: implements the main algorithm
	- strategy interface: describes the functionality that can be provided by the clients
	- concrete implementations of the strategy
- A strategy is passed to the context in some form.
- ==Using the strategy pattern, a client can select which implementation of the strategy should be used during the execution.==
- Combining multiple features is possible.
- This pattern encourages encapsulation and decoupling of features, and even enables distributed development and separate compilation.
---
## Decorator Pattern
- ==The **decorator pattern** describes a **delegation-based** mechanism to flexibly extend objects with additional behaviour==
- the decorator receive a component as a constructor argument and forwards all calls to that component, except for the intended changes to the behaviour.
- The strength of decorators is that additional behaviour can be added incrementally at run time to existing classes
- In product-line development, the decorator pattern is well-suited to implement optional features and feature groups of which multiple features can be selected.
---
## Summary design patterns.
Similar to runtime-parameters, but with the following distinctions.

| Criteria               | Comment                                                                         |
| ---------------------- | ------------------------------------------------------------------------------- |
| Preplanning Effort     | allow non invasive future extensions at some pre-planning effort                |
| Feature Tracability    | support clear tracing of features                                               |
| Separation of Concerns | Encourage decoupling for features                                               |
| Information Hiding     | Encourage encapsulation for features                                            |
| Granularity            |                                                                                 |
| Uniformity             | Can be encoded in different programming lanuages but not to non code artefacts. |

## Strong points:
- Well established, ease communication between developers.
- Guidelines for disciplined design.
- Separate feature code from base code, possibly with clear interfaces.
- Noninvasive extensions without modifying the base code, given a preplanning effort. 

## Weak points:
- Boilerplate code and architectural overhead.
- Preplanning of extension points necessary.


---
# 3. Frameworks:
- ==A **framework** is an incomplete set of collaborating classes that can be extended and tailored for a specific use case.==
- A framework provides explicit points for extensions, called **hot spots**, at which developers can extend the framework.
- a framework is responsible for the main control flow and asks its extensions for custom behaviour, on demand; a principle called **inversion of control**.
- In feature-oriented product-line development, ideally, we develop one plug-in per feature and configure the application by assembling and activating plug-ins corresponding to the feature selection — a composition process.
---
## White Box Frameworks:
- ==**White-box frameworks** consist of a set of concrete and abstract classes.== 
- To customise their behaviour, developers extend white-box frameworks by overriding and adding methods through **subclassing**.
- the while box comes from the fact that developers need to understand the frameworks internals. 
- All non-private fields and methods can be regarded as hotspots of the framework, providing additional flexibility.
- When using while box frameworks for product line variability, we can only add one subclass at a time to a given class, but not mix and match multiple extensions. Hence, as the template method design pattern, while box frameworks are **best suited for implementing alternative features**.
---
## Black Box Frameworks:
- ==**Black-box** frameworks separate framework code and extensions through interfaces.==
- ==A **plug-in** extends hot spots of a black-box framework with custom behaviour. A plug-in can be separately compiled and deployed. ==
- Whereas white-box frameworks can be understood in terms of the template-method pattern, black-box frameworks follow the strategy and observer patterns
- The framework exposes explicit hot spots, at which plug-ins can register observers and strategies.
- Ideally developers need to understand merely the interfaces, but not internal implementation of the framework. Hence black box.
---
## Discussion:
### Strong Points
- Well suited for implementing variability
- Automated product derivation by loading plug-ins
- Modularity by separating feature, hiding feature internals and enabling feature traceability
### Weak Points:
- High upfront design effort
- Difficult evolution
- Unsuited for fine grained and cross cutting features.
---
# [[The Expression Problem]]
