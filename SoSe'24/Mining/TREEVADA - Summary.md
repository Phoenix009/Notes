# Introduction:
ARVADA's generalisation steps violate common language concept nesting rules. TREEVADA thus, proposes to pre-structure input programs along these nesting rules, apply learnt rules recursively and make black box grammar inference deterministic.

# ARVADA key technical challenges:

## 10 Non-deterministic $O(n^4)$ runs runtime:
- ARVADA uses non-determinism to explore various sequences of grammar generalisation steps. such a generalisation sequence ca get ARCADA stuck in the sense of cutting off subsequent generalisation options reducing the inferred grammars quality.

### Source of non-determinism:
#### Shuffling initial candidate node pair merges:
1. Tokenise each input program along character classes leaving remaining characters as individual tokens
2. attempt `MergeAllValid` to generalise the grammar rules
	- create all pairs of existing non terminals. 
	- order the pairs arbitrarily
	- try to merge each pair

#### Ranking and shuffling $O(n^4)$ candidate merges:
For each grammar generalisation step ARVADA first collects and ranks all possible parse tree sibling node sequences up to a configurable length together with their pairs.

For $n$ tokens there are essentially $O(n^2)$ 1-bubbles which makes the ranking overall $O(n^4)$

## Not generalising recursively:
ARVADA does not reapply a rule generalisation it learned and thus on some runs needs additional expensive steps or gets stuck.

## Breaking bracket implied nesting structure:
On some runs it prioritises a bubble that conflicts with a parse trees bracket implied nesting structure and thus gets stuck.


# Overview and Design:

## Assumptions on strings and brackets
TreeVada’s current heuristics build on two “soft” assumptions:
1. Many languages use `'` `"` quotes to wrap strings.
2. Many languages use `( )` `[ ]` `{ }` brackets for nesting.

If a language also uses these characters for other purposes, TreeVada’s F1 score may suffer.

## Pre tokenising input programs
As many languages share basic tokenisation rules, TREEVADA and ARVADA first tokenise their input programs. Tokenising both yields higher quality grammars and is more efficient than on each run rediscovering common lexing rules via expensive grammar inference.

We use simple heuristics that solve several scenarios that are common in many languages. 

## Pre-Structuring parse trees along brackets:

the deeper structured the parse trees become via generalisation steps the cheaper the subsequent generalisation step is. This is due to a more structured tree permits shorted sibling toke sequences.

Our goal is to thus quickly convert parse trees from flat to richly structured by essentially enforcing common nesting rules.

## Removing specialised bubbling heuristics
- **1 Bracket Bubbles**:
  - Avoid generating 1-bracket bubbles to ensure TREEVADA doesn't consider bracket-unbalanced bubbles.

- **Partial 1-char Node Merges**:
  - Skip merging bubbles with subsets of interior nodes representing terminal characters, as tokenising input programs makes these merges rarely successful.

## Deterministic Grammar Inference:
TreeVada orders parse trees' unique node types by their shortest distance from any program root node for two related operations:
1. Merging all node-pairs upfront and after exhausting bubbling.
2. Ordering merge-target nodes for a given bubble.

### Depth and Length aware bubbling:
TREEVADA builds on two observations:
1. a longer bubble has a higher chance of being rejected because it tries to group together more nodes. Additionally, when a longer bubble is accepted, it has a higher likelihood of causing grammar inference to get stuck.
   
2. a more deeply nested bubble is more promising than a bubble closer to the root.

Thus, the resulting ranking scheme is:

Resulting Ranking Scheme:   
1. Context Similarity
2. Bubble Depth (minimum root-distance)
3. Bubble Occurrence Counts
4. Bubble Length`

## Applying Learned Rules Recursively
After merging a bubble, Arvada does not recursively reapply the just learned grammar rule. TreeVada instead directly tries to recursively reapply the just learned rule.

