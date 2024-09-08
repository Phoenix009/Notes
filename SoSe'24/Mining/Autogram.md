- Use dynamic tainting to trace the data flow of each input character, and aggregate those input fragments that would be handled by the same function into lexical and syntactical entities

- Resulting grammars can be used for
	- reverse engineer of input formats
	- serve as input test generators

# Introduction
- given a set of program runs with inputs, automatically produce a context free grammar that represents the language of the inputs seen.
- The idea is to dynamically observe how input is processed in a program.

# Background
## Language Induction:
## Input Origins:
## Data Tainting:


# Tainting Input Characters:
# Synthesising Grammars:

- We can obtain a set of intervals of inputs from the taint information and the dynamic call tree
- Each interval of the input is
	- processed by a function
	- stored by a variable or a parameter
	- is used a derive a output of a function

- 




# Contributions:
- first to automatically derive a context free input structure from given inputs and a processing program
- 





