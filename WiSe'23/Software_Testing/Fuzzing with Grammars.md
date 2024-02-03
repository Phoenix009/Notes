- The set of all inputs that a program takes from its input sources determines how the program will behave - including its failures.
- when testing, it is therefore very helpful to think of input sources, how to get them under control and how to systematically test them.
- a connection between input structure and functionality leads to a strong _correlation between grammar coverage and code coverage_. 
- In other words: If we can achieve a high grammar coverage, this also leads to a high code coverage.

## Grammars
- The set of valid inputs to a program is called a _language_
- To formally describe languages, the field of _formal languages_ has devised a number of _language specifications_ that describe a language.
- Grammars are among the most popular (and best understood) formalisms to formally specify input languages

## Grammars as Mutation Seeds
- One possibility is the put together the strengths of grammar-based fuzzing and [[Mutation Based Fuzzing]]
- Idea is to use the grammar-generated inputs as seeds for further mutation-based fuzzing.
- We could also use coverage to guide test generation.


## Efficient Grammar Fuzzing
We know how to use grammars for very effective and efficient testing. We now refine the algorithm from string based to a tree based algorithm which is much faster and allows for much more control over the production of fuzz inputs

### Derivation Trees:
We will use a special representation for the string that our grammar produces. The general idea is to use a tree structure that will be subsequently expanded called derivation trees.


## Grammar Coverage

- The aim of test generation os to cover all functionality of a program - hopefully including the failing functionality
- If we fail to produce certain input elements, then the associated code and functionality will not be triggered reducing our chances to find bugs
- Our aim must thus be to cover all possible expansions of the grammar.

### Covering Grammar Elements
- The idea is as follows
	1. we determine children yet uncovered
	2. if all children are covered, we fall back to the original method of random expansion
	3. else we select a child from the uncovered children mark is as covered and expand it

- This is good but not good enough.

### Deep Foresight
![[grammar_coverage.png]]
- When expanding a letter symbol, we have the choice between three possible expansions. 
- However once all the three expansions are covered then we choose the expansions randomly even if there are more expansions to cover when choosing `<percent>`
- A better strategy will pick expansions if there are more uncovered expansions following the initial expansion.


### Coverage in Context
![[context_coverage.png]]
- Here, the `<integer>` is used for integer numbers as well as for floating point numbers
- Our strategy will ensure that all `<integer>` expansions are covered. However, the individual digits would be distributed across all occurrences of `<integer>`.
- If there are different functions that operate on floating-point numbers and integers then we would like these functions to be tested with all digits.
- We would like to ensure that an occurrence of a symbol is systematically covered independent of all other occurrences of that same symbol

- One simple way to achieve this in context is by duplicating symbol as well as their expansions
![[context_coverage_expanded.png]]


## Parsing Inputs


## Probabilistic Grammar Fuzzing
- We can give more power to grammars by assigning probabilities to individual expansions. 
- This allows us to control how each element should be produced and allows to target our tests towards a specific functionality

### Learning probabilities from Samples
- Probabilities need not be set manually, they can also be learned from other sources. We can count how frequently an individual expansion occurs in a given set of inputs.

