References: 
- [x] 11.5 RSA
- [ ] 12 Digital Signatures

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

If a public key encryption scheme has indistinguishable encryptions int he presence of an eavesdropper then the scheme is CPA secure

### Multiple Encryptions:
If public key encryption scheme is CPA-secure then it also has indistinguishable multiple encryptions.

an immediate consequence of this is that a CPA-secure public key encryption scheme for fixed length messages implies a public key encryption scheme for arbitrary length messages satisfying the same notion of security.

## Security against CCA:
Broadly speaking, in the public key setting there are two classes of chosen ciphertext attacks:
1. $\mathcal{A}$ might send a modified ciphertext $c^\prime$ to $\mathcal{R}$ on behalf of $\mathcal{S}$. Although it is unlikely that $\mathcal{A}$ would be able to obtain the entire decryption $m^\prime$ of $c^\prime$, it might still be possible for $\mathcal{A}$ to obtain some information about $m^\prime$ based on the subsequent behaviour of $\mathcal{R}$.
2. $\mathcal{A}$ might send a modified ciphertext $c^\prime$ to $\mathcal{R}$ in its own name and might obtain the entire decryption for $c^\prime$. 


## Impossibility of perfectly secret public key encryption:

[[Symmetric Cryptography#Perfect secrecy|What is perfect secrecy?]]

In contrast to the private key setting, perfectly secret public key encryption is impossible.

