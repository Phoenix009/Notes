
### Signalling:
#definition Exchange of messages among network entities to enable/provide a service.

Signalling is about maintaining state. Exchange of information between network components required to provide and maintain a service

Examples:
1. TCP Handshake (Connection setup and teardown)
2. RSVP (Resource Reservation Protocol)
3. SIP (Session Initiation Protocol)

### Signalling in the Internet:
No network signalling protocols in initial IP design =
- Connectionless (stateless forwarding by IP routers) + 
- best effort service
However, Transport protocols need state and variable initialisation


*Application Layer protocols enable users to be reachable independent of the device and location.*
### SIP - Session Initiation Protocol:
- IETF protocol
- all telephone calls and vide conference cals take place over the internet
- People are identified by email addresses rather than phone#
- Callee reachable independent of the location and device

#### Setting up a call:
Provide mechanism for :
- the caller to let callee know she wants to establish a call
- caller and callee to agree on media type and encoding
- end call

#### Determine current IP address of callee
- Require a mapping of mnemonic identifier to IP Address

#### Call Management:
- Add new media streams during call
- Change encoding during call
- Invite others
- Transfer and hold calls


*Reserve resources along end-to-end path for QoS for multimedia application*
### RSVP - Resource Reservation Protocol
- Allows users to communication requirements to network in robust and efficient way
- Designed with multicast


#### Multicast Service Model:
**Multicast Group**:
- Host sends IP datagram to multicast group using the multicast group address
- Hosts that have joined the group will receive the packets
- Routers forward multicast datagram to hosts

Routers actively participate in multicast making copies of packets as needed and forwarding them towards multicast receivers.


**Group Semantics**:
- Anyone can join multicast group
	- Host informs local multicast router of desire to join group - IGMP
	- Local router interacts with other routers to receive multicast datagrams - DVMRP, MOSPF, PIM etc.
- Anyone can send to multicast group
- No network layer identification to hosts of members

We need infrastructure to deliver multicast datagrams to all hosts that have joined that group

**IGMP - Internet Group Management Protocol**:
- Host sends IGMP report when application joins mcast group
	- IP_ADD_MEMBERSHIP socket option
	- Host need not explicitly "unjoin" group when leaving
- Router sends IGMP query at regular intervals
	- Hosts belonging to a multicast group must reply to query
