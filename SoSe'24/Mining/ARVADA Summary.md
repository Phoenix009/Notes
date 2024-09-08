
# Introduction
ARVADA presents an algorithms that learns a context free grammar given a set of positive examples $\mathcal{S}$ and a boolean oracle $\mathcal{O}$. The goal is to learn a grammar $\mathcal{G}$ that maximally generalises the example strings in a manner consistent with the oracle $\mathcal{O}$.


# Technique

## Step1: Start
The algorithm that begins by constructing naive flat parse trees from the examples. Here the root non-terminal $t_0$ expands to the sequence of characters in each example $s \in S$.

Let $\mathcal{T}$ designate the set of trees ARVADA maintains at any point in its algorithm

## Step2: Bubbling and Merging
### 1 and 2-Bubbling:
The fundamental operation ARVADA performs is to bubble a sequence $s_1$ in $\mathcal{T}$:
- Create a new non-terminal $t_{s_1}$ with $s_1$ as its expansion
- Replace all occurrences of $s_1$ in $t \in \mathcal{T}$ with $t_{s_1}$

2-Bubbling happens when we select two sequences $s_1$ and $s_2$ to be bubbled simultaneously. These sequences cannot be overlapping sequences of one another. they can either be distinct of a subset or a super set.

#### Which sequences to bubble?
For each tree $t \in \mathcal{T}$, we collect all proper contiguous subsequences of children in $t$. That is, if the tree contains a node $t_i$ with children $C = c_1 , c_2 , \cdots , c_n$ , the potential bubbles include all subsequences of $C$ such that $0 < |C| <n$.

All non conflicting pairs are selected for 2-bubbling.


### Merging:
The bubbling of non-terminals is accepted iff it enables valid generalisation i.e. it expands the language accepted by the induced grammar while maintaining the oracle validity.

Two non-terminals $t_a$ and $t_b$ can be merged iff 
- the occurrences of $t_a$ and $t_b$ are swapped in $t \in \mathcal{T}$. 
- candidate strings from these mutated trees, are all accepted by the oracle.

### Bubbling Order:
The order in which bubbles are applied and checked has a large impact on ARVADAs performance. We use certain heuristics to order the bubbles that increase the probability of ARVADAs success in learning the grammar.

## Step3: Termination:
When no more bubbling and merging can be done ARVADA simply outputs the induced grammar.


# Evaluation:

Metrics:
- Precision: Using the grammar inferred by the approaches we generate 1k random programs and test their acceptance by the parser.
  
- Recall: We create a parser from the inferred grammar and test how many of the sample 1k programs the parser accepts

- F1 Score: is the harmonic mean of the precision and recall

- Runtime:

- Averages: 


# Research Questions:
## 0. How does non-determinism affect ARVADA: 
Unreliable results

## 1. Grammar Quality: At similar runtime which approach inferred better grammar



## 2. Runtime
## 3. Readability
## 4. Ablation Study


- R1 - The new input set $S$ may not cover all golden grammar rules
- R5 - larger input programs


- TreeVada on average produces better grammars and is faster than Arvada.
- 9.3% higher recall
- 22.1% higher precision
- 2.4% speed-up

The large difference in runtime between Arvada and TreeVada is due to the time spent on bubble ranking.

Since the F1 score of TreeVada is higher than Arvada, bubble ranking is counter productive.

Despite covering more of the golden grammars and having higher F1 scores, TreeVadas grammars are smaller for 8/10 languages.


