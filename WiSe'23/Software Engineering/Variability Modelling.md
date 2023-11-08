- A **configurable software system** offers a number of ==configuration options== that a stakeholder can select to create a ==tailor made system variant==

- A **software product line** offers a number of ==features== that a stake holder can select to create a ==tailor-made software product==

What is a feature?
- Key abstraction of the application domain
- Fulfils one or more requirements
- Represents commonalities and variability among product variants
- Is a means to communicate among stakeholders
- Used to specify product variants

Basic Definitions:

| | | |
|-|-|-|
|$F$| Set of Features|$\{a, b\}$|
|$S = \mathscr{P}(F)$| Set of all possible feature selections|$\{\{\}, \{a\}, \{b\}, \{a, b\}\}$|
|$C \subseteq S$|Set of variants / Valid feature selections|$\{\{a\}, \{b\}\}$|

Function Representations:

| | | |
|-|-|-|
|$V: F \rightarrow \{0, 1\}$| Provides a mapping of features to selected or deselected|$\{(a, 0), (b, 1)\}$|
|$\chi: V \rightarrow S$| $\chi(v) = \{f \mid v(f) = 1\}$ Gives a set of features that are selected|$\{b\}$|

Domain Engineering:
Is the activity of collecting, organising and storing past experiences in building systems in a ==particular domain== in the form of reusable assets as well as ==providing an adequate means for reusing== these assets when building new systems


![[domain_and_application_engg.jpg]]

## Feature Modelling:
- A feature model describes the features of a system and the dependencies among them.
- Two Representations:
	- Propositional Formulas: Formal and machine readable
	- Feature Diagrams: informal and human readable

### Feature models as Propositional Formulas
- Boolean variable per feature
- Formula ($\Phi$) defines feature model
- Formula evaluates to true iff the feature selection is valid.

Example:
- Features: Win, Unix, Txn, Read, Write
- Constraints:
	- Either Win or Unix must be selected
	- if Txn is selected then Write must also be selected

$$F = \{ \text{Win}, \text{Unix}, \text{Txn}, \text{Read}, \text{Write} \}$$
$$ \Phi = (\text{Win} \oplus \text{Unix}) \wedge (\text{Txn} \Rightarrow \text{Write}) $$
Basic Definitions:

| | | |
|-|-|-|
|$F$| Set of Features|$\{a, b\}$|
|$S = \mathscr{P}(F)$| Set of all possible feature selections|$\{\{\}, \{a\}, \{b\}, \{a, b\}\}$|
|$\Phi \in \mathbb{B}_F$| Boolean formula over $F$| $a \Rightarrow b$|
|$C$|System Variants/valid feature selections|$\{\{\}, \{b\}, \{a, b\}\}$|

