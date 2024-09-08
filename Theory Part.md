
## Configuration Representation

### Set Representation

|                      |                                            |                                    |
| -------------------- | ------------------------------------------ | ---------------------------------- |
| $F$                  | Set of Features                            | $\{a, b\}$                         |
| $S = \mathscr{P}(F)$ | Set of all possible feature selections     | $\{\{\}, \{a\}, \{b\}, \{a, b\}\}$ |
| $C \subseteq S$      | Set of variants / Valid feature selections | $\{\{a\}, \{b\}\}$                 |

### Function Representation

|                             |                                                                           |                      |
| --------------------------- | ------------------------------------------------------------------------- | -------------------- |
| $V: F \rightarrow \{0, 1\}$ | Provides a mapping of features to selected or deselected                  | $\{(a, 0), (b, 1)\}$ |
| $\chi: V \rightarrow S$     | $\chi(v) = \{f \mid v(f) = 1\}$ Gives a set of features that are selected | $\{b\}$              |

---

## Feature Model Representation

### Propositional Formulas

|                         |                                          |                                    |
| ----------------------- | ---------------------------------------- | ---------------------------------- |
| $F$                     | Set of Features                          | $\{a, b\}$                         |
| $S = \mathscr{P}(F)$    | Set of all possible feature selections   | $\{\{\}, \{a\}, \{b\}, \{a, b\}\}$ |
| $\Phi \in \mathbb{B}_F$ | Boolean formula over $F$                 | $a \Rightarrow b$                  |
| $C = [\![\Phi]\!]$      | System Variants/valid feature selections | $\{\{\}, \{b\}, \{a, b\}\}$        |
### Set Representation to Propositional Formulas
$$
\begin{align}

\eta &: S \rightarrow \mathbb{B}_F 
\\
\\
\eta(s) &: ( \bigwedge_{f \in s} f) \wedge (\bigwedge_{f \in F/s} \neg f) 

\end{align}
$$

### Function Representation to Propositional Formulas
$$
\begin{align}

\eta &: V \rightarrow \mathbb{B}_F 
\\
\\
\eta(v) &: ( \bigwedge_{(f,1)\in v} f) \wedge (\bigwedge_{(f, 0) \in v} \neg f) 

\end{align}
$$



---

## Feature Diagrams
### Feature Diagrams to Propositional Formulas

---

## Feature Model Analysis:
### Valid Configurations:
Is a configuration valid?

$$
\begin{align}

\text{valid}(s, \Phi) &=  s \in [\![\Phi]\!]
\\
\\
\text{valid}(s, \Phi) &=  \text{SAT} (\Phi \wedge \eta (s))

\end{align}
$$

### Model Consistency:
Is there at least one valid configuration for the given feature model?

$$
\begin{align}

\text{consistent}(\Phi) &=  [\![\Phi]\!] \neq \emptyset
\\
\\
\text{consistent}(\Phi) &=  \text{SAT} (\Phi)

\end{align}
$$

### Selectable and De-selectable Features:

$$
\begin{align}

\text{selectable}(f, \Phi) &=  \exists s \in [\![\Phi]\!] \mid f \in s
\\
\\
\text{selectable}(f, \Phi) &=  \text{SAT} (\Phi \wedge f)
\\
\\
\text{deselectable}(f, \Phi) &=  \exists s \in [\![\Phi]\!] \mid f \notin s
\\
\\
\text{deselectable}(f, \Phi) &=  \text{SAT} (\Phi \wedge \neg f)

\end{align}
$$

### Dead Immortal and Mortal Features

$$
\begin{align}
\text{immortal}(\Phi) &=  \{f \mid \forall s \in [\![\Phi]\!] . f \in s\}
\\
\\
\text{immortal}(\Phi) &=  \{f \mid \neg \text{SAT}(\Phi \wedge \neg f) \}
\\
\\
\text{dead}(\Phi) &=  \{f \mid \forall s \in [\![\Phi]\!] . f \notin s\}
\\
\\
\text{dead}(\Phi) &=  \{f \mid \neg \text{SAT}(\Phi \wedge f) \}
\\
\\
\text{mortal}(\Phi) &=  F - \text{immortal}(\Phi) \cup \text{dead}(\Phi)
\end{align}
$$

---

## Measuring Cohesion and Coupling

|                      |                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------- |
| $\text{intdeps}(f)$  | Set of dependencies among program elements of feature $f$                                   |
| $\text{extdeps}(f)$  | Set of dependencies among program elements of feature $f$ with elements from other features |
| $\text{elements}(f)$ | Set of program elements of feature $f$                                                      |

$$
\begin{aligned}

\text{IFD} &= \frac{|\text{intdeps}(f)|}{|\text{elements}(f)| \cdot (|\text{elements}(f)|-1) / 2} \\

\\

\text{EFD} &= \frac{|\text{extdeps}(f)|}{|\text{intdeps}(f) \cup \text{extdeps}(f)|}

\end{aligned}
$$


## Code Scattering and Tangling

| | |
|-|-|
|$\text{M}$|Set of modules of a program|
|$\text{locations}(f)$| Set of code locations of feature $f$|
|$\text{locations}(m)$| Set of code locations of module  $m$|

**Feature Scattering Degree**:
$$SD(f) = |\{m \mid \text{locations}(f) \cap \text{locations}(m) \ne \emptyset \}|$$

**Module Tangling Degree**:
$$TD(m) = |\{f \mid \text{locations}(f) \cap \text{locations}(m) \neq \emptyset \}|$$
