
# From Requirements to Configurations
- Functional requirements are mapped to a set of features that are implemented.
- Then in feature selection some of the features are selected to generate a variant.
- however in most cases there is a partial selection. 
- In order to make this partial selection complete, the non functional properties are considered.


# Non Functional Properties:
Any property of a product that is not related with functionality represents a non-functional property

## Practical Relevance:
The discussion of non-functional properties helps us to answer some of the following questions.

- What is the binary footprint of a variant for a given feature selection?
- What is the best feature selection to minimise memory consumption?
- Which are the performance critical features?

## Categorisation:
### 1. Quantitative
- Measurable properties
- Response time, throughput etc
- Energy and Memory Consumption

### 2. Qualitative
- Extensibility
- Error Freeness
- Robustness
- Security


## Overview:
We have our configuration space which represent every possible configuration/feature-selection.
$$|C| = \mathcal{O}(2^{|F|})$$
### 1. Measurement:
- The goal is to measure non-functional properties via sampling

### 2. Learning Model:
- Learn a model accurately describing performance of all configurations

### 3. Optimisation:
- Finding optimal configuration in a single or multi-objective scenario

### 4. Comprehension:
- Explainable machine learning with while box models
- Helps to understand the interaction between various features. Helps in software development 


# Predictive Modelling
- We want to train a model that helps us to measure non functional properties. 
- We will achieve this by measuring these properties on the sample configurations. 
- These measurements will be used to train a model and then predict these properties on unseen configurations.

## Linear Regression
- The features are used as independent variables or regressors.
- the non functional property is the dependent variable.
- We train a linear regression model that can then predict the non functional property.

$$\begin{equation}
y = \mathbf{x}^\top \mathbf{\beta} + \epsilon
\end{equation}$$

- $y$ is a non functional property
- $\mathbf{x}$ represents one particular configuration (boolean vector representing selected and non selected features)
- $\beta$ represents the weights for each feature

Most of the non functional properties are nonlinear and cannot be modelled accurately by a linear model.
Influences of individual features cannot accurately predict the non functional properties.

So $\mathbf{x}$ is not just a simple boolean vector representing selection of features but represents features and interactions.

However, we cannot include all possible combinations of interactions. So we incrementally build models by iterating and adding more and more variables and increasing complexity.


## CART
- Write a function to calculate the sample mean
$$l_L = \frac{1}{|X_L|}\sum_{(x, y) \in X_L}y$$
$$l_R = \frac{1}{|X_R|}\sum_{(x, y) \in X_R}y$$

- write a function to calculate square error loss
$$\sum_{(x, y) \in X_L}(y - l_L)^2$$
$$\sum_{(x, y) \in X_R}(y - l_R)^2$$
- calculate the sum squared error loss
$$\sum_{(x, y) \in X_L}(y - l_L)^2 + \sum_{(x, y) \in X_R}(y - l_R)^2$$

- the feature that minimises the above value is chosen as the splitting criterion. the remaining inputs are recursively splitted until the stopping criteria is reached.
