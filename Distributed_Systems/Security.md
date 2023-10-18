In the context of distributed systems, Security is the science and art of building ==distributed systems that maintain their properties even when a subset of nodes are malicious or adversarial or *Byzantine*==.

**Fail-stop vs malicious behaviour**: Malicious behaviour contrasts with fail-stop behaviour, where a node either follows its role, or just stops participating (it never does any of the above)

A node is *malicious* if we cannot assume anything about its behaviour. In particular, it may behave in the worst possible way needed to break the properties of our distributed system.
- Refusing to participate in the system
- Sending out messages to other nodes in contravention of the protocol
- Declaring a false identity

**Origin of malicious behaviour**: node has been under the control of a malicious entity.

Read: [[Byzantine Fault Tolerance]]
