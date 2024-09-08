# What is an Operating System?

Is a layer of software that manages a computers resources for its users and their applications.

## Resource Sharing:
One users should not be allowed to monopolise system resources or to access or corrupt another users files without permission. Enforcing and balancing these concerns is a central role of an operating system.

### Resource Allocation:
Operating System must keep all the activities separate, allocating resources to each as appropriate. Seemingly trivial differences in how resources are allocated can impact user perceived performance.

### Isolation:
An error in one application should not disrupt other applications or even the operating system itself. This is called **fault-isolation**.

Fault isolation requires restricting the behaviour of applications to less than the full potential of the underlying hardware.

### Communication:
Flip side of isolation is the need for communication between different applications and different users.


## Masking Limitations:

## Providing Common Services:
Operating system provides a set of common, standard services to applications to simplify and standardise their design. The choice of which services an operating system should provide is often judgement call.

For applications that can operate at a higher level of abstraction, the operating system serves as an interoperability layer so both applications and devices can evolve independently.


# Operating System Evaluation:
How do we choose among alternative designs of an operating system. We will look at several desirable criteria for operating systems.

TradeOffs between these criteria are inevitable. Improving a system along one dimension may hurt it along another.

## Reliability and Availability:
Reliability means that a system does exactly what it is designed to do. 

Making an operating system reliable is challenging. As the lowest level of software running on hardware, operating system can have devastating and hidden effects.

A related concept is availability, the percentage of time that the system is usable. 

A buggy operating system that crashes *frequently losing the users work* is both *unreliable* and *unavailable*. A buggy operating system that crashes frequently but *never loses users work is reliable but unavailable*. 

Both reliability and availability are desirable. Availability is affected by two factors.
1. the frequency of failures measured as MTTF Mean time to failure
2. the time taken to restore a system measured as MTTR mean time to repair

**Only precise abstractions provide a basis for constructing reliable and available systems.**

## Security:
Two concepts that are closely related to reliability are security and privacy. 
- Security means that the computers operation cannot be compromised by a malicious attacker.
- Privacy is an aspect of security that data stored on the computer is only accessible to authorised users.

An operating system can be and should be designed to minimise its vulnerability to attack. Even with strong fault isolation, a system can be insecure if its applications are not designed for security.

An operating system thus needs both an enforcement mechanism and a security policy.
- Enforcement is how the operating system ensures that only permitted actions are allowed.
- Security policy defines what is permitted.


## Portability:
All operating systems provide applications with an abstraction of the underlying computer hardware; a portable abstraction is one that does not change as the hardware changes. Portability also applies to the operating system itself.

To design operating system to achieve portability it helps to have simple standard way for applications to interact with the operating system.

The *AVM - Abstract Virtual Machine* is the interface provided by operating systems to applications including:
- the application programming interface
- the memory access model
- which instructions can be legally executed.

This notion of a portable hardware abstraction is so powerful that operating system use the same idea internally: The operating system itself can largely be implemented independently of the hardware specifics. *HAL - Hardware Abstraction Layer* is the interface that makes this possible.

## Performance:
