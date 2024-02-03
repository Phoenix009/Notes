
Regularization is any modification we make to a learning algorithm that is intended to reduce the generalization error but not its training error.

These constraints and penalties are designed to encode specific kinds of prior knowledge. Other times, these constrains are designed to express a generic preference for a simpler model class in order to promote generalization.

In deep learning most regularization strategies are based on regularizing estimators by trading increased bias with reduced variance. 

an effective regularizer makes a profitable trade, reducing variance significantly while not overly increasing bias.

In practical deep learning scenarios, we find that the best fitting model is a large model that has been regularized appropriately.

# Parameter Norm Penalties:

Many regularization approaches are based on limitting the capacity of models by adding a parameter norm penalty $\Omega(\theta)$ to the objective function $J$. We denote the regularized cost function by $\tilde{J}$

$$
\tilde{J} = 
J(\theta) + \alpha \Omega(\theta)
$$

when our training algorithm minimizes the $\tilde{J}$ it will decrease both the original objective function $J$ and some measure of the size of the parameters.

Different choice for the parameter norm $\Omega$ can result in different solutions being preferred.

## $L^2$ Parameter Regularization:

The weight decay regularization strategy drives the weights closer to the origin by adding a regularization term $\Omega(\theta) = \frac{1}{2}\|w\|_2^2$  to the cost function.

$$
\tilde{J} = J(w) + \frac{\alpha}{2} w^\top w
$$

The corresponding parametric gradient is

$$
\nabla_w \tilde{J} = 
\nabla_wJ + \alpha w
$$

To take a single gradient step to update the weights we do

$$
w = w - \epsilon(\alpha w + \nabla_wJ)\\

w = (1 - \epsilon \alpha)w  - \epsilon \nabla_wJ
$$

We can see that the addition of the weight decay term to the overall cost function leads to multiplicatively shrink the weight by a constant factor before doing the usual weight update.

What is the effect over the entire training?
![[L2Regularization.png]]


Only directions along which the parameters contribute significantly to reducing the objective function are preserved relatively intact.

In directions that do not contribute to reducing the objective function, a small eigen value of Hessian tess us that movement in this direction will not significantly increase the gradient.

Components of the weight vector corresponding to such unimportant directions are decayed awy through the use of regularization

## $L^1$ Parameter Regularization:

$L^1$ regularization on the model parameter $w$ is defined as

$$
\Omega(\theta) = 
\|w\|_1 = 
\sum_i\|w_i\|
$$

The regularized cost function is given by

$$
\tilde{J}(w) = J(w) + \alpha \|w\|_1
$$

The corresponding gradient is given by

$$
\nabla_w \tilde{J} = \alpha \text{sign}(w) + \nabla_wJ
$$

To take a single gradient step to update the weights we do

$$
w = w - \epsilon(\alpha \text{sign}(w) + \nabla_wJ)\\

w = w - \epsilon \alpha\text{sign}(w)  - \epsilon \nabla_wJ
$$

We can see that the regularization contribution to the gradient is no longer scales linearly with each $w_i$ instead it is a constant contribution of $\epsilon \alpha$ with sign equal to $\text{sign}(w_i)$

![[L1Regularization.png]]


In comparison to the $L^2$ regularization, $L^1$ regularization results in a solution that is more sparse. Sparsity in this context refers to the fact that some parameters have an optimal value of zero.

The sparsity property induced by the $L^1$ regularization has been used extensively as a feature selection mechanism. 

# Dataset Augmentation:

Best way to make a machine learning model to generalize better is to train it on more data. However, the amount of data is limited. One way to get around this problem is to create fake data and add it to the training set.

This approach is easiest for training a classifier. The main task of a classifier is to be invariant to a wide variety of transformations. We can generate $(x, y)$ pairs easily just by transforming the $x$ inputs in our training set.

One has to be careful not to apply transformation that would change the correct class. Ex: Flipping 6 in digit recognition will change the correct class from 6 to 9.

# Semi Supervised Learning:

Both unlabeled examples from $P(\text{x})$ and labeled examples from 
$P(\text{x, y})$ are used to estimate $P(\text{y} \mid \text{x})$.

In context of deep learning, semi-supervised learning refers to a representation $h = f(x)$. The goal is to learn a representation so that examples from the same class have similar representations.

Unsupervised learning can provide useful cues for how to group examples in representation space. A linear classigier in the new space may acheive better generalization in many cases.

Instead of having seperate unsupervised and supervised components in the model, one can construct models in which a generative model of either $P(\text{x})$ or $P(\text{x}, \text{y})$ shares parameters with a discriminative model of $P(y \mid x)$.

The generative model then expresses a particular form of prior belief about the solution to the supervised learning problem. BY controlling how much the generative criterion is included in the total criterion, one can find a better trade-off than with a purely generative or a purely discriminative criterion.

# Multi-Task Learning:

In a same way that additional training examples put more pressure on the parameters of the model towards values that generalize well, when part of a model is shared across tasks, that part of the model is more constrained towards good values., ogten yielding better generalization.

Of course, this will happen only if some assumptions about the statistical relationship between the different tasks are valid, meaning that there is something shared across some of the tasks.

# Early Stopping:

When training large models with sufficient representational capacity to overfit the task, we often observe that training error decreases steadily over time, but validation error begins to rise again.

Instead of running our optimization until we reach a minimum of validation error, we run it until the error on the validation set has not improved for some amount of iterations.

Every time the error on the validation set improves we store a copy of the parameters. When the training algorithm terminates, we return these parameters, rather than the latest parameters.

In case of early stopping we are controlling the effective capacity of the model by determining how many steps it can take to fit the training set.

## Early stopping as a regularizer:

Early stopping has the effect of retricting the optimization procedure to relatively small volume of parameter space in the neighborhood of the initial parameter values $\theta_0$.

Assume $\tau$ as the number of iterations with learning rate $\epsilon$. We can view the product $\epsilon \tau$ as a measure of effective capacity.

# Parameter Typing and Parameter Sharing:

Sometimes we might not know precisely what values the parameters should take but we know, from knowledge of the domain and model architecture, that there should be some dependencies between the model parameters.

A common type of dependency that we often want to express is that certain parameters should be close to one another. We can use a parameter norm penalty of the form $$\Omega(w^{(A)},w^{(B)}) = \|w^{(A)} - w^{(B)}\|_2^2$$


# Bagging and Other Ensemble Methods:

Bagging (Bootstrap Averaging) is the idea to train several different models seperately, then have all of the models cote on the output for test examples. This is an example of general strategy in machine learning called model averaging. Techniques employing this strategy are known as ensemble methods.

Specifically, Bagging involves constructing $k$ different datasets. Each dataset has the same number of examples as the original dataset, but each dataset is constructed by sampling with replacement from the original dataset.

# Dropout:

Dropout trains the ensemble consisting of all subnetworks that can be formed by removing non-output units from an underlying base network.