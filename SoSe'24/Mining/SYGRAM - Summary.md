==Approach to statically mine context free input grammars from recursive descent parsers based on symbolic execution==

# Approach
Recursive descent parses consists of a set of mutually recursive parse functions. Parse functions operate on tokens, which are requested either by calling a tokenisation function or another parse function.

Each parse function corresponds to one non terminal in the grammar. The idea is to analyse each parse function in isolation to produce function level grammar.

# Methodology

For analysing functions we need to implement 
1. a *harness* to start and guide symbolic execution.
2. a *proxy* that is instead invoked whenever the parse function calls another parse function.
for each tokenisation and parse functions.

We want to collect event traces by executing symbolic execution on the parse functions. The following events are logged:

| Event + Arguments     | Description                        |
| --------------------- | ---------------------------------- |
| accept(tokenValues)   | A token was accepted.              |
| revert(tokenCount)    | Accepted token(s) were lookaheads. |
| call(parseFunction)   | A parse function was called.       |
| enter(loopID,iterCnt) | A parse function entered a loop.   |
| exit(loopID)          | A parse function exited a loop     |

The events logged in one function level execution path corresponds to rule alternative in the grammar.

However, there can be infinitely many execution paths in a function due to recursion and loops leading to path explosion. This needs to be handled to limit the symbolic execution to crucial execution paths.

Now everything is setup to run symbolic execution on the harnessed parse function and generate event traces. 

# Traces to Grammar
## Lexical Analysis:
The result of the symbolic execution on the harnessed tokenisation function are pairs `(token_id, token_instance)` which are then aggregated by the `token_id`. If we observe more than four tokens for a `token_id`, we generalise them.

## Syntactical Analysis:
The events of one function level execution path corresponds to one rule alternative in the grammar. The event log is serialised when the parse function returns without a syntax error. 

This trace is then pruned based on certain heuristics and loop events are generalised. We then compute all possible solutions for the `accept` events by querying the SMT solver.

## Joining the Grammar:
We copy all non terminals in a single grammar and define a `<start>` symbol. At this point the grammar is operational. 

