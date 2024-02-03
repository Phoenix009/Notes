
# Introduction to Linear Algebra:

## Scalars: 
A scalar is just a single number. A scalars type is defined of being either a real number or an integer

## Vectors: 
A vector is an array of numbers arranged in an order. We need to specify what kind of numbers are stored in the vector. 

We can think of a vector as identifying a point in space, with each element giving the coordinate along a different axis

## Matrices:

A matrix is a 2-D array of numbers, so each element is identified using two indices.

### What are linear transformations?

A transformation is linear if all lines must remain lines without getting curved and origin must remain fixed in its place.

Think of rotation transformation as a linear transformation.

Now given a single point in the reference space how can we get the coordinates of that point in the transformed space?

We can do this by creating a matrix that can represent the trasformation. Then by doing the matrix multiplication with the vector of the point, we can get the coordinates of the point in the transformed space.

Thus linear transformation can be understood as a function that takes in a vector and produces a vector.

## Tensors:

In some cases we will need array of more than two axes. In the general case, an array of numbers arranged on a regular grid with a variable number of axes is known as a tensor.

# Operations on Matrices:
## Transpose:

$$
(A^T)_{i, j} = A_{j, i}
$$

One important operation of matrices is transpose. The transpose of a matrix is its mirror image across a diagonal line called the main diagonal, running down and to the right, strating from the top left corner

## Adding Matrices:

$$
A = B + C  
$$

$$
A_{i, j} = B_{i, j} + C_{i, j}
$$

We can add matrices together as long as they have the same shape just by adding their corresponding elements

## Scalar Multiplication and Addition:

$$
D = a \cdot B + c
$$

$$
D_{i, j} = a.B_{i, j} + c
$$

We can also add a scalar to a matric or multiply a matrix by a scalar just by performing that operation on each element of a matrix.

## Multiplying Matrices and Vectors:

The matrix product of the matrices **A** and **B** is a third matrix **C**. 

For the product to be valid the number of columns of **A** should be equal to the number of rows of **B**

$$
C = AB
$$

$$
C_{i, j} = \sum_{k}A_{i, k}B_{k, j}
$$

The dot product between two vectors $x$ and $y$ of same dimensionality is the matrix product

$$
x^Ty
$$

We can think of matrix product $C = AB$ as computing the $C_{ij}$  as the dot product between row i of the $A$ and column j of $B$

### Matrix Product is distributive:

$$
A(B + C) \Rightarrow A \cdot B + A \cdot C 
$$

### Matrix Product is associative

$$
(AB)C = A(BC)
$$

### What is the significance of Matrix Multiplication?

Matrix Multiplication can be thought of as a composition of  linear transformation. When applying transformation one after another we can simply compute their multiplication and get a single linear transformer.

This makes the associativity of the matrix multiplication intuitive.

## Hadamard Product:

$$
C = A \odot B
$$

$$
C_{i, j} = A_{i, j} \cdot B_{i, j}
$$

Hadamard product or elementwise product is an operation betwen two matrices where the corresponding elements are multiplie together

## Identity and Inverse Matrices:

An identity matrix is a matrix that does not change any vector when we multiply that vector by that matrix.

$$
\forall x \in R^n , I_{n}x = x
$$

For identity matrix all the entries along the main diagonal are 1 else are 0

The matrix inverse of $A$ is denoted as $A^{-1}$, and is defined as the matrix such that

$$
A^{-1}A = I
$$

## Determinants:

Determinants give the factor by which the space is modified.

For a 2D matrix the determinant will tess us how the area is modified

For a 3D matrix the determinant will tess us how the volume is modified

# System of Linear Equations:

$$
Ax = b
$$

where $A \in \mathbb{R}^{m \times n}$ is a known matrix, $x \in \mathbb{R}^{n}$ is a vector of unknown variables we would like to solve for.

We can rewrite the equation as follows

$$
A_{i, :} \cdot x = b_{i}
$$

Using inverse matrices we can write the solution for the above equation as follows:

$$
Ax = b \\ 
A^{-1}Ax = A^{-1}b \\
I_{n}x = A^{-1}b \\ 
x = A^{-1}b
$$

In order for $A^{-1}$ to exists the equation must have exactly one solution for every value of b.

To analyze how many solutions the equation has, we can think of the columns of $A$ as specifying different directions we can travel from origin and $x$ specifying how far we should travel in each of those directions.

$$
Ax = \sum_{i} x_{i}A_{:, i} 
$$

In general this kind of operation is called a linear combination.

The span of a set of vectors is the set of all points obtainable by the linear combination of the original vectors.

Thus, determining whether $Ax = b$ has a solution amounts to testing whether $b$ is in the span of columns of $A$

In order for the system $Ax=b$ to have solution for all values of $b \in \mathbb{R}^n$ we therefore require that the column span of $A$ be all of $\mathbb{R}^n$

The requirement that the column span of $A$ be all of $\mathbb{R}^n$ implies that $A$ must have atleast $n$ columns

This is a necessary condition but not sufficient. It is possible for some of the columns to be redundant.

Formally, this kind of redundancy is known as linear dependence. For the columns space of the matrix to encompass all of $\mathbb{R}^n$, the matrix must contain atleast one set of $n$ linearly independent columns.

A set of vectors is linearly independent if no vector in the set is a linear combination of the other vectors

In order for the matrix to have an inverse, we additionally need to ensure that $Ax = b$ has atmost one solution for each $b$ for this we need to ensure that the matrix $A$ has atmost $n$ columns.

Together this means that the matrix must be square and that all the columns of the matrix are linearly independent.

# Norms:
the norm of a vector $x$ measures the distance from the origin to the point $x$.

In machine learning, we usually measure the size of the vectors using a function called a *norm.*

Formally the $L^p$ norm is given by:

$$
\|x\|_{p} = (\sum_{i}|x_{i}|^p)^{\frac{1}{p}}
$$

The $L^2$ norm is called the *euclidean norm.* The $L^2$ norm is most frequently used in Machine learning and is denoted simply as $\|x\|$

$$
\|x\|_{1} = \sum_{i}|x_{i}|
$$

The $L^1$ norm is commonly used in machine learning when difference between zero and non-zero elements is very important.

$$
\|x\|_{\infty} = \max_{i}|x_{i}|
$$

Another norm that commonly arises in machine learning is the $L^{\infty}$ norm also known as the *max norm.* this norm simplifies to the maximum absolute value of the element in the vector.

# Special Matrices and Vectors:

## 1. Diagonal Matrices 
Consists of non-zero entries only along the main diagonal and have zero elsewhere.

We write $diag(v)$ to denote a square diagonal matrix whose diagonal entries are given by the entries of the vector $v$. 

Diagonal matrix are of interest because multiplying by a diagonal matrix is very computationally efficient.

To compute $diag(v)x$, we only need to scale each element $x_{i}$ by $v_{i}$. In other words $diag(v)x = diag(v) \odot x$


Inverting a square diagonal matrix is also efficient.

The inverse exists if every diagonal entry is nonzero, and in that case, $diag(v)^{-1} = diag([1/v_{1}, …, 1/v_{n}]^T)$

## 2. Symmetric Matrix
is any matrix that is also equal to its transpose
$$A = A^T$$
    
## 3. A unit vector
is a vector with unit norm
$$\|x\|_{2} = 1$$

## 4. Orthogonal vectors
A vector $x$ and vector $y$ are orthogonal to one another if $$x^Ty = 0$$

# Eigen decomposition:
Many mathematical objects can be understood better by breaking them into its constituent parts, or finding some properties of them that are universal.

We can also decompose matrices in ways that show us information about their functional properties that is not obvious from the representation of the matrix

Eigen decomposition decomposes a matrix into a set of eigenvectors and eigenvalues.

An eigenvector of a square matrix $A$ is a non-zero vector $v$ such that multiplication by $A$ alters only the scale of $v$

$$
Av = \lambda v
$$

the scalar $\lambda$ is called the eigenvalue corresponding to the eigenvector $v$.

If $v$ is a eigenvector of $A$ then so is any rescaled vector $sv$ where $s \in \mathbb{R}$, with the same eigenvalue $\lambda$.

Suppose that a matrix $A$ has $n$ linearly independent eigen vectors $\{v^{(1)}, ..., v^{(n)}\}$ with corresponding eigen values $\{\lambda_{1},..., \lambda_{n}\}$. We can concatenate all of the eigen vectors to form the matrix $V$ with one eigen vector per column $V = [v^{(1)},…, v^{(n)}]$. 

Likewise we can concatenate eigenvalues to form a vector $\lambda = [\lambda_{1}, …, \lambda{n}]$. The eigendecomposition of $A$ is then given by

$$
A = Vdiag(\lambda)V^{-1}
$$

A matrix whose eigenvalues:
1. all positive is called positive definite.
2. all non-negative is called positive semi-definite.
3. all negative is called negative definite.
4. all non-positive is called negative semi-definite.

# SVD - Singular value Decomposition:

SVD provides another way to factorize a matrix into singular vectors and singular values. 

Every real matrix has a singular value decomposition, but the same is not true of the eigenvalue decomposition.

For SVD we can rewrite $A$ as the product of three matrices.

$$
A = UDV^\top
$$

- $A$ - $m \times n$ matrix
- $U$ - $m \times m$ matrix is the left singular matrix and are the eigen vectors of $AA^\top$
- $V$ - $n \times n$ matrix is the right singular matrix and are the eigen vectors of $A^\top A$
- $D$ - $m \times n$ matrix are the singular values and are the eigen values of either $AA^\top$ or $A^\top A$

The most useful feature of the SVD is that we can use it to partially generalize matrix inversion to non-square matrices.

# Moore Penrose Pseudo Inverse:

Matrix inversion is not defined for matrices that are not square. 

Suppose we want to solve the linear equation $Ax=B$ where $A$ is a rectangular matrix then following cases arise for $A_{n \times m}$

1. $n < m$: there can be multiple many solution:
    
    Solving the linear equation with $A^+$ gives one of the many possible solutions with the minimal norm $\|x\|_{2}$
    
2. $m > n:$  there might not be any possible solution:
    
    Solving the linear equation with $A^+$ gives $x$ for which $Ax$  is as close as possible to $y$.
    

# Trace Operator:

the trace operator gives the sum of all diagonal entries of a matrix.

$$
\text{Tr}(A) = \sum_{i}A_{i, i}
$$

# Determinant:

The determinant is equal to the product of all the eigen values of the matrix.

The absolute value of the determinant can be through of as a measure of how much multiplication by the matrix expands or contracts the space.

If the determinant is $0$,  then the space is contracted completely along atleast one dimension, causing it to lose all of its volume.

If the determinant $1$ then the transformation is volume preserving.

# Principal Component Analysis:

We have a collection of $m$  points. We would like to apply lossy compression to the points. Lossy compression means storing the points in a way that requires less memory but may loose some precision.

For each point $x^{(i)} \in \mathbb{R}^n$ we will find a corresponding code vector $c^{(i)}\in \mathbb{R}^l$ such that $l < n$.

We will want to find some encoding function that produces the code for an input $f(x) = c$ and a decoding function that produces the reconstructed input given its code $g(f(x)) \approx x$

Here the encoding function is given by

$$
f(x) = D^\top x
$$

The decoding function is given by

$$
g(x) = DD^\top x
$$

The encoding matrix $D$ is given by the eigen vectors of $X^\top X$ corresponding to the largest eigen values.