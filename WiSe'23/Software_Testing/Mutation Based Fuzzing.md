Randomly generated inputs are syntactically invalid and thus are quickly rejected by the target program. We need to increase chances of generating valid inputs.

Mutation based fuzzing is one such way that introduces small changes to existing inputs that may still be valid but exercise new behaviour.

Many programs expect their inputs to come in very specific format before they would actually process them. The odds of producing a valid input may be very less if we use just random fuzzing.

## Mutating Inputs
The alternative to generating random inputs from scratch is to start with a given valid input and then subsequently mutate it.

a mutation in this context is simple string manipulation - insertion, deletion, flipping bit. This is called mutational fuzzing.

The idea is now that if we have some valid inputs to begin with, we may create more input candidates by applying one of the above mutations.

The higher variety in inputs, though, increases the risk of having an invalid input. **The key to success lies in the idea of guiding these mutations – that is, keeping those that are especially valuable.**

## Guiding by Coverage
How can we use coverage to guide test generation?
One successful idea is implemented in the popular fuzzer named AFL.

AFL evolves test cases that have been successful, for AFL success means finding a new path through the program execution. if an input finds another path it will be retained.





