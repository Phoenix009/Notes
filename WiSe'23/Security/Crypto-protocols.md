Reference: https://cacr.uwaterloo.ca/hac/
- [x] 1.13–1.13.3, 
- [ ] 10.3.3, 
- [ ] 12.5.1 + 12.5.2

# Challenge-response identification (Strong Authentication)

The idea of cryptographic challenge response protocol is that one entity (the claimant) proves its identity to another entity by demonstrating knowledge of a secret known to be associated with that entity, without revealing the secret itself to the verifier.

## Time variant parameters
Time variant parameters may be used in identification protocols to counteract replay and interleaving attacks, to provide uniqueness or timeliness guarantees and to prevent certain chosen text attacks

Time variant parameters which serve to distinguish one protocol instance from another are sometimes called **nonces**, **unique number** or **non-repeating values**.

**Nonce**:
	is a value used no more than once for the same purpose. Typically serves to prevent replay attack.

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



# Crypto Protocols and Vulnerabilities

## 1. Needham Schroeder Protocol
The protocol provides key transport of distinct keys $k_A$, $k_B$ from $A$ to $B$ and $B$ to $A$, respectively, as well as mutual authentication.

$$
\begin{aligned}
A \rightarrow B &: (n_A)_{pk_B} \\
A \leftarrow B &: (n_A, n_B)_{pk_A} \\
A \rightarrow B &: (n_B)_{pk_B}
\end{aligned}
$$

This protocol however is not secure, 