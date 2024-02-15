In this chapter we examine advanced language based approaches. We discuss Feature oriented programming and aspect oriented programming both of which are composition based.

The goal of these techniques is to provide efficient means to implement product lines to make their variability, features and interactions explicit as well as to ease their handling and then reasoning about them.

We distinguish between two types: those that extend a language to express feature oriented concepts and those that provide tool support to develop and manage features.


# Feature Oriented Programming:
Feature oriented programming is a composition based approach for building software product lines that relies directly on the notion of features.

The idea is
- to decompose a systems design and code into the features it provides. Now the system structure aligns with its features. Ideally 1:1 mapping between the features and the code fragments
- Now we require language constructs to express which parts of the program contribute to which features and to encapsulate the features code in composable, modular units.

Feature Modularity has many benefits, in particular a simple mapping between features and their implementations which eases tracing and debugging.

## Collaboration Based Design:
Collaboration based design is the fundamental technique to decompose systems into collaborations.

==A **collaboration** is a set of interacting classes, each class playing a distinct role to achieve a certain function or capability.==

Typically a software system consists of multiple collaborations implementing multiple features. So a class often participates in the implementation of multiple features.

==A **role** defines the responsibilities a class takes in a collaboration== 

A class that plays multiple roles defines multiple sets of functionalities associated with individual roles.

==Composing collaboration is, in effect, superimposing them by lining up classes according to the roles they play.==

## Feature Models:
In feature oriented programming, each collaboration maps to a feature and is called a feature model.

A feature model refines the content of a base program (which itself may result from a composition of feature models) either by adding new elements or modifying and extending existing elements.

The order in which features are applied is important: earlier features in the sequence may add elements that are refined by later features.

## Discussion:
Feature modules can be composed statically at compile time or even dynamically at run time. Although some static composition mechanisms encode class refinements as inheritance, there are composition mechanisms that entirely inline extensions into the base code without runtime overhead.

### For Cross cutting Features and Traceability:
Feature oriented programming excels at feature traceability. There is a direct mapping from features to feature modules that implement them. A feature is implemented by a collaboration of classes contained in a feature modules. This way feature oriented programming supports the separation of crosscutting concerns which eases feature tracing.

### For Preplanning problem:
Using classic implementation approaches, many additions and modifications a feature has to apply have to be planned upfront when designing the extension points.

Using feature oriented programming, one can extend existing classes via class refinement, without touching existing implementations. In contrast to inheritance one can refine a class without breaking any client code

### For diamond problem and exploding class hierarchies:
Once we have multiple features, we want to compose them in different combinations. Classic approaches that rely on inheritance and delegation this leads to exploding class hierarchies or induces the diamond problem. With class refinements however one can flexibly combine extensions.




# Aspect Oriented Programming:
Cross cutting features leads to code scattering and code tangling. Aspect oriented programming aims at reducing such code scattering, tangling and replication induced by concerns that are not well separated.

in Aspect oriented programming: concerns that can be localised well using classic implementation, such as classes and methods are implemented further using these mechanisms; all other concerns that crosscut the implementation of other concerns are implemented as aspects.

==An **aspect** encapsulates the implementation of a crosscutting concern.==

The base program and the aspects are combined using an aspect weaver forming an executable program..

==An **aspect weaver** merges the separate aspects of a program and the remaining program elements at user selected program locations called join points.==

In some sense, the aspect weaver produces again tangled and scattered code, but hidden from the programmer. 

==A **join point** is an event in the execution of the program at which aspects can be woven into the program. the source code locations that give rise to a join point are called its **join point shadows**==

In most aspect oriented language, aspects contain pointcuts, advice and inter type declarations. A pointcut defines which join points an aspect affects.

==A **pointcut** is a declarative specification of the join points that as aspect affects. It is a predicate that determined whether a given join point matches.==

==An **advice** is a method like element of an aspect that encapsulates the instructions that are executed at a set of join points.==

## Discussion:
- the granularity of extensions is driven by the join point model of the aspect oriented language.
- Proper feature traceability is achieved by using one aspect to implement one feature.
- The most controversial concept of aspect programming is that the base program is oblivious with regards to the aspects that hook into the system. An aspect may effect internals of other modules directly, possible even breaking module interfaces.
- Another problem is that if programmers are not aware of the extensions, they are also not aware of the possible patterns induced by these extensions.
- there are no guaranteed invariants of interfaces and no hiding barriers of location information. What if the programmer modifies the base program and in which join points are removed accidentally. This problem is also called the fragile-pointcut problem.
