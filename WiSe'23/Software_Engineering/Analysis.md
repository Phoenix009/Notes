
# Analysis Strategies

## Brute Force Analysis:

## Sampling:
### Sampling Strategies 
#### 1. Random Sampling
- Unbiased selection of sample configurations

$$\hat{C} \subseteq C \text{ with } Pr(c \in \hat{C}) = \frac{|\hat{C}|}{|C|}$$
- The size of $\hat{C}$ i.e $|\hat{C}|$ is a free parameter with $1 \le |\hat{C}| \le |C|$

##### How to implement random sampling?
1. **Enumeration**:
- Requires generating all the configuration list and then randomly sample from the set.
- enumerating the entire list is not feasible in most cases
- This implementation provides uniform randomness but is not scalable

2. **Using SAT Solver**:
$$v_{i+1} = \hat{\text{SAT}}(\Phi \wedge \neg(\eta(v_1)) \wedge \cdots \wedge \neg(\eta(v_i)))$$
- $v_i$ - represents a particular configuration $\{0, 1\}^n$ for $n$ configuration options
- $\Phi$ - represents a boolean formula representing a feature model
- $\eta({v_i})$ - gives the boolean formula that represents one particular configuration
- $\hat{\text{SAT}}$ - produces possible configuration assignment that satisfies the given boolean formula

- This implementation is scalable using available tools, however the sampling is not uniform.

3. **Distance Based**:
- Sample configurations according to distance from the origin (Hamming, Euclidean).
![[distance-based-sampling.png]]

- Here the feature selection is represented by a boolean space.
- Now we can define a distance on this boolean space.
- This implementation is scalable however there are no uniformity guarantees

**Problems with Random Sampling**:
1. Not all features may appear in the sample
2. Not all combinations of features may appear in the sample
3. Not all code may be present in the sample

#### 2. Coverage Based Sampling

##### 1. **T-wise Feature Coverage**:
Enforces that all subsets of $t$ features appear in the sample

Ex: for 1-wise feature coverage the following condition should hold

$$\forall f \in F : \text{selectable}(\Phi, f) \Rightarrow \exists c \in \hat{C} \mid f \in c$$
for every feature that is selectable, there should be at least one configuration in the sample that contains that feature.

Ex: for 2-wise feature coverage the following condition should hold
$$ \begin{aligned}

\forall (f_1, f_2) \in F \times F : \text{selectable}(\Phi, f_1 \wedge f_2)
\\
\Rightarrow \exists c \in \hat{C} \mid f_1, f_2 \in c 
\end{aligned}$$

##### 2. **T-wise interaction coverage**
Enforces that all combinations of selections and deselections of $t$ features appear in the sample

Ex: for 2-wise feature interaction coverage the following condition should hold.

$$ \begin{aligned}

\forall (f_1, f_2) \in F \times F : \\
&\text{selectable}(\Phi, f_1 \wedge f_2)
\Rightarrow \exists c \in \hat{C} \mid f_1 \in c , f_2 \in c \\
\wedge \\
&\text{selectable}(\Phi, f_1 \wedge \neg f_2)
\Rightarrow \exists c \in \hat{C} \mid f_1 \in c , f_2 \notin c \\
\wedge \\
&\text{selectable}(\Phi, \neg f_1 \wedge f_2)
\Rightarrow \exists c \in \hat{C} \mid f_1 \notin c , f_2 \in c \\
\wedge \\
&\text{selectable}(\Phi, \neg f_1 \wedge \neg f_2)
\Rightarrow \exists c \in \hat{C} \mid f_1 \notin c , f_2 \notin c \\
\end{aligned}$$



## Variability Aware Analysis
