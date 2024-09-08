REINAM, is an reinforcement learning approach for synthesising probabilistic context free grammars without any seed inputs. It includes a novel formulation of the search problem as a reinforcement learning problem.

# Introduction:
The existing language inference approaches face the following three main challenges:
1. Un-analysable Code.
2. Low variety and quality of seed inputs.
3. Lack of seed inputs.

Current state of the art approaches are based on active learning which are rigid strategies based on "no over generalisation allowed". This approach ensures that candidate grammars in each generalisation step are precise.

However, even if a generalisation operation often results in an inaccurate grammar, the composition of multiple such generalisations may complement each other in a way that forms an accurate composite generalisation.

REINAM thus improves over the existing approaches retaining the ability to accept the inaccurate generalisation steps while still synthesising a final grammar that achieves high accuracy.

# Phase 1:
REINAM generates a high-variety, high-quality seed inputs using automatic test generation execution engine Pex. 

Next, it runs a grammar synthesiser on these seed inputs to get an initial CFG.

# Phase 2:
## CFG to PCFG:
Convert the initial CFG to a PCFG by assigning uniform probability distribution to each production of a non terminal. For $k$ possible productions for a non terminal, each production rule gets assigned $\frac{1}{k}$ probability

## Reinforcement Learning

We formulate the grammar inference problem as reinforcement learning problem as follows:

- Agent: The PCFG.  
- Environment: The target program.
- State: A partial derivation of the PCFG (i.e., a sequence $\alpha$ consisting of both terminal and non-terminal symbols).
- Action: The choice of the production rule to apply to one non-terminal symbol in $\alpha$ in current state. 
- Reward: Whether the constructed input is accepted by the target program i.e., $1$ if it is accepted and $0$ if it is rejected.

We have two technical considerations while using reinforcement learning to our use case:
1. Existing RL algorithms are designed to maximise a real valued parameters. 
2. We need to modify the structure of the PCFG itself. (to add new production rules) 

To do so we interleave a traditional RL algorithm with the application of generalisation operations on the PCFG.

We do the following steps iteratively:
1. apply a generalisation operation
2. run a traditional RL algorithm
3. remove any production rule with probability lower than a fixed threshold.

## Generalisation operations:
The following generalisation operations are used which are similar to those used in Glade.

1. Character generalisation
2. Repetition
3. Alternation
4. Merging

## Constructing inputs using a PCFG:
For each non terminal the productions used for expanding them are chosen based on the assigned probabilities to those productions.

We keep track of the production rules used to later update their probabilities at the probability adjustment step.

## Probability Adjustment:
In our PCFG mode each production probability quantifies the correctness of the production.

For each production $r_i$ used in the input generation we calculate the reward as follows:
$$\text{reward}(r_i) = \frac{\text{\# accepted inputs that use } r_i}{ \text{\# inputs that use } r_i}$$

Finally, the policy gradient update gives the following adjusted probability for the production.
$$\theta^{\prime}(r_i^A) = \theta(r_i^A) + \eta \ \cdot \ \frac{\text{reward}(r_i^A)}{\theta(r_i^A)}$$
where:
- $\theta(r_i^A)$ is the old probability of the production
- $\theta^{\prime}(r_i^A)$ is the new probability of the production
- $\eta$ is the learning rate

We iteratively apply the generalisation steps and update the probabilities until no probability change is more than $$\frac{1}{10\ \cdot\ \text{\# of productions of A}}$$
## Rule Removal:
We remove production rules with probability lower than the convergence threshold.


