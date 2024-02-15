
References: 
- [x] 2.2-2.3
- [x] 3.6.3
- [x] 4.2, 4.4
- [ ] 6.1, 6.3-6.4
- [ ] 7.2.5, 7.3.2

Modern cryptography involves the study of mathematical techniques for securing digital information, systems, and distributed computations against adversarial attacks.

# Setting of Private Key Encryption

Classical cryptography was concerned with designing and using codes that enables two parties to communicate secretly in the presence of an eavesdropper who can monitor all communication between them.

Security of all classical encryption schemes relied on a secret key shared by the communicating parties in advance and unknown to the eavesdropper. This scenarios is known as the private-key setting.

Two parties share a key and use this key when they want to communicate secretly. Note the same key is used to convert the plaintext to a cipher text and back; this is why it is also known as the symmetric-key setting.

## Syntax of Private Key Encryption:
|  |  |
| ---- | ---- |
| $\mathcal{M}$ | Message space |
| $\mathcal{K}$ | the finite key space |
| $\text{Gen}$ | is a probabilistic algorithm that outputs a key $k$ chosen according to some distribution. |
| $\text{Enc}_k(m)$ | Encryption function that takes as input a key $k$ and a plain text message $m$ outputs a cipher text $c$ |
| $\text{Dec}_k(c)$ | Decryption function that takes as input a key $k$ and a cipher text message $c$ outputs a plain text $m$ |

We assume perfect correctness, meaning that for all $k \in \mathcal{K}$ and for all $m \in \mathcal{M}$ the following holds 

$$\text{Dec}(\text{Enc}_k(m))_k = m$$

## Kerchoff's Principle:

The cipher method must not be required to be secret, and it must be able to fall into the hands of the enemy without inconvenience.

==A crypto-system should be secure if everything about the system, except the key is public knowledge.==

- it is significantly easier for the parties to maintain secrecy of a short key than to keep secret the (more complicated) algorithm they are using.
- it is much easier for them to change a key than to replace an encryption scheme.
- it is desirable for encryption schemes to be standardized so that 
	1. compatibility is ensured by default and
	2. users will utilize an encryption scheme that has undergone public scrutiny and in which no weaknesses have been found.


# Perfect secrecy:
For a scheme to be perfectly secret, observing the cipher text $c$ should have no effect on the adversary’s knowledge regarding the actual message $m$ that was sent; 

$$\text{Pr}[M=m \mid C=c] = \text{Pr}[M=m]$$
In other words, the a posteriori probability that some message $m$ was sent, conditioned on the cipher text $c$ that was observed, should be no different from the a priori probability that $m$ would be sent.

This formulation requires that the probability distribution of the cipher text does not depend on the plaintext.


# One Time Pad:
## Construction:
- for an integer $l$
- Message Space $\mathcal{M}$ = $\{0, 1\}^l$
- Key Space $\mathcal{K}$ = $\{0, 1\}^l$
- Cipher Text Space $\mathcal{C}$ = $\{0, 1\}^l$

| | |
|-|-|
| $\text{Gen}$ | chooses a key from $\mathcal{K}$ according to uniform distribution |
| $\text{Enc}$ | given a key $k$ and a message $m$, $c := m \oplus k$|
| $\text{Dec}$ | given a key $k$ and a cipher message $c$, $m := c \oplus k$|

## Limitations:
OTP - One time pad encryption scheme is perfectly secret. However, there are a few limitations.

### 1. Encrypting more than one message using the same key leaks information.
It is easy to see,
$$c_1 \oplus c_2 = m_1 \oplus k \oplus m_2 \oplus k = m_1 \oplus m_2$$

### 2. key should be as long as the message.
This limits the usefulness of the scheme for sending very long messages



# Computational Security
Perfect secrecy requires that absolutely no information about an encrypted message is leaked even to an eavesdropper with unlimited computational power. For all practical purposes however, this definition is rigid.

Security definitions that take into account computational limits of the attacker, and allow for a small probability of failure are called computational. For example, a scheme that leaks information with probability of $2^{-60}$ to eavesdropper investing $200$ years of computational effort is adequate for real-world applications.

Computational security incorporates two relaxations:
1. Given enough time an attacker may be able to violate security. If we can make the resources required to break the scheme larger than those available to any realistic attacker, then for all practical purposes the scheme is unbreakable.
2. Adversaries can potentially succeed with some very small probability. If we can make this probability sufficiently small, we need not worry about it.




# Block Ciphers:

## Pseudorandom Function
- We call $F$ pseudorandom if the function $F_k$ (for a uniform key $k$) is indistinguishable from a function chosen uniformly at random from the set of all functions having the same domain and range; that is, 
- if no efficient adversary can distinguish whether it is interacting with $F_k$ (for uniform $k$) or $f$ (where $f$ is chosen uniformly from the set of all functions mapping $n$-bit inputs to $n$-bit outputs).

## Pseudorandom Permutation


## CPA - Chosen plain text attacks
![[Crypto-protocols#3. Chosen plaintext attack]]

## CPA Secure Encryption from Pseudorandom Functions
- A naive attempt at constructing a secure encryption scheme from a pseudorandom permutation is to define $$Enc_k(m) = F_k(m)$$
- This method of encryption is deterministic and so cannot be CPA-secure. encrypting the same plaintext twice will yield the same cipher text
- Our secure construction is randomised. Specifically, we encrypt by applying the pseudorandom function to a random value $r$ (rather than the message) and XORing the result with the plaintext.

![[encryption-pseudorandom-function.png]]


# Modes of Operation
We have seen a construction of CPA-secure encryption scheme based on pseudorandom functions. However it has a drawback that the length of the cipher text is double the length of the plaintext.

Block ciphers mode of operation provide a way of encrypting arbitrary length messages using shorter cipher texts.

## ECB - Electronic Code Book

### Construction
![[ecb.png|center]]
naive mode of operation in which the cipher text is obtained by direct application of the block cipher to each plain text blocks.
$$c := \langle F_k(m_1), F_k(m_1), \cdots, F_k(m_l) \rangle$$
### Discussion
- ECB mode is deterministic and is not CPA-secure
- ECB mode is not EAV-secure
- ECB should never be used

## CBC - Cipher Block Chaining
### Construction
![[cbc.png|center]]
- A uniform initialisation vector $IV$ of length $n$ is first chosen
- set $c_0 = IV$
- for $i = 1 \text{ to } l, c_i = F_k(c_{i-1} \oplus m_i)$

### Discussion
- encryption in CBC mode is probabilistic and it has been proven that if $F$ is pseudorandom then CBC mode encryption is CPA-secure
- main drawback is that encryption must be carried out sequentially.


## CTR - Counter

### Construction
![[ctr.png]]
- a uniform value $ctr \in \{0, 1\}^n$ is chosen
- pseudorandom stream is generated $y_i = F_k(ctr + i)$
- the $i$th cipher block is calculated as $c_i = y_i \oplus m_i$

### Discussion
- encryption and decryption can be fully parallelised
- if $F$ is pseudorandom then CTR mode is CPA-secure

# [[Integrity]]

# [[Key Distribution]]

