# Arguments:
*Why should we care*?
Attempts of persuasion are everywhere and are a driving force in our society. Thus, their reasoning becomes crucial.

*How does it differ from a Rhetoric*
- Rhetoric is an attempt to persuade mostly through the power of words not through reason.
- Arguments are mainly about truth, rhetorics are mainly about emotions

*Definition:*
A collection of statements of which at least one functions as a ==conclusion== and the remainder as ==premises==. The premises are intended to support the conclusion

*What should be the premise and the conclusion for an argument?*
1. Your own argument?
	- Conclusion: what we want to argue for
	- Premise: reason to believe in our conclusion

2. Others Argument?
	- Depends on the context
	- Usually the first step when you try yo work out an argument is to identify the conclusion
	- Premises are harder to find than conclusions and requires both close and charitable reading


# Principle Of Charity:

## While reading:
- Find the best and strongest interpretation of any text
- Assume that every text was written by someone rational, capable and intelligent.
- Find charitable premises and conclusions when reconstructing another's arguments.

## While writing:
- Write as precise as if our addressee is a mean expert who loves to point out ambiguities, mistakes and inconsistencies.
- Write as easy and crisp as possible 


# Natural Language Pitfalls:
## Ambiguity:
- An expression is ambiguous in a context C iff there is more than one thing it could plausibly be taken to express in C.
- Ex: "I made her duck"

## Vagueness:
- An expression is vague iff there are border cases for which it is just not clear what it means.
- Ex: "He is bald"


## Rhetorics:
- A question is rhetorical when it is not really intended as a question, but as an assertion.
- Ex: "Do I really need to explain how rhetorics work?"

## Unclear Reference Class:
- It can be unclear whether the sentence is true or false. Requires explicit mention of the reference class.
- Ex: "Uncle Eddie is a fast runner"


# Deductive Arguments:
Validity and soundness: We want arguments to be good and sound arguments are often good arguments.

## Validity:
- An argument is valid iff it is not possible for all premises to be true and the conclusion to be false.

- When an argument is valid, the conclusion really follows from the premises.

- *How to show that something is not valid?*
	Provide a ==counterfactual== case where all the premises are true, but the conclusion is false.

*How to ensure validity?*
### Logical Validity:
- An argument is logically valid due to its logical structure.
- Formally: Let $P$ be the set of formalised premises and $C$ be the conclusion. Then $(\forall p \in P \mid \wedge p \rightarrow C)$ is a tautology.
- Formalise your argument to ensure its validity. There are two common structures:
	1. Modus Ponens
	2. Modus Tollens

### Analytical Validity:
- An argument is analytically valid iff it is valid due to the meaning of certain terms used in the argument
- This form of validity is common and formally correct but should be used with caution.


## Soundness:
An argument is sound iff it is valid and all its premises are true.

*How to tell if an argument is sound?*
![[soundness.svg]]

# Different Forms of Arguments:

| Textual Form | Standard Form | Logical Form |
|--------------|---------------|--------------|
| Good for communicating the argument | Good for checking the truth of the premises, missing / implicit premises and general structure | Good for checking validity 
| bad for assessing | bad for communicating | impossible to check truth/plausibility of premises, i.e. impossible to check soundness |



# Defeasible Reasoning
Reasoning is defeasible iff the corresponding argument is rationally compelling but not deductively valid

*Why use Defeasible Reasoning?*
- Default in the communication of everyday life:
	- We seldomly argue deductively in everyday life
		- This is sometimes impractical
		- Most people do not know how to do this
		- Often we do not have enough information to make a sound argument
	- We often make assumptions based on what is rather than what is not said and draw conclusions
	- We update our conclusions if new information comes in

- Helps us with generics
	- "Roses are red" is a shorthand for "Usually roses are red"

- Helps us with Pro-tanto reasons
	- A reason that can be outweighed by other reasons


*Common kinds of Defeasible Reasoning*
## Abductive Arguments:
- You have an observation and try to find the best possible explanation

## Analogies:
- You argue for a case by pointing to similarities to another case

## General Defeasible Arguments:
- Reasoning to a probable conclusion
- **Defeasible Force**:
	- An argument is defeasibly forceful iff it is not deductively valid, but given the premises and only the premises, it is sufficiently probable that the conclusion is true

- Note:
	1. Often we cannot assign specific probabilities
	2. Certainty and truth/falsity are two different things
	3. Probability is naturally a matter of degree and so is a defeasible force
	4. There is no cutoff point to defeasible force, it is a vague form

- **Defeasible cogency**:
	- An argument is defeasibly cogent iff it is defeasibly forceful and all the premises are true

## Inductive Arguments:
- Generalising from a set of observations to a general rule

## Evidence:
- Very roughly: $E$ is evidence for $X$ iff $P(X\mid E) > P(X)$
- There can be conflicting evidence of different strength
- Often the premises for defeasible arguments are evidence

# Rhetoric Ploys
- Rhetoric is an attempt to persuade mostly through the power of words used and not through reason.
- We usually should want to know reasons. Hence, we should be aware of rhetorical tricks.
- Rhetoric is not always bad and can be used in contexts where giving a reason is not required.

- **Presupposition**:
	- A statement presupposes $p$ iff in order to properly make sense of the statement you have to assume $p$

- **Buzz-wording**:
	- Using 'HOT' words when actually they are not useful in that context

- **Names-dropping**:
	- unnecessarily mentioning a lot of important persons, often without giving an explanation and making he listener feel like they need to know these people

- **Jargon**:
	- Using unnecessarily many technical terms and expert jargon that is not commonly known to the target audience

# Discourse
*Why engage in a discourse?*
- Finding out the truth or getting closer to the truth
- Improve your understanding and thinking about the world
- It has to be respectful, fair, open and unbiased
- Also has to be orderly and structured

>[!info]
> **WINNING** a discourse is not a goal of a discourse :)
> This would defeat the purpose of discourse

**Attacking an argument**
- ==showing an argument is not suitable to support its conclusion==
- showing that an argument does not give us sufficient reason to believe in its conclusion

*How to attack an argument:*
Assume we are attacking an argument having premise $p_1$, $p_2$, $p_3$ and conclusion $C$

1. **Show that it has at-least one false premise**
	p1: $\neg p_1$ (argument is given concluding $\neg p$)
	p2: if $\neg p_1$ then the argument is not suitable to support $C$
	c: The argument is not suitable to support $C$
  
2. **Show that the inference does not work**
	- For deductive arguments: show that the argument is invalid
	- For defeasible arguments: show that the argument is not defeasible forceful
	- Remember: a deductive argument is valid iff it is not possible for the conclusion to be false if all the premises are true 
	p1: argument is given concluding the ==argument is invalid==
	p2: if argument is invalid then the argument is not suitable to support $C$
	c: The argument is not suitable to support $C$

3. **Show that the reasoning is fallacious or that it is pseudo-reasoning**
	- an argument in text form is given that show which kind of fallacy or pseudo reasoning that argument is and why
	p1: an argument is given in text form concluding an argument to be a fallacy
	p2: if the argument is a fallacy then the argument is not suitable to support $C$
	c: The argument is not suitable to support $C$
   
4. **For defeasible argument: Show that there is a defeater**
	p1: There is a defeater
	p2: If there is a defeater then the argument is not suitable to support $C$
	c: The argument is not suitable to support $C$

5. **Show that it is not rationally persuasive to a target audience**
	**Rational Persuasiveness**:
	- an argument is rationally persuasive to someone iff it is epistemically rational for them to be persuaded by the conclusion given by the argument


*How does a discourse end?*
- It continues forever (in theory of-course)
- they give up and agree to disagree
- they ultimately withhold judgement *(neither yours nor mine)*
- one of them is convinced by an argument of other person

**Arguments about Morals:**
There are four kinds of arguments about morals:
1. **Reasoning from a single theory**:
	- if you are committed to moral theory T, and T says that an action has a certain deontic/normative status, then you also are committed to saying that that action has that particular deontic/normative status
	- *Points of disagreement*:
		- disagreement on the adequacy of moral theory
		- disagreement on the application of moral theory

2. **Reasoning from a broad range of moral theories**:
	- If all the theories agree on a deontic status of an action ==probably==, the deontic status is true
	- The defeasible force of this argument increases with
		- the number of moral theories considered
		- diversity of moral theories
	- *Points of disagreement*:
		- selection of theories not good enough: too few, too one sided
		- mode of inference not suitable: if the theories are wrong then ...
		- right application of theories

3. **Reasoning from consistent intuition**:
4. **Reasoning from strong intuition**:
	- *Points of disagreement*:
		- we do not share intuition, or are willing to give it up

# Argument Reconstruction
- We want to know whether we should be persuaded by what someone says
- We need to know if they make a good point
- We need to know what point they make

Steps:
1. Identify the conclusion
2. Get rid of extraneous material
3. Logical Streamlining
4. Write down the final reconstruction

Remarks:
1. Implicit and explicit premises and conclusion
2. Leave out things that are not relevant for the standard form
	- a premise is unnecessary if the argument would still be valid if we leave out the premise
	- collapse unnecessarily detailed premises into one premise
	- Soundness reasoning and background information do not belong in a standard form
3. Think ambiguous and vague aspects through
4. Intensional contexts: An intensional context is a context in which not only the designation of a term matters but also its meaning

# Fallacies and Pseudo-reasoning:
**Fallacies**: Bad arguments of a certain form
**Pseudo Reasoning**: something that should appear to be an argument, but in fact is not even a bad argument, but no argument at all

1. **Affirming the consequent**: Violates validity
	- arguments of the form
	- p1: $a \rightarrow b$
	- p2: $b$
	- c: $a$
	- The issue here is that true value of $b$ does not determine $a$

2. **Denying the antecedent**: Violates validity
	- arguments of the form
	- p1: $a \rightarrow b$
	- p2: $\neg a$
	- c: $\neg b$
	- The issue here is that if $a$ is false then $b$ can be anything

3. **Composition**: Violates validity
	- Just because some things are true individually, it does not mean that you can combine them in any context

4. **Equivocation**: Violates validity and truth of premises
	- an argument is not sound because one expression is used in two or more different ways

5. **Red herring**: Violates discourse
	- tries to distract from the actual argument by changing the topic

6. **Argument from bad argument**: Violates discourse and truth of premises
	- arguing that an argument was a bad argument and thus the conclusion can be dismissed
	- There could be other possible valid good argument that concludes the same conclusion

7. **Straw man**:
	- someone tries to ascribe a much more unfavourable argument to someone than he actually advocated

8. **Petitio Principii**

9. **No true scotsman**

10. **Post hoc ergo propter hoc**: (After this, therefore because of this)
11. **Cum hoc ergo propter hoc**: (With this, therefore because of this)

12. **False dilemma/dichotomy**
	- The impression is created that there are only two possibilities, when there are more. BAJPA ya CONGRESS

13. **Ad hominen**: Violates discourse
	- instead of attacking the argument, you attack the author of the argument
	- **tu quoque** or **whataboutism**:
		- I do not believe in climate change, Even Greta was spotted using plastic cups
	- **guilt by association**:
		- Yea, sure we all should become vegetarians, You know who else was vegetarian, Hitler
	- **Genetic attack**
	- **Deflection by insult**: Tu chutiya

14. **Slippery slope**:
	- tries to make the case that a relatively small first step would lead to unfavourable consequences, because this starts a chain of unfavourable effects
	- Ex: Baal lambe karlo, saari pehenlo, sadak pe nacho...

15. **Appeal to nature**: Homeopathy is natural and thus superior to all medicines
16. **Appeal to ignorance**
17. **Appeal to tradition**: humans have always consumed meat, so they should continue 

18. **Hasty generalisation**: Genearlising based on very few examples
19. **Essentialisation**: Essentialision based on very few examples


20. **Is-ought fallacy**: Just because we can do something does not mean we should
21. **Special pleading**: making a general claim but at the same time claiming that oneself is an exception

