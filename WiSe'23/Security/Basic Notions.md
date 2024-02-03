# Basic Components:
## 1. Confidentiality:
Confidentiality is the concealment of information or resources. The need for keeping information secret arises from the use of computers in institutions with sensitive information such as government and industry.

## 2. Integrity:
Integrity refers to the trustworthiness of data or resources, and it is usually phrased in terms of preventing improper or unauthorized change.

Integrity includes **data integrity** (the content of the information) and **origin integrity** (the source of the data, often called authentication).

Integrity mechanisms fall into two classes: **prevention** mechanisms and **detection** mechanisms.

Prevention mechanisms seek to maintain the integrity of the data by blocking any unauthorized attempts to change the data or any attempts to change the data in unauthorized ways.

Detection mechanisms do not try to prevent violations of integrity; they simply report that the data’s integrity is no longer trustworthy.

## 3. Availability:
Availability refers to the ability to use information or resources. Availability is an important aspect of reliability as well as of system design because an unavailable system is at least as bad as no system at all.

System designs usually assume a statistical model to analyze expected patterns of use, and mechanisms ensure availability when that statistical model holds. Someone may be able to manipulate use (or parameters that control use, such as network traffic) so that the assumptions of the statistical model are no longer valid. 

This means that the mechanisms for keeping the resource or data available are working in an environment for which they were not designed. As a result, they will often fail.

## Additional Goals:
1. **Accountability**: Undeniably trace back actions to unique entity
2. **Authenticity**: property of being genuine and begin able to be verified and trusted
3. **Non-Repudiation**: inability to dispute that a certain action was take by an entity
4. **Anonymity**: remaining unidentified for certain actions
5. **Un-linkability**: inability to link two entities who are in fact the same

>[!note] 
>Not all security goals can be met at the same time
>Ex: anonymity vs accountability

# Model for Computer Security

## Threats:
A threat is a potential violation of security. 

The violation need not actually occur for there to be a threat. The fact that the violation might occur means that those actions that could cause it to occur must be guarded against.

the three security services - confidentiality, integrity and availability counter threats to the security of the system.

Attacks can be divided into four broad classes:
1. **disclosure**: unauthorised access to resources
2. **deception**: acceptance of false data
3. **disruption**: interruption or prevention of correction operation
4. **usurpation**: unauthorised control of some part of the system

## Policy and Mechanism:
A **security policy** is a statement of what is and what is not allowed.

Policy consists of a set of axioms that the policy makers believer can be enforced. Designers of policies always make two assumptions. 
1. The policy correctly and unambiguously partitions the set of system states into secure and insecure states
2. The security mechanisms prevent the system from entering insecure state

If either of the above two assumptions is erroneous the system with be insecure.


A **security mechanism** is a method, tool or procedure for enforcing a security policy.
The security mechanisms are either secure, precise or broad.

Let,
- $P$ - set of all possible states
- $Q$ - set of secure states (specified by the policy)
- $R$ - set of states the security mechanisms restrict the system to be in

Then, security mechanism is 
- **secure** if $R \subseteq Q$
- **broad** if $r \in R$ and $r \notin Q$

In practice, the security mechanisms are **broad**, i.e. they allow the system to be in nonsecure states.


## Security Model:
- **Asset/System Resource**: Hardware, software, Data, networks
- **Adversaries** pose threats to assets i.e. violation of security goals, exploiting vulnerabilities etc
- Owners of assets impose counter measures/**security mechanisms** to enforce security policies to reduce the risk to their assets from threats
- **Thread modelling** or **attacker modelling** identify possible threats and their mitigations in protecting a specific asset.
- **Attacks** are threats that are carried out.
	- **Active**: an attempt to alter system resources or affect their operation
	- **Passive**: an attempt to learn or make use of information from the system that does not affect system resources.
- **Attack Surface**:
	- Network
	- Hardware
	- Software
	- Human



