# Dimensions of Variability Implementation:

## Variability:

==**Variability** is the ability to derive different products from a common set of artefacts.==

The desire for variable software is driven by the typically broad and diverse spectrum of requirements of the stakeholders of a product line, manifested in features they want in a product.

---

## Binding Time:
When we derive a product, we decide which features will be included in the product. We also say that we bind a decision.

Different implementation techniques allow binding decisions at different times. In other words, they allow different binding times.

### Compile Time binding:
- With an implementation technique that supports compile-time binding, developers make decisions of which features to include at or before compile time.
- Code of deselected features is then not even compiled into the product
- Ex:
	- [[Compile-time Variability#3. Preprocessors|Preprocessors]]
	- [[Advanced Language Mechanisms#Feature Oriented Programming|Feature Oriented Programming]]

### Load Time Binding:
- Developers can defer feature selection until the program is actually started. 
- That is, during compilation all variations are still available; they are decided after deployment, for example, through command-line parameters or configuration files.

### Run Time Binding:
- Decisions are deferred to run time and may even change during program execution
- Ex:
	- [[Runtime Variability#1. Runtime Parameters|Runtime Parameters]]

---

## Technology: Language Based Vs Tool Based:
### Language Based Approach:
A language based approach uses the mechanisms provided by a host programming language to implement features and to derive products

### Tool Based Approach:
Uses one or more external tools to implement or represent features in code and to control the product derivation process.

---

## Representation: Annotation Vs Composition
A major goal of feature oriented product line engineering is to derive a product automatically from variable code, based on users feature selection.

We concentrate on two approaches that are widely used in practice: annotation based and composition based approaches, which differ in the way they represent variability in the code base and the way they generate products.

### Annotation Based:
- This approach annotates a common code base, such that code that belongs to a certain feature is marked accordingly.
- During the product derivation all code that belongs to deselected features or invalid feature combinations are removed (compile time) or ignored (run time) to form the final product.

### Composition Based:
- This approach implements features in the form of composable units, ideally one unit per feature. 
- During product derivation, all units of all selected features and valid feature combinations are composed to form the final product.


---


# Quality Criteria:
To assess tradeoffs and compare implementation strategies, we introduce and discuss six quality criterias that product line implementation techniques should ideally meet:

| Criteria               | Comment |
| ---------------------- | ------- |
| Preplanning Effort     | LOW     |
| Feature Tracability    | YES     |
| Separation of Concerns | YES     |
| Information Hiding     | YES     |
| Granularity            | YES     |
| Uniformity             | YES     |

## Preplanning Effort:
- The key goal of preplanning is to ease the anticipation of changes and sources of variability and reuse.
- Instead of developing individual software systems, product developers analyse an entire domain and anticipate potential requirements in terms of features.
- ==Ideally a product line implementation technique minimises the necessary preplanning effort and even allows implementing features that were not planned upfront with low effort.==
## Feature Traceability:
- Is the ability to trace a feature from the problem space (ex. Feature Model) to the solution space (ex. Actual Implementation).

## Separation of Concerns:
- A common approach to attain traceability (especially, in composition-based approaches) is to separate features both in design and code, such that the relationship between features and corresponding design and implementation artefacts are explicit.
- ==**Crosscutting** is a structural relationship between the representations of two concerns. It is an alternative to hierarchical and block structure.==
- Classic programming languages suffer from a limitation that is referred to as the **tyranny of the dominant decomposition**, which is the cause of crosscutting (Tarr et al. 1999): ==Using hierarchical structures, a program can be decomposed in only one way (along one dimension) at a time, called the dominant decomposition. All concerns that do not align with the dominant decomposition end up in scattered and tangled code.==
- ==**Code Scattering** refers to a concern representation that is scattered across representations of multiple other concerns==
- ==**Code Tangling** refers to the intermingled representation of several concerns within a module.==

## Information Hiding:
- Ideally, with information hiding, ==each module can be understood in isolation by looking just at the module’s secret and the interfaces of imported modules but not the secrets of other modules known as **modularity reasoning**.==
- Information hiding is the separation of a module into internal and external part
- The internal part remains hidden from other modules, whereas the external part, the modules interface, specifies the contract of how the module interacts with the rest of the system.
- ==Information hiding enables modular reasoning.==

## Granularity:
- ==A **level of granularity** refers here to the hierarchical structure of an implementation artefact, typically, defined by a containment relation among the artefacts structural elements, given by its syntax.== 
- Changes at the top of the hierarchical structure of an implementation artefact are coarse-grained. Changes at lower levels are fine-grained.
- The rule of thumb is that annotation-based approaches support more fine-grained changes and interactions than composition-based approaches
- the code of different features is intermixed anyway in a common code base, and annotations can be applied at nearly every place in a code base. 
- Composition-base approaches rely on interfaces that are defined typically at the level of classes and methods.

## Uniformity:
- Features are implemented by a diverse selection of software artefacts and any kind of software artefact can be subjected to subsequent changes and extensions.
- Conceptually, all artefacts should be encoded and synthesised in a similar manner.
- Ideally, an implementation technique should be applicable, in principle and in practice to a wide variety of different code and non code artefacts.

---
