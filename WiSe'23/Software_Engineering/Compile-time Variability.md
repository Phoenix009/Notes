The goal of compile-time variability is to compile and include only code that is needed; typically, a tool decides which code fragments the compiler receives.

# 1. Version Control Systems:
Version-control systems track changes in source code and other development artefacts to facilitate collaborative development.

## Terminology:
Version-control systems are best known for tracking ==revisions== of source code, that is, variation over time.

More version control systems allow some form of ==branching==. In different branches same files can be changed independently.

Branching creates a new variant and ==merging== combines two variants. In practice merges are mostly performed in a line-based fashion using textual patches, and only when there is a conflict manual intervention is required.

A ==release== is a selected revision of the software that is given a specific name or number and is deployed to the customers.

> [!info]
> - A *revision* describes ordered variations over time; 
> - a *release* is a specifically named revision;
> - a *variant* describes intentional variations that exist in parallel.
> - Versions encompass both revision and variants

### customer-branch:
- Developers familiar with branching often use it to build customer-specific variants. 
- All development of code that is shared by all products happens on the main branch. 
- Upon release, a new branch is created per customer that request some modifications or extensions.
### feature-branch
- Instead of developing each product in a separate branch, we can also implement each feature in a distinct branch and create products by merging corresponding feature branches.

## Version control for feature variability
As a product line evolves, problems accumulate. Unless only few small variations are required for few customers, the use of version control systems should be restricted to revision control and not for feature variability.

the drawbacks are as follows:
- In principle, we could develop each feature in a separate branch and merge them depending on a feature selection; 
- however, this pattern relies strongly on ==effective merging and is problematic for related features that touch similar code fragments==
- Merging branches is a problematic composition technique. 
- Although language-specific merge mechanisms exist merging is usually performed in a text-based fashion, oblivious to the meaning of the merged artefacts

## Discussion:
## Strong Points:
- Well known, established and mature tools
- Arbitrary compile time customisation independent of granularity and cross cutting
- Minor effort in pre planning
### Weak Points:
- No feature traceability, no separation of feature code and no information hiding
- Relies on merging, prone to conflicts and errors.
- Hard to maintain with many branches, Fixes must be pushed to all the branches.

# 2. Build Systems:
A build system is responsible for scheduling and executing all build-related tasks, which may include running generators, compiling source code, running tests, and creating and copying deliverable units.

Since a build system already decides when, what and how to compile, it is an obvious candidate to manage compile time variability.

## Variability in build scripts
- Shell scripts and batch files are typical means to automate tasks, and can also be used to automate builds.
- We can introduce variability to build scripts by passing parameters and compiling one of alternative implementations.
- Depending on the parameters, different files are compiled and packaged.

## Custom build scripts
- Product line developers create a custom build script for each customer.
- A customer specific build script may replace files from the base implementation and can add additional files to the build

## Discussion:
- Build systems are suitable to control variability at file level. They can directly control which files to compile under which condition
- At the file level, a build system is language agnostic and can uniformly implement variability for different code and non-code artefacts
- As long as features can be mapped to files, build systems are well suited for feature oriented product lines. 
- File level granularity can be problematic when only small changes are to be integrated. This requires need to replicate entire files.
- However, many implementation patterns can be used to reduce replication and enable composition.
- Developers can encode feature-specific extensions as observers, strategy objects and decorators in separate files or use the factory method pattern to control which classes to instantiate in a central place
- In combination with other variability mechanisms build systems are a common suitable and well known means to implement variability especially as a form of conditional compilation for entire files.


# 3. Preprocessors
- A preprocessor is a tool that manipulates source code before compilation.
- It provides directives to inline files, to define macros, and to remove code fragments based on user-defined conditions.
- Conditional compilation allows code fragments in the source code to be conditionally removed before compilation.
- Conditional compilation is the mode common technique for implementing variability in product lines in industrial practice.

## Variability with Preprocessors
- typical way to implement variability with preprocessors is to wrap a code fragment with conditional compilation directives such as `#ifdef` and `#endif`, such a code fragment is called **annotated**.
- Depending on whether a certain macro is defined, the code fragments are included or removed before compilation.

## Disciplined Annotations:
Preprocessors are powerful tools that allow almost arbitrary source code manipulation at compile time. The power comes at the price of writing complex and hard to maintain and understand source code.

We say an annotation is disciplined, if it aligns with the source code structure of a language.

Disciplined annotations can be mapped to elements of the documents abstract syntax tree and are restricted to selected structures, typically top level declarations fields and statements but not subexpressions or parameter declarations.

The notion of disciplined annotations can help developers to avoid complex ad hoc implementations that might be hard to maintain. We argue that disciplined annotations are usually also easier to read and maintain.

## Discussion:
### Strong Points:
- Easy to use
- Simple programming model
- Compile time customisation of the source code. No boiler plate code
- Little preplanning required

### Weak Points:
- Scattering and tangling of feature code and configuration knowledge
- No support for information hiding
- Prone to simple errors
- Difficult to analyse and to write tool support for the underlying language.