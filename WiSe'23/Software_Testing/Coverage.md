## Black Box Testing:
- Black box testing ==derives tests from the specifications==.
- It tries to finds errors in the specified behaviour.

## White Box Testing:
- White box testing ==derives tests from the implementation==, notably the internal structure.
- Is closely tied to the concept of covering structural features of the code.

- White box testing introduces a number of coverage criteria that have to be fulfilled before the test can be said to be sufficient
1. **Statement Coverage**: each statement in the code must be executed by at least one test input
2. **Branch Coverage**: each branch in the code must be taken by at least one test input


We will make use of coverage to measure the effectiveness of different test generation techniques and to guide test generation towards code coverage

## Finding Errors with Fuzzing
Given sufficient fuzzing inputs we can cover each and ever line within a target function. This does not mean that they would be error-free. 

To catch such errors we would have to set up a results checker commonly called an *oracle*. 