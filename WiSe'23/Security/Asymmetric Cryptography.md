References: 
- [x] 11.5 RSA
- [x] 12 Digital Signatures

# Setting of Public Key Encryption

Public key techniques enable parties to communicate privately without having agreed on any secret information in advance.

Public key encryption is asymmetric. One party generates a pair of keys $(pk, sk)$ called the public key and the secret key. Public key is used to encrypt the message and secret key is used to decrypt the cipher text.

This encryption scheme removes the need for key exchange.

The main disadvantage of public-key encryption is that it is roughly 2x to 3x orders of magnitude slower the private key encryption. Thus, when private key encryption is an option then typically it should be used.


## Syntax of Public Key Encryption:

|  |  |
| ---- | ---- |
| $\text{Gen}$ | takes as input the security parameter $1^n$ and outputs a pair of keys $\langle pk, sk \rangle$  |
| $c \leftarrow \text{Enc}_{pk}(m)$ | $\text{Enc}$ takes as input a public key $pk$ of the receiver and a message $m$ and outputs a cipher text $c$ |
| $m := \text{Dec}_{sk}(c)$ | $\text{Dec}$ takes as input a private key $sk$ and a cipher text $c$ and outputs a message $m$ or a special symbol $\bot$ denoting failure |

# Security Discussion

## Security against CPA:
[[Symmetric Cryptography#CPA - Chosen plain text attacks|What is CPA?]]

If a public key encryption scheme has indistinguishable encryptions in the presence of an eavesdropper then the scheme is CPA secure

### Multiple Encryptions:
If public key encryption scheme is CPA-secure then it also has indistinguishable multiple encryptions.

an immediate consequence of this is that a CPA-secure public key encryption scheme for fixed length messages implies a public key encryption scheme for arbitrary length messages satisfying the same notion of security.

## Security against CCA:
[[Attacks and Security Models#5. Chosen cipher text attack|What is CCA?]]

Broadly speaking, in the public key setting there are two classes of chosen ciphertext attacks:
1. $\mathcal{A}$ might send a modified ciphertext $c^\prime$ to $\mathcal{R}$ on behalf of $\mathcal{S}$. Although it is unlikely that $\mathcal{A}$ would be able to obtain the entire decryption $m^\prime$ of $c^\prime$, it might still be possible for $\mathcal{A}$ to obtain some information about $m^\prime$ based on the subsequent behaviour of $\mathcal{R}$.
2. $\mathcal{A}$ might send a modified ciphertext $c^\prime$ to $\mathcal{R}$ in its own name and might obtain the entire decryption for $c^\prime$. 

## Impossibility of perfectly secret public key encryption:
[[Symmetric Cryptography#Perfect secrecy|What is perfect secrecy?]]

In contrast to the private key setting, perfectly secret public key encryption is impossible.

# Hybrid Encryption and KEM/DEM paradigms:
Public key cryptography is 2 to 3 times slower than private key encryption schemes. Is it possible to do better by using private key encryption in tandem with public key encryption schemes.

This improves efficiency because private key encryption is faster and improves bandwidth because private key schemes have lower ciphertext expansion.

The basic idea is to use a public key encryption to obtain a shared key $k$ and then encrypt the message $m$ using a private key encryption scheme and key $k$

The sender would share the key $k$ by
1. Choosing a uniform value $k$
2. encrypting $k$ using a public key encryption scheme
A more direct approach is to use a public key primitive called KEM - key encapsulation mechanism to accomplish both in a single step.

KEM has three algorithms:

|  |  |
| ---- | ---- |
| $\text{Gen}$ | Takes as input a security parameter $1^n$ and outputs a public-private key pair $(pk, sk)$ |
| $\text{Encaps}$ | Takes as input a public key $pk$ and the security parameter $1^n$. It outputs a cipher text $c$ and a key $k \in \{0, 1\}^{l(n)}$ |
| $\text{Decaps}$ | takes as input a private key $sk$ and a ciphertext $c$, and outputs a key $k$ or a special symbol $\bot$ denoting failure. |

Any public-key encryption scheme trivially gives a KEM by choosing a random key $k$ and encrypting it.



# RSA

## The Factoring Assumption:
Let $\text{GenModulus}$ be polynomial time algorithm that on input outputs $(N, p, q)$ such that $N = p \cdot q$ and $p$ and $q$ are both n-bit primes.

### Factoring experiment
1. Run $\text{GenModulus}(1^n)$ to obtain $(N, p, q)$.
2. $\mathcal{A}$ is given $N$, and outputs $p^′,q^′ \gt 1$.
3. The output of the experiment is defined to be $1$ if $p^′ \cdot q^′ = N$, and $0$ otherwise.

## RSA Assumption
The factoring problem has been studied for hundreds of years without an efficient algorithm being found.

This has motivated a search for other problems whose difficulty is related to the hardness of factoring.

### RSA experiment:
1. Run $\text{GenRSA}(1^n)$ to obtain $(N, e, d)$.
2. Choose a uniform $y \in \mathbb{Z}^∗_N$ .
3. $\mathcal{A}$ is given $(N,e,y)$ and outputs $x \in \mathbb{Z}^∗_N$.
4. The output of the experiment is defined to be $1$ if $x^e = y \mod N$, and $0$ otherwise.

in the above experiment to find $x$, the adversary must know $d$. then,
- $x \equiv y^d \mod N$
- $x^e \equiv (y^d)^e \equiv y^{d \cdot e} \equiv y \mod N$

So how can an adversary calculate $d$?
- We know for $x^{e \cdot d} \equiv x \mod N$ to hold
- $d \equiv e^{-1} \mod \phi(N)$ 
- an adversary knows $e, N$.
- Calculating $\phi(N)$ for a vary large composite number should be expensive
- But if i am able to obtain prime factorization of $N$ into $p$ and $q$ then $\phi(N) = (p-1) \cdot (q-1)$
- However, the prime factorization is difficult as discsussed in [[Asymmetric Cryptography#The Factoring Assumption |Factoring Assumption]]


## Plain RSA: 
### Plain RSA Encryption:
Let $\text{GenRSA}$ be as in the text. Define a public-key encryption scheme as follows:

- $\text{Gen}$: on input $1^n$ run $\text{GenRSA}(1^n)$ to obtain $N, e, d$. The public key is $\langle N, e \rangle$ and the private key is $\langle N, d \rangle$.
  
- $\text{Enc}$: on input a public key $pk = \langle N, e \rangle$ and a message $m \in \mathbb{Z}^∗_N$, compute the ciphertext
$$c := m^e\mod N$$

- $\text{Dec}$: on input a private key $sk = \langle N, d \rangle$ and a ciphertext $c \in \mathbb{Z}^∗_N$, compute the message
$$m := c^d \mod N$$

# Digital Signatures
Integrity (or authenticity) in the public-key setting is provided using digital signature schemes.

Signature schemes allow a signer $\mathcal{S}$ who has established a public key $pk$ to “sign” a message using the associated private key $sk$ in such a way that anyone who knows $pk$ (and knows that this public key was established by $\mathcal{S}$) can verify that the message originated from $\mathcal{S}$ and was not modified in transit.

## Comparison to MACs:
Both message authentication codes and digital signature schemes are used to ensure the integrity of transmitted message. However, there are important advantages of digital signatures over MACs as follows:

1. A qualitative advantage that digital signatures have as compared to MACs is that signatures are publicly verifiable.
2. Digital signature schemes also provide the very important property of non repudiation. that means that once $\mathcal{S}$ signs a message he cannot later deny having done so. MACs simply cannot provide non-repudiation.
3. MACs have the advantage of being shorted and roughly 2 to 3 orders of magnitude more efficient than digital signatures.

## Digital Signature Syntax:
A (digital) signature scheme consists of three probabilistic polynomial-time algorithms $(\text{Gen}, \text{Sign}, \text{Vrfy})$ such that:

|  |  |
| ---- | ---- |
| $\text{Gen}$ | takes as input a security parameter $1^n$ and outputs a pair of keys $(pk, sk)$ |
| $\text{Sign}$ | takes as input a private key $sk$ and a message $m$ from some message space. It outputs a signature $\sigma$, and we write this as $\sigma \leftarrow \text{Sign}_{sk}(m)$. |
| $\text{Vrfy}$ | The deterministic verification algorithm $\text{Vrfy}$ takes as input a public key $pk$, a message $m$, and a signature $\sigma$. It outputs a bit $b$, $b := \text{Vrfy}_{pk}(m, \sigma)$.  |

## Hash and Sign Paradigm:
The intuition behind the hash-and-sign approach is straightforward. Say we have a signature scheme for messages of length $l$, and wish to sign a (longer) message $m \in \{0,1\}^∗$. Rather than sign $m$ itself, we can instead use a hash function $H$ to hash the message down to a fixed-length digest of length $l$, and then sign the resulting digest.

