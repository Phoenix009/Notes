Reference: https://cacr.uwaterloo.ca/hac/
- [x] 1.13–1.13.3, 
- [ ] 10.3.3, 
- [ ] 12.5.1 + 12.5.2

# Classes of attacks and Security Models

The attacks an adversary can mount can be classified as follows:
1. A passive attack where the adversary only monitors the communication channel. A passive attacker only threatens confidentiality of data
2. An active attack is one where the adversary attempts to delete, add or in some other way alter the transmission channel. An active attacker threatens data integrity, authentication as well as confidentiality.

## Attacks on encryption schemes:
### 1. Cipher text only attack:
Adversary tries to deduce the decryption key or plaintext by only observing the cipher text. encryption scheme vulnerable to this attack is completely insecure

### 2. Known plaintext attack
Adversary has a quantity of plaintext and corresponding cipher text. This type of attack is only marginally more difficult to mount.

### 3. Chosen plaintext attack
Adversary choses plaintext and is then given corresponding cipher text. Then tries to deduce information to help him deduce other cipher texts

### 4. Adaptive chosen plaintext attack
the choice of plaintext may depend on the cipher text received from previous requests

### 5. Chosen cipher text attack:
one where adversary selects the cipher text and is then given the corresponding plain text. the objective then is to be able to deduce the plain text from the other cipher text.


## Attacks on Protocols
### 1. Known-key attack:
Adversary obtains some keys that were used previously and uses them to deduce the new key being used.

### 2. replay:
adversary records a communication session and replays the entire or partial session at some later point in time

### 3. impersonation:
Adversary assumes the identity of one of the legitimate parties in a network

### 4. dictionary:
Usually an attack against passwords, A password is stored. Typically passwords are stored as hashed values. When a user logs on and enters a password, it is hashed and checked against the stored hash.

Adversary can take a list of all probable passwords hash them and try to match the hashes hoping to finding a match.


### 5. Forward search:
The attack is similar to the dictionary attack and is used to decrypt messages. (Makes sense when the message space $\mathcal{M}$ is small??) (Wont this type of attack require deterministic encryption scheme and if that wont this type of scheme be vulnerable to chosen plain text attack??)

### 6. Interleaving attack:
this type of attack usually involves some form of impersonation in an authentication protocol


## Models for evaluating security
The security of cryptographic primitives and protocols can be evaluated under several different models. 


### 1. Unconditional security
Unconditional security for for encryption systems is called perfect secrecy.
For perfect secrecy, the uncertainty in the plaintext, after observing the cipher text must be equal to the priori uncertainty about the plain text.
$$\text{Pr}[M=n \mid C=c] = \text{Pr}[M=m]$$
A necessary condition for symmetric key encryption schemes to be unconditionally secure is that the key be at least as long as the message.

Asymmetric key encryption schemes cannot be unconditionally secure since, given a cipher text $c$, the plaintext can be in principle be recovered by encrypting all possible plaintext until $c$ is obtained.


### 2. Complexity theoretic security
An appropriate model of computation is defined and adversaries are modelled as having polynomial computational power. A proof of security relative to the model is then constructed. 

Asymptotic analysis and worst case analysis is used and so care must be taken when determining proofs have practical significance.

### 3. Provable security
A cryptographic scheme is said to be provable if the difficult of defeating it can be show to be equivalent to solving a well known and supposedly difficult problem.

### 4. Computational Security
A proposed scheme is said to be computationally secure if the perceived level of computation required to defeat it exceeds, by a comfortable margin, the computational resource of the hypothesised adversary.

Most of the best known private and public key encryption schemes belong to this class. This class is aka practical security.





# Challenge-response identification

The idea of cryptographic challenge response protocol is that one entity (the claimant) proves its identity to another entity by demonstrating knowledge of a secret known to be associated with that entity, without revealing the secret itself to the verifier.

## Time variant parameters
time variant parameters may be used in identification protocols to counteract replay and interleaving attacks, to provide uniqueness or timeliness guarantees and to prevent certain chosen text attacks

Time variant parameters which serve to distinguish one protocol instance from another are sometimes called **nonces**, **unique number** or **non-repeating values**.

**Nonce**:
	is a value used no more tan once for the same purpose. Typically serves to prevent replay attack.

### Random numbers:
- Random numbers may be used in challenge-response mechanisms to provide uniqueness and timeliness assurances.
- In protocol description, "choose a random number" is usually intended to mean "select from a uniform distribution"
- One entity includes a new random number in an outgoing message. A subsequent incoming message, whose construction required the knowledge of this nonce and to which this nonce is inseparably bound, is then deemed to be fresh based on the reasoning that the random number links the two messages.

### Sequence numbers:
- A sequence number serves as a unique number identifying a message, and is typically used to detect message replay.
- Sequence numbers are specific to a particular pair of entities, and must explicitly or implicitly be associated with both the originator and recipient of a message; distinct sequences are customarily necessary for messages from $A$ to $B$ and from $B$ to $A$

### Timestamps:
- Timestamps may be used to provide timeliness and uniqueness guarantees, to detect message replay.
- May also be used to implement time-limited access privileges and to detect forced delays.



## Challenge-response by public key techniques
Public key techniques may be used for challenge-response based identification with a claimant demonstrating knowledge of its private key in of the two ways:
1. the claimant decrypts a challenge encrypted under its public key
2. the claimant digitally signs a challenge



