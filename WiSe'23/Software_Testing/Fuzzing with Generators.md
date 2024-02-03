- We will see how to extend grammars with functions that get executed during grammar expansion and that can generate, check or change elements produced
- Adding functions to grammar allows for very versatile test generation

## Attaching functions to Expansions
The functions attached to the expansions can be executed either
1. before expansion: replacing the element to be expanded by a computed value
2. after expansion: checking generated elements and possibly also replacing them
