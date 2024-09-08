
AUTOGRAM is based on the idea that, given a set of inputs and program runs on those inputs, can be used to generate a context free grammar that represents those inputs. This helps to facilitate understanding of inputs structure, serves as a base for test generations and parsing.

The idea is based on three areas.
1. Language Induction: provides approaches to infer the grammar and language of the text
2. Input Origins: Helps to relate the input fragments to the code fragments that process them
3. Data Tainting: Used to identify the origin of data and track their flow during program execution.

The taint information and logs of the program helps us to identify the fragments of inputs that are formed while parsing, eg: a function that processes an input fragment. 

The fragments formed have an inherent hierarchy among them which can be computed by traversing the dynamic call tree. Thus, we form an interval (fragment) tree. This interval tree is formed for each input.

We map each input fragment to the code fragments and check if they qualify as a nonterminal for which production rules can be derived. We derive production rules by looking at each interval tree. The children of each node corresponds to the sequence of terminal and nonterminal that would be the expansion rule for that node.

# Questions:

## Question 1:
- For generating the grammar we want a program that processes the input and parses them into its sub components
- How can we mention that we are able to write parsers using the AUTOGRAM which itself requires a parsers execution log to form the grammar.
- Cyclic dependency?

## Question 2:
- The grammar generated is dependent on the correctness of the processing program.
- If the program whose runs are used to generate grammar will lead to incorrect grammar generation. This grammar wont be helpful to test the correctness of the program.
- If we don't know the grammar how can we judge if the program used for grammar generation?

