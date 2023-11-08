We will refer to any device that runs a link-layer protocol (such as hosts, routers, switches and WiFi access points) as a ==node==. 

We will also refer to the communication channels that connect adjacent nodes along the communication paths as ==links==.

Over a given link, a transmitting node encapsulates the datagram in a link-layer ==frame== and transmits the frame into the link.

## Where is the Link Layer Implemented?
For the most part, the link layer is implemented in a network adapter, also sometimes known as the network interface card NIC.

At the heart of the network adapter is the ==link-layer controller==, usually a single, special purpose chip that implements many of the link-layer services.


## Services provided by the Link Layer:
### Framing: 
Almost all link-layer protocols encapsulate each network layer datagram within a link-layer frame before transmitting over the link. MAC addresses used in frame headers to identify source and destination.

### Link Access:
A media access control MAC protocol specifies the rules by which a frame is transmitted onto the link.

Interesting case is when multiple nodes share a single broadcast link. Here MAC protocol serves to coordinate the frame transmissions of the many nodes.

### Reliable Delivery:
It guarantees to move each network-layer datagram across the link without error.

Can be an unnecessary overhead for low bit-error links. Many wired link-layer protocols do not provide reliable delivery service.

No loss $\longrightarrow$ No retransmission needed

### Error Detection and Correction:
Done by having the sending node include error-detection bits in the frame, and having the receiving node perform an error check.


## Error Detection and Correction:
Error detection and correction techniques allow the receiver to sometimes, but not always, detect that bit errors have occurred. 

Even with the use of error-detection bits there still may be undetected bit errors.

Bit-level error detection and correction involves detecting and correcting the corruption of bits in a link layer frame sent from one node to another node.

### Parity Checks:
The information to be sent $D$ has $d$ bits.

In even parity the sender adds one additional bit and chooses its value such that the total number of $1s$  in the $d+1$ bits is even

The receiver counts the number of $1s$ in the received bits. If an odd number of $1s$ are present, the receiver knows that some odd number of bit errors have occurred.

Consider a generalisation of one-bit parity across two dimensions.

Here, the $d$ bits in $D$ are divided into $i$ rows and $j$ columns. A parity value is computed for each row and column. The resulting $i+j+1$ parity bits comprise the link layer frame’s error detection bits.

With this two-dimensional parity scheme, the parity of both the column and row containing the flipped bit will be in error. T==he receiver can thus not only detect the single bit error but can also correct that error==.

The ability of the receiver to both detect and correct errors is known as forward error correction ==FEC==. FEC techniques are valuable because they can decrease the number of sender retransmissions required.

### Checksum:
In checksumming techniques, the $d$ bits of data are treated as a sequence of $k$-bit integers.

The Internet checksum is based on this approach.

Bytes of data are treated as $\texttt{16b}$ integers and summed. The $1s$ complement of this sum then forms the Internet checksum that is carried in the segment header.

The receiver checks the checksum by taking the $1s$ complement of the sum of the received data (including checksum) and checking whether the result is all $1$.

Checksumming methods require little packet overhead however, they provide relatively weak protection against errors as compared with cyclic redundancy check.

### Cyclic Redundancy Check CRC:
An error detection technique used widely in todays computer networks is based on CRC.

The sender and receiver must agree on an $r+1$ bit pattern, known as a $\text{generator}$ $G$.

For a given piece of data, $D$, the sender will choose $r$ additional bits, $R$ and append them to $D$ such that the resulting pattern is exactly divisible by $G$.

Process of error checking with $\text{CRC}s$ is simple, the receiver divides the received bits by $G$. If the remainder is nonzero, the receiver knows that an error has occured.

Each of the CRC standards can detect
1. burst errors of fewer than $r+1$ bits.
2. burst errors of length greater than $r+1$ can be detected with probability $1-0.5^r$
3. odd number of bit errors. 

#### Theory:
In CRC, all calculations are done in modulo-2 arithmetic. All additions are done without carry and subtractions without borrow. Thus $\texttt{a + b = a - b = a XOR b}$


![[CRC.png]]

How the sender computes $R$. We want to find $R$ such that there is an $n$ such that

$$
D.2^r \texttt{ XOR } R = nG
$$

by adding $R$ on both sides

$$
D.2^r \texttt{ XOR } R \texttt{ XOR } R = nG \texttt{ XOR } R
$$
$$
D.2^r= nG \texttt{ XOR } R \\
$$

thus, 
$$
R = D.2^r \mod G
$$

International standards have been defined for generators $G$.



## Multiple Access Links and Protocols:

A point-to-point link consists of a single sender at one end of the link and single receiver at the other end of the link.

A ==broadcast link==, can have multiple sending and receiving nodes connected to the same, single, shared broadcast channel.

The term broadcast is used here because when any one node transmits a frame, the channel broadcasts the frame and each of the other nodes receives a copy.

Because all nodes are capable of transmitting frame, more than two nodes can transmit frames at the same time, leading to ==collision==.

==How to coordinate the access of multiple sending and receiving nodes to a shared broadcast channel is the multiple access problem.==

In order to ensure that the broadcast channel performs useful work, computer networks have multiple access protocols by which nodes regulate their transmission into the shared broadcast channel.

### Ideal Multiple Access Protocol:
Given a broadcast channel of rate $R \text{ bps}$:
- When one node wants to transmit, it can send at rate $R$
- When $M$ nodes want to transmit, each can send at an average rate $R/M$
- Fully decentralised: No special node to coordinate transmission


### Channel Partitioning Protocols:

#### Time Division Multiplexing TDM:
TDM divides time into time frames and further divides each time frame into $N$ time slots.

Each slot is assigned to one of the $N$ nodes and a node can transmit packet in its assigned time slot only.

A node is limited to an average rate of of $R/N \text{ bps}$ even though its the only one transmitting.

#### Frequency Division Multiplexing FDM:
FDM divides the $R \text{ bps}$ channel into different frequencies and assigns each frequency to one of the $N$ nodes.

Thus creates $N$ smaller channels of $R/N \text{ bps}$ out of a single larger $R\text{ bps}$ channel

Same disadvantage as of TDM.

#### Code Division Multiple Access CDMA: 
CDMA assigns a different code to each node.

Each node encodes the data by using the uniquely assigned code.

Nodes can transmit simultaneously and yet have their respective receiver correctly receive a senders encoded data bits.


### Random Access Protocols:
In a random access protocol, a transmitting node always transmits at the full rate of the channel. 

When there is a collision, each node waits for a random amount of time before retransmitting the data. This continues until the data is transmitted without any collisions.

#### Slotted ALOHA:
All frames are $L$ bits long. Time is divided into slots of size $L/R$. 

When a node has a fresh frame to send, it waits until the beginning of the next slot and transmits the entire frame in the slot.

If there is a collision, the node detects the collision. The node retransmits its frame in each subsequent slot with probability $p$ until the frame is transmitted without any collision.

The efficiency of a slotted multiple access protocol is defined to be in the long-run, fraction of successful slots, in the case when there are a large number of active nodes, each always having a large number of frames to send.

The maximum efficiency of the protocol is given by $1/e = 0.37$. Thus, effective transmission rate of the channel is not $R\texttt{ bps}$ but only  $0.37R \texttt{ bps}$

#### ALOHA:
In pure ALOHA, when a frame arrives, the node immediately transmits the frame in its entirety into the broadcast channel.

If collision, the node will immediately retransmit with probability $p$.

The maximum efficiency of the pure ALOHA is only $1/2e = 0.18$. This is the price to be paid for a fully decentralised ALOHA protocol.

#### CSMA - Carrier Sense Multiple Access:
**Carrier Sensing**: a node listens to the channel before transmitting. If a frame from another node is currently being transmitted into the channel, a node then waits until it detects no transmissions for a short amount of time and then begins transmission.

**Collision Detection**: a transmitting node listens to the channel while its is transmitting. If it detects that another node is transmitting an interfering frame, it stops transmitting and waits a random amount of time before repeating the sense and transmit.

**Channel Propagation Delay**:  the amount of time it takes for a signal to propagate from one of the nodes to another, plays a crucial role in determining the performance.

The longer the propagation delay, the larger the chance that a carrier sensing node is not yet able to sense a transmission that has already begun.

#### CSMA/CD:
When a node performs collision detection, it ceases transmission as soon as it detects a collision. The it waits a random amount of time and then returns for sensing and transmission.

*What is a good interval of time from which to choose a random backoff time?*
We would like an interval that is short when the number of colliding nodes is small and long when the number of colliding nodes is large.

The ==binary exponential backoff== is used in Ethernet and elegantly solves this problem.

When a transmitting frame has experienced $n$ collisions, a node chooses $K$ at random from $\{0, 1, \dots, 2^n -1\}$. For ethernet, the actual amount of time a node waits is $K.512$ bit times, which is $K$ times the time it takes to send $512b$ in the ethernet.

The efficiency is given by 
$$
\frac{1}{1 + 5t_{prop} / t_{trans}}
$$
- $t_{prop}$: maximum propagation delay between two nodes in LAN
- $t_{trans}$: time to transmit max-size frame


### Taking Turns Protocols:
#### Polling Protocol:
The polling protocol requires one of the nodes to be designated as a master node. ==The master node polls each of the nodes in a roud-robin fashion.==

the polling protocol eliminates collisions and empty slots which allows polling to achieve much higher efficiency.

It introduces a polling delay which is the amount of time required to notify a node that it can transmit. If the master node fails, the entire channel becomes inoperative.

#### Token Passing:
A small special-purpose frame known as a ==token is exchanged among the nodes in some fixed order==. When a node receives a token, it holds onto the token only if it has some frames to transmit; otherwise it forwards the token to the next node.	


## Switched Local Area Networks

### Link Layer Addressing and ARP:

#### MAC Addresses:
A host or a router with multiple network interfaces will have multiple link layer addresses associated with it, just as it would also have multiple IP Addresses.

MAC Address is 6B long and has a ==flat structure==. This flat structure allows for portability, we can move LAN card form one LAN to another. No two adapters have the same address. 

When an adapter wants to send a frame to some destination adapter, the sending adapter inserts the destination adapters MAC address into the frame and then sends the frame into the LAN.

#### ARP - Adress Resolution Protocol:
*How does the sending host determine the MAC address for the destination host with a given IPv4 address*?

An ARP module on the sending host takes any IP address on the same LAN as input and returns the corresponding MAC address. This mapping is done using a
ARP table.

==ARP resolves IP addresses only for hosts and router interfaces on the same subnet.==

*How is the ARP table populated?*
If SRC wants to send a frame to DEST whose entry is not in the SRC's ARP table then, 
- An ARP query is broadcasted containing DEST's IP address
- DEST replies to SRC with its MAC Address
- SRC caches this information in its ARP table

*How does the sending host determine the MAC address for the destination host with a given IPv6 address*?
Each IP node on a LAN has a Neighbour table, an equivalent of an ARP table.

*How is the neighbour table populated?*
If SRC wants to send a frame to DEST whose entry is not in the SRC's ARP table then,
- SRC sends an ICMPv6 `neighbour_solicitation`
	SRC: A's link-local IPv6 address
	DEST: Solicited nodes multicast address `ff02::1:ff + [last 24b of B's IPv6 address]`
- All nodes on the LAN that share the last 24b with DEST receive this packet.
- DEST replies to SRC with a `neighbour advertisement` including its `IID` - Interface Identifier
- SRC caches this information in its neighbour table


*Now we have a host on a subnet that wants to send a network layer datagram to a host on another subnet. How can we send a datagram off the subnet?*

The IP Address is of the ultimate receiver host but the MAC address is of the next intermediate receiver which then passes the frame to the appropriate default router of the destination using routing. The destination router then uses the destinations MAC address and sends the packet in its subnet.

## Ethernet:

Ethernet Frame Structure:

![[EthernetFrame.png]]

1. $\texttt{Data (46-1500B)}$ : $\texttt{MTU}$ - Maximum Transmission Unit for ethernet is $\texttt{1500B}$
2. $\texttt{Destination Address (6B)}$: Contains the MAC address of the destination adapter
3. $\texttt{Source Address (6B)}$: Contains the MAC address of the adapter that transmits the frame into the LAN
4. $\texttt{Type (2B)}$: The type field permits ethernet to multiplex the network layer protocols.
5. $\texttt{CRC}$: Cyclic Redundancy Check is used for error detection
6. $\texttt{Preamble (8B)}$: The ethernet frame begins with an $\texttt{8B}$ preamble field.

 
==All of the Ethernet technologies provide connectionless and unreliable service to the network layer.== 

When sender adapter wants to send a datagram to destination adapter, sender adapter encapsulates the datagram into an ethernet frame and sends the frame into the $\texttt{LAN}$, without any handshaking.

When a frame fails the $\texttt{CRC}$ check the destination adapter simply discards the frame. No $\texttt{ACK}$ is given to the sending adapter.

The lack of reliable transport at link layer helps to make Ethernet simple and cheap

Earlier Ethernet was implemented as a $\texttt{BUS}$ topology or a $\texttt{HUB}$ based $\texttt{STAR}$ topology, in which frame collisions occurred and $\texttt{CSMA/CD}$ protocol was used for $\texttt{MAC}$.

Ethernet today is a $\texttt{SWITCH}$ based $\texttt{STAR}$ topology, using 
store-and-forward packet switching. Thus there are no collisions and no need for a $\texttt{MAC}$ protocol.

### Link Layer Switches:

The role of a switch is to receive incoming link-layer frames and forward them onto outgoing links.

==Filtering== is the switch function that determines whether a frame should be forwarded to some interface or should just be dropped.

==Forwarding== is the switch function that determines the interface to which a frame should be directed, and then moves the frame to those interfaces.

Switches filtering and forwarding is done using a switch table.

An entry in the switch table consists three entries

1. $\texttt{MAC}$ Address
2. $\texttt{InterfaceID}$
3. $\texttt{Created Time}$

Given a destination $\texttt{MAC}$ Address, the switch indexes the switch table with it and finds the $\texttt{Dest InterfaceID}$ there are three possible cases for a switch table:

1. If there is not entry for the destination address, the switch broadcasts the frame on all interfaces
2. If the $\texttt{Destination InterfaceID = Source InterfaceID}$ then no forwarding is necessary. Switch performs filtering and drops the frame
3. If the $\texttt{Destination InterfaceID != Source InterfaceID}$ then switch forwards the frame on the appropriate interface

*How are the switch tables configured?*

Switches are ==self-learning==. This capability is accomplished as follows:

1. Switch table is initially empty
2. For each incoming frame, the switch stores
    1. $\texttt{MAC}$ address of the source
    2. $\texttt{InterfaceID}$ of the interface from which the frame was received
    3. $\texttt{Created Time}$ which is the current time
3. The switch deletes an address in the table if no frames are received with that address as the source address after some period of time.

