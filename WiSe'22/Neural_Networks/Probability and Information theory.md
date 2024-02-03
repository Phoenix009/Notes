# Frequentist Probability and Bayesian Probability:

When we say that an outcome has a probability $p$ of occuring, it means that if we repeated the experiment infinitely many times, then the proportion $p$ of the repetitions would result in that outcome.

If a doctor analyses a patient and says that the patient has 40% chance of having the flu, we use probability to represent a degree of belief.

The former kind of probability, related directly to the rates at which the events occur is called the frequentist probability.

The latter, related to qualitative levels of certainty is known as Bayesian Probability.

# Random Variables:

A random variable is a variable that can take on different values randomly.

On its own a random variable is just a description of the states that are possbile; it must be coupled with a probability distribution that specifies how likely each of these states are.

# Probability Distributions:

A probability distribution is the description of how likely is a random variable or a set of random variable is to take on each of its possible states.

## Discrete Variables and Probability Mass Function:

A probability distribution over discrete variables may be described using a probability mass function.

Probability mass function can act on many variables at the same time. Such a probability distribtion over many variables is known as join probability distribution. $P(\texttt{x}=x, \texttt{y}=y)$ denotes the probability that $\texttt{x}=x$ and $\texttt{y}=y$ simultaneously.


For a $PMF$ the following conditions should hold:
1. the domain of $P$ must be set of all possible states of $\texttt{x}$.
   
2. $\forall x \in \texttt{x}, 0 \le P(x) \le 1$ for all states $x$ the probability is in \[0, 1\]
   
3. $\sum_{x\in\texttt{x}}P(x) = 1$. We refer to this property as being normalized.


## Continuous Variables and Probability Density Function:

When working with random variables we describe probability density function $PDF$ rather than a probability mass function $PMF$.

A $PDF$, $p(x)$ does not give the probability of a specific state directly, instead the probability of landing inside an infinitesimal region with the volume $\delta x$ is given by $p(x)\delta x$

# Marginal Probability:

We know the distribution over a set of variables and we want to know the distribution over just a subset of them. This is known as marginal probability distribution.

Suppose we have discrete random variables $\texttt{x}$ and $\texttt{y}$, and we know $P(\texttt{x}, \texttt{y})$. We can find $P(\texttt{x})$ with the sum rule.

$$

P(\texttt{x}=x) =

\sum_{y}{P(x, y)}

$$

For continuous random variable and $PMF$ we can write
$$

p(\texttt{x}=x) =

\int{P(x, y)}.dy

$$

  

# Conditional Probability:

In many cases we want to calculate the probability of some event given that some other event has happened. This is called conditional probability.

These two events happen in one single experiment and not one after another.

We denote the conditional probability that $\texttt{y}=y$ given that $\texttt{x}=x$ is given by $P(\texttt{y}=y \mid \texttt{x}=x)$

$$

P(\texttt{y}=y \mid \texttt{x}=x) = \frac

{P(\texttt{y}=y,\texttt{x}=x)}

{P(\texttt{x}=x)}

$$

A joint probability distribution over many random variables may be decomposed into conditional distributions over only one variable
$$

P(a, b, c) =

P(a \mid b, c)

P(b \mid c)

P(c)

$$

  
# Expectation Variance and CoVariance:

The expectation or expected value of some function $f(x)$ with respect to a probability distribution $P(\texttt{x})$ is the average value that $f$ takes on when $x$ is drawn from $P$.
$$

\mathbb{E}(f(x)) =

\mathbb{E}_{\texttt{x} \sim P}[f(x)] =

\sum_{x}P(x)f(x)

$$
Variance gives a measure of how much the values of a function of a random variable $\texttt{x}$ vary as we sample different $x$ from its distribution.
$$

\text{Var}(f(x)) = \mathbb{E}[(f(x) - \mathbb{E}[f(x)])^2]

$$

  

## Understanding CoVariance:
1. High absolute values of the covariance means that the values change very much and are both far form their repective means at the same time
   
2. Positive then both the variables tend to take on relatively high values simultaneously
   
3. Negative then both variables tend to take on a relatively high values ar times that the other takes relatively low value and vice versa.

  

# CoVariance and Dependence:

The notions of covariance and dependence are related, but in fact are distinct concepts. They are relate because two variables are
1. Independent if $\text{Cov} = 0$
2. Dependendent if $\text{Cov} \ne 0$

However, independence is a stronger requirement, than non-zero covariance. It is possible for two variables to be dependent but have zero covariance.

# Common Probability Distribution:

## 1. Bernoulli Distribution:

The distribution is over a single binary random variable.

Parameterized by a single random variable $\phi \in [0, 1]$, which gives the probability of the random variable being equal to $1$.

## 2. Multinoulli Distribution:

The multinoulli or categorical distribution over a single discrete variable with $k$ different states, where $k$ is finite.

Parameterized by a vector $p \in [0, 1]^{k-1}$, where $p_i$ gives the probability of the $i^{th}$ state.

## 3. Gaussian Distribution:

Also known as the normal distribution
$$
\mathcal{N}(x;\mu, \sigma^2)
$$
Parameterized by two quatities
1. $\mu \in \mathbb{R}$, gives the coordinates of the central peak , is the mean of the distribution. $\mathbb{E}[\texttt{x}] = \mu$

2. $\sigma \in (0, \infty)$, gives the standard deviation of the distribution is given by $\sigma$, and the variance is given by $\sigma^2$

### Central Limit Theorem:
Sum of many independent random variables is approximately normally distributed.

Many complicated systems can be modeled as normally distributed noise, even if the system can be decomposed into parts with more structured behavior.

## 4. Dirac and Empirical Distribution:

We wish to specify that all of the mass in a probability distribution clusters around a single point. This can be done using defining a Dirac Delta function $\delta(x)$

$$

p(x) = \delta(x - \mu)

$$

The dirac delta function is defined such that it is zero every where except at $0$, still integrates to $1$

  

# Bayes Rule:
TODO