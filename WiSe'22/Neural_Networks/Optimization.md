
# Challenges in Neural Network Optimizations:

## 1. Ill conditioning of the Hessian $H$: 
Ill conditioning can manifest by causing the $\text{SGD}$ to get stuck in the sense that even amall steps increase the cost function.

The result is that learning becomes very slow despite the presence of a strong gradient because the learning rate must be shrunk to compensate for even stronger curvature.

Though ill conditioning is present in other settings besides neural network training, some of the techniques used to combat it in other contexts are less appplicable to neural networks. Newtons method is an excellent tool for minimizing convex functions with poorly conditioned Hessian matrices.

## 2. Local Minima:

With non-convex functions it is possible to have many local minima. However, this is not necessarily a major problem. All of these local minima are equivalent to each other in cost function value.

Local minima can be problematic if they have high cost in comparison to the global minimum. If the local minima with high cost are common, this could pose a serious problem for gradient based optimizations.

## 3. Plateaus, Saddle Points and other flat regions:

At a saddle point, the Hessian matrix has both positive and negative eigenvalues. Points lying along eigenvectors associated with positive eigenvalues have greater cost than the saddle point, while points lying along negative eigenvalues have lower value

An amazing property of many classes of function is that the eigenvalues of the Hessian become more likely to be positive as we reach regions of lower cost. 

For a function $f: \mathbb{R}^n \rightarrow \mathbb{R}$ of this type, the expected ratio of the number of saddle points to local minima grows exponentially with $n$.

What are the implications of the proliferation of saddle points for training algorithm?

For first order optimization algorithm that use only graidnet information, the situation is unclear. The gradient can often become very small near a saddle point. However, gradient descent empirically seems to be able to escale saddle points.

For Newtons method, it is clear that saddle points constitute a problem. It is designed to solve for a point where the gradient is zero.  Without appropriate modification, it can jump to a saddle point. 

The proliferation of saddle points in high dimensional spaces presumably explains why second-order methods have not succeeded in replacing gradient descent for neural network training.


## 4. Cliffs and Exploding Gradients:

On the face of an extremely steep cliff structure, the gradient update stap can move the parameters extremely far, usually jumping off of the cliff structure,

The cliff can be dangerous whether we approach it from above or bottom but, fortunately its most serious consequences can be avoided using the gradient clipping.

## 5. Long-Term dependencies:

This difficulty arises when the computational graph becomes extremenly deep. FFN with many layers have such deep computational graphs.

suppose a computational graph contains a path that consists of repeatedly multiplying by a matrix $W$. After $t$ steps this is equivalent to multiplying with $W^t$.

$$
W^t = (V\text{diag}(\lambda)V^{-1})^t = V \text{diag}(\lambda)^tV^{-1}
$$

Any eigenvalue $\lambda_i$ that are not near absolute value of 1 will either explode if  $>1$ or vanish if  $<1$

The vanishing or exploding gradient problem refers to the thact that gradient through such a graph are also scaled according to 
$\text{diag}(\lambda)^t$.

Vanishing gradients make it difficult to know which direction the parameters should move to improve the cost function, while exploding gradient can make the learning unstable.


# Basic Algorithms:

## 1. Stochastic Gradient Descent:

A crucial parameter for the SGD algorithm is the learning rate. In practice it is necessary to gradually decrease the learning rate over time. The learning rate at iteration $k$  is $\epsilon_k$.

This is because the $\text{SGD}$ gradient estimator introduces a source of noise that does not vanish even when we arrive at a minimum.

In practice, it is common to decay the learning rate linearly until iteraion $\tau$:

$$
\epsilon_{k} = 
(1-\frac{k}{\tau})\epsilon_0 + 
\frac{k}{\tau} \epsilon_{\tau}
$$

$\epsilon_{0}, \epsilon_{\tau}$ and $\tau$ are to be chosen wisely.

1. For $\epsilon_0$: best to choose by monitoring the learning curves that plot the objective function as a function of time.
2. For $\epsilon_{\tau}$: should be set to roughly $1\%$ of the value of $\epsilon_0$
3. For $\tau$: should be set to take few hundred passes through the dataset.

The most important property of $\text{SGD}$ is that computation time per update does not grow with the number of training examples.

To study the convergence rate of an optimization it is common to measure the $J(\theta) - \min_{\theta}J(\theta)$, which is the amount by which the current cost function exceeds the minimum possible cost.

When $\text{SGD}$ is applied to a convex problem, the excess error is $O(\frac{1}{\sqrt{k}})$


## 2. Momentum:

The learning with. $\text{SGD}$ can be slow. The method of momentum is designed to accelerate learning. 

The algorithm introduces a variable $v$ that plays the role of velocity. the velocity is set to an exponentially decaying average of the negative gradient.

Previously, size of the step was norm of the gradient multiplied by the learning rate. Now, the step depeends on how large and how alighed a sequence of gradients are. the step size is largest when many successive gradienst point in exactly the same direction.


# Algorithms with Adaptive Learning rates:

If we believe that the directions of sensitivity are somewhat axis-aligned, it can make sense to use a separate learning rate for each parameter, and automatically adapt these learning rates throughout the course of learning.

If the partial derivative of the loss, with respect to a given parameter, remains the same size then the learning rate should be increase otherwise decreased.

## 1. AdaGrad:

The learning rate of the parameters is directly proportional to the partial derivative of the loss with that parameter. The net effect is greater progress in the more gently sloped directions of parameter space.

$$
g = \nabla_{\theta}J(\theta)
\\
r = r + g \odot g
\\
\theta = \theta - \frac{\epsilon}{\delta + \sqrt{r}} \odot g
$$

Performs well for some but not all deep learning models

## 2. RMS Prop:

AdaGrad shrinks learning rate by using the entire gradient history, the learning rate may become too small.

$$
g = \nabla_{\theta}J(\theta)
\\
r = \rho r + (1 - \rho)g \odot g
\\
\theta = \theta - \frac{\epsilon}{\delta + \sqrt{r}} \odot g
$$

RMS prop uses an exponentially weighted moving average instead of a sum of squares. This allows it to discard extreme past from history so that it converges faster.

RMS prop is a popular choice for optimizing neural networks due to its ability to converge quickly and its relative simplicity compared to other adaptive learning rate methods.


## 3. Adam:

Adam derives from the phrase “ADAptive Moments”. 

$$
s = \rho_1 s + (1- \rho_1)g
\\
r = \rho_2 r + (1- \rho_2)g \odot g
$$

$$
\hat{s} = \frac{s}{1 - \rho_1^t}
\\
\hat{r} = \frac{r}{1 - \rho_2^t}
$$

$$
\theta = \theta - \frac{\epsilon \hat{s}}{\delta + \sqrt{\hat{r}}}
$$


We focus on one particular case of optimization: finding the parameters $\theta$ of a neural network that significantly reduce a cost function $J(\theta)$.

# How learning differs from Pure Optimization:

Optimization algorithms used for training of deep models differ form traditional optimization algorithms in several ways.

We care about some performance measyre $P$, that is degined with respect to the test set. We reduce a different cost function $J(\theta)$ in the hope that doing so will improve $P$.

This is in contrast to pure optimization, where minimizing $J$ is a goal in itself.

## Empirical Risk Minimization:

The risk is the expected generalization error that is taken over the true underlying distribution $p_{\text{data}}$

If we knew the true distribution $p_{\text{data}}(x, y)$, risk minimization would be an optimization task solvable by an optimization algorithm. However, we only have samples from that distribution which makes it a machine learning problem.

Thus, we try to minimize the empirical distribution $\hat{p}_{\text{data}}$ defined by the training set. We now try to minimize the empirical risk hoping that it minimizes the actual risk.

## Surrogate Loss Function:

Empirical risk minimization is prone to overfitting and the cost function used do not have a defined gradient at most points.

Thus, we optimize a surrogate loss function, which acts as a procy but has advantages. Ex. Negative log likelihood is used as a surrogate for the 0-1 Loss.

A very important difference between general optimization and optimization for learning is that training algorithm do not usually halt at a local minimum.

The early stopping criterion is based on the tru underlying loss function, such as 0-1 loss on validation set, and is designed to cause the algorithm to halt whenever overfitting begins.

## Batch and Minibatch Algorithms:

Optimization algorithms that use the entire training set are called batch or deterministic gradient methods, because they prrocess all of the training examples simultaneously in a large batch.

Optimization algorithms that use only one single example at a time are called stochastic or sometimes online methods.

Most algorithms used for deep learning fall somewhere in between. These methods were traditionally called minibatch or minibatch stochastic methods.