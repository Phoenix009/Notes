

| Team Member | Matriculation Number |
|-|-|
|Pranav Shetty | 7028806 |
|Jaideep More | 7043840 |

# Theoretical Part

## Cryptographic Protocols

### a
- No 
- there are still chances that the attacker can download or record the encrypted traffic between a client and a server over a period of time, once they gain access to the key, they can decrypt all of this information.
### b
- Yes 
- having different keys (asymmetric) between the two directions, will prevent reflection attacks. The messages are encrypted by the receiver’s public key. Hence, the attacker will not be able to decrypt the message if it tries to send it back to the sender.
### e.
- Certificate Transparency provides an auditing and monitoring system for the SSL certificates because of this all issued certificates are now visible to the domain owner.

- If a certificate needs to be issued the request is first sent to the CT log server which then records this and issues a SCT which the CA can use to show that the issued certificate has been logged on a particular server.

- This increases trust as the client now knows that this certificate has been logged on a server and someone will monitor it to ensure that it is valid.

- The domain owner or some other monitor can inspect the logs of the certificates and if an illegal certificate is found it can be revoked.

- If there is a threshold in place that says that the certificate will be issued publicly only after 24 hours and only if there are no complaints received within this time. An illegal certificate can be revoked even before the certificate is publicly released.

- But this depends on the vigilance of the monitors and how they monitor the logs. Hence, CT does not completely protect the clients against connecting to an attacker-controlled server that presents an illegitimate-but-valid certificate.


## Authentication
### a.
- Storing passwords using encryption in the database is not a good idea as it is a 2 way function these passwords can be decrypted easily. If the user passwords are not unique then identifying similar passwords and finding patterns in the encrypted text becomes easy especially in ECB mode.
  
- For eg: If the attacker figures out that the encrypted text ‘ecbd’ corresponds to plain text ‘x’, then in all the other encrypted texts even if the plain text passwords are different ‘ecbd’ will also correspond to plain text ‘x’ and the attacker can exploit this. Same passwords have the same encrypted text and hence it is easy to brute force the decryption key and then decrypt all passwords to plain text.
  
- Whereas, if cryptographic hash functions are used then all the hash values of the passwords are unique i.e. no 2 hash values of different passwords are the same and let’s say if the users have used identical passwords then the hash values will be the same however these hash values cannot be reverted back to plain text.
  
- The only possible way for the attacker to find out the plain text password is to guess the exact string that corresponds to that particular hash.

### b. 
#### 1.
The server does not use salting,
$$|S| = 62$$
$$L = 6$$

Possible Combinations,
$$|S|^L = 62^6$$
Bits of entropy, 
$$\begin{aligned}
e &= L \times \frac{\log{S}}{\log{2}} \\
&= 6 \times \frac{log(62)}{log(2)} \\
&= 36
\end{aligned}
$$
No. of possible precomputed passwords,
$$2^e = 2^{36}$$

The table will consist of $2^{36}$ entries.

And the table will require a space of,

$$64 * 2^{36} \text{ bytes} = 2^{42} \text{ bytes}$$
#### 2.
The server uses a completely random password salt of length $6$ chosen from a character-set with size $96$

Possible Salt Combinations = $96^6$

Bits of entropy $e = 40$

No. of possible precomputed salts = $2^e = 2^{40}$

No. of possible precomputed passwords = $2^{36} * 2^{40} = 2^{76}$

The table will now consist of $2^{76}$ entries.

### c.
**Data Leaks:** 
- If we are not using Passwordless authentication with WebAuthn (FIDO2) and the server gets compromised then the attacker gets access to the username and the passwords stored on the server and the attacker can then brute force these passwords if they are encrypted.
- However, if we are using Passwordless authentication with WebAuthn (FIDO2), the server does not store any secret keys and the attacker only gets access to the public key but without the private key he cannot do anything.

**Phishing Attacks:** 
- A normal phishing attack will not work because of the added layer of security that the challenge-response protocol provides. The attacker will only get access to the username in this case, since there is no involvement of a password and naturally there will be no response from the server with a challenge or nonce, hence everything will terminate with no data breach.
- Phishing in combination with MitM attacks could be identified easily because of the origin checking. The client signs on the information of his TLS/SSL channel and the server can check if it is the same channel that the server is using if not then there is a possible MitM attack.

**Keyloggers:** 
- When we are not using Passwordless authentication with WebAuthn and the  keylogger gets compromised then the attacker gets access to the passwords.
- Since we are using Passwordless authentication with WebAuthn there are no passwords stored on keyloggers, hence if it gets compromised the attacker gets nothing.


# Practical Part
## a.
1. Kevin thinks that this is Gru’s student ID s9grdesp followed by a year (yyyy)
- target hash: `a58fb25c653a1e3aed1b57dfe624c5ba`
- cracked password: `s9grdesp1965`
- command: `hashcat -m0 -a3 ./target.txt "s9grdesp?d?d?d?d"`

2. Kevin thinks that this a 8-digit PIN
- target hash: `ce89d3950f504ba091d77d33023dd225`
- cracked password: `11295172`
- command: `hashcat -m0 -a3 ./target.txt "?d?d?d?d?d?d?d?d"`

3. Kevin thinks that this is a fully random password with 5 characters
- target hash: `d76888fe4ed3a85126d6cbe1c9aa8cf5`
- cracked password: `ra$P7`
- command: `hashcat -m0 -a3 ./target.txt "?a?a?a?a?a"`

