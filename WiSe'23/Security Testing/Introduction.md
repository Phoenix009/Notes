**Runtime Verification:**
- Instead of writing and running tests for a target function we can simply integrate the tests inside the function implementation itself
- Thus, each and every invocation of the test function will be automatically checked for correctness

- One has to be able to formulate such run-time checks but, formulating desired properties in an abstract fashion can be very complex.
- Run time checks should be affordable to do at every invocation of the function.

**What kind of checks are to be done apart from the logic?**
1. Input Validity
2. Input corner cases

> While extensive testing may give us a high confidence into the correctness of a program, it does not provide a guarantee that all future executions will be correct.




- `Fuzzer` is a base class for fuzzers, with `RandomFuzzer` as a simple instantiation. 
- a `Fuzzer` can be paired with a `Runner`, whichi takes the fuzzed strings as input. Its result is a class-specific status and an outcome (PASS, FAIL, UNRESOLVED)

What kind of bugs to expect?
- Buffer overflows: We know
  
- Missing Error checks: 
	Some functions don't raise an exception instead return a special value ex `EOF`. These checks are sometimes missed.
  
- Rogue Numbers:
	Corner cases basically.

 