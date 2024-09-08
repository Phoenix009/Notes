- Why cannot we further automate boring repetitive activities such as testing and debugging?

- the problem is that we lack computer readable specifications for what the system should do or not do.

- S3 introduces a unified approach to learning oracles automatically

- given a software system; it infers ad decodes its inputs and outputs and runs experiments to extract models of how the system behaves, capturing the semantics by predicting the out features for given input features.

- these models serve as system invariants and allow to fully automate the critical software development activities.

- we want a future where testing debugging and monitoring would be taken over by software bots who would autonomously explore software behaviour, report issues and suggest actions to humans boosting developer productivity and software reliability.


- the aim of S3 is to fully automate testing, debugging ad monitoring of complex software systems.


Challenges and State of the Art:
- Bots first have to be able to interact with the software system by generating inputs examining outputs and relating these outputs to the inputs and thus explore the functionality. 

- The **input generation problem** (How can a bot generate inputs that reliably cover functionality?)
- Creating valid inputs that reliably reach code beyond input processing is still a challenge for which test generators still require human assistance either through input specification or a comprehensive input samples that cover behaviour.

- The **Test Oracle Problem** (How can a bot check the outputs of a system?)
- Compared to many aspects of test automation, the problem of automating the test oracle has received significantly less attention and remains comparatively less well-solved.

- Specification mining and program synthesis techniques mine oracle candidates from a program that match a set of given executions.


Approach for learning and leveraging system invariants:

- The recent works have allowed to automatically infer the input language of a system. Thus, we address the input generation problem

- We can create similar techniques to also infer the syntax and semantics of system outputs. A testing bot can thus decompose and check system outputs. Thus, addressing the test oracle problem

- 