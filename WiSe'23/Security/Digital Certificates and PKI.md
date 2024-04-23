# Intuition

One of the primary applications of digital signatures includes the secure distribution of public keys.

The key notion here is a digital certificate, which is simply a signature binding an entity to a public key. 

To be concrete, a party Charlie has generated a key pair $(pk_C, sk_C)$ for a secure digital signature scheme. Another party Bob also has generated a key pair $(pk_B, sk_B)$. Charlie knows that $pk_B$ is Bobs public key Then Charlie can compute the signature
$$
\text{cert}_{C \rightarrow B} = \text{Sign}_{sk_C}(\text{Bob's public key is }pk_B)
$$
and then give this signature to Bob.

We call $\text{cert}_{C \rightarrow B}$ a certificate for Bobs key issued by Charlie.

In practice a certificate should unambiguously identify the party holding a particular public key and so a more uniquely descriptive term like full name and email address or the URL of website.

Now if Bob wants to communicate with some other party Alice who already knows $pk_C$. Bob can send $(pk_B, \text{cert}_{C \rightarrow B})$ to Alice who can then verify that certificate is indeed a valid signature with respect to $pk_C$.

If Alice trusts Charlie, she can accept $pk_B$ as Bobs legit public key.

There are some details missing in the above description:
- How Alice learns $pk_C$
- How Charlie can be sure that $pk_B$ is Bobs public key
- How Alice decides to trust Charlie.

Fully specifying such details defines a PKI - public key infrastructure that enables widespread distribution of public keys.

# PKI Models:

## Single CA:
The simplest PKI assumes a single certificate authority CA who is completely trusted by everybody and who issues certificates for everyones public key.

Anyone who wants to rely on the services of the CA would have to obtain a legitimate copy of the CA’s public key $pk_{CA}$. This means that $pk_{CA}$ must be distributed over an authenticated channel.

In many popular browsers today CA's public key is provided together with the browser and the browser is programmed to automatically verify certificates as they arrive.

## Multiple CA:
While the model in which there is only one CA is simple and appealing, it is not very practical.

One approach is to rely on multiple CAs. A party Bob who wants to obtain a certificate on his public key can choose which CA it wants to issue a certificate.

Alice who is presented with a certificate or even multiple certificates issued by different CA's can then choose which CA's certificate she trusts.

# Delegation and Certificate Chains:
Another approach that alleviates the burden from a single CA. This approach however does not address the security concerns of having a Single point of failure.

Consider Charlie acting as a CA issues a certificate to Bob as in our original discussion. Bob in turn can issue his own certificates for other parties. Ex: Bob can issue certificate for Alice
$$
\text{cert}_{B \rightarrow A} = \text{Sign}_{sk_B}(\texttt{Alices's public key is }pk_A)
$$

Now if Alice wants to communicate with some fourth party Dave who knows Charlies public key but not Bob's, then Alice can send 
$$
pk_A,\ cert_{B \rightarrow A},\ pk_B,\ cert_{C \rightarrow B}
$$

Dave can do the following:
- Verify Charlie has signed the certificate $cert_{C \rightarrow B}$. This indicates the $pk_B$ is in fact Bob's public key.
- Verify Bob has signed the certificate $cert_{B \rightarrow A}$. This indicates the $pk_A$ is in fact Alice's public key.
If Dave trusts Charlie then he may accept $pk_A$ as being authentic key of Alice.



# Invalidating Certificates:
There are cases where we may want to invalidate the previously issued certificates. We will discuss only two relatively simple ideas.

## Expiration:
- One method for preventing certificates from being used indefinitely is to include an expiry date as part of the certificate.
- Using expiry dates provides a very coarse grained solution to the problems mentioned earlier.
## Revocation:
- One possibility is for the CA to include a serial number in every certificate it issues
- Each certificate should have a unique serial number and the CA will store the mapping from serial number to the certificates
- When a certificate is to be revoked the Ca can then search the serial number and revoke the certificate.
- At the end of the day, the CA will then generate **certificate revocation list - CRL** with the serial numbers of all revoked certificates. The CRL is then made available to verifiers.
- Verification of certificates then involves checking the signature in the certificate and verifying the CA's signature on the revocation itself.


# Rogue CAs:
Rogue CA's can issue certificates to existing domains allowing phishing attacks with a valid certificate. The following are the approaches to prevent them.

## DNS Certification Authority Authorisation - CAA:
- DNS entry to authorise CA's to issue a certificate for this domain.

| flag value | meaning |
| ---- | ---- |
| `<flag> 0` | default |
| `<flag> issue "letsencrypt.org"` | Allow lets encrypt to issue certificates for this domain |
| `<flag> issuewild "letsencrypt.org"` | Allow lets encrypt to issue wildcard certificates |
| `<flag> issue ";"` | no certificates may be issues |
| `<flag> iodef "mailto:admin@domain.com"` | where to send notifications about violated policies |
CAA should be followed by any CA, but cannot be really enforced. Ex: How do you know a certificate was issued by any CA for a given domain?

## DNS based Authentication of Named Entities - DANE:
- Replaces the reliance on CAs with the reliance on DNS.
- Relies on [[Network security#DNSSEC - DNS Security Extensions|DNSSEC]] to sign DNS records
- DNS administrator makes an authoritative binding between the domain name and a certificate that a host at that domain name might use.
- New DNS record type `TLSA` that can specify different usage models. Ex: a valid leaf certificate and its public key.
- Client check the TLSA entry against the servers certificate during certificate verification.

## Certificate Pinning:
- Certificate Pinning as a countermeasure against untrusted signed certificates
- Idea: Server instructs the client to cache a certificate -> trust on first use.
- However, pinning is ineffective if "first use" is in an adversarial setting.
- Alternatives:
	- Built in pins in the client
	- Out of bound channel ex: DANE

## Certificate Transparency
- Idea: Monitor and audit SSL Certificates
- ==Makes it impossible for a CA to issue an SSL certificate for a domain without the certificate being visible to the owner of that domain.==
- Provide an open auditing and monitoring system that lets any domain owner or CA determine whether certificates have been mistakenly or maliciously issued.

