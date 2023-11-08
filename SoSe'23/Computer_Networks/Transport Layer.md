How does TLS work?

### Introduction:

A transport layer protocol provides for ==logical communication between application process== running on different hosts. 

Transport layer protocols are implemented in the end systems but not in network routers.

On the sending side, the transport layer converts the application layer messages into transport layer packets known as segments.

More than one transport layer protocol may be available to network applications. For example, the internet has two protocols - TCP and UDP.


### Relation between Transport and Network Layers:

A transport layer protocol ==provides logical communication between processes== running on different hosts, a network layer provides ==logical communication between hosts==. The distinction is subtle but important.

The services that a transport layer protocol can provide are often constrained by the service model of the underlying network layer protocol. 

If the network layer protocol cannot provide delay or bandwidth guarantees, then the transport layer protocol cannot provide delay or bandwidth guarantees for the application layer.

However, certain services can be offered by a transport protocol even when the underlying network protocol does not offer the services.


### Overview of the Transport Layer in Internet:

The most fundamental responsibility of the transport layer protocol is to extend IP’s delivery service between two hosts to a delivery service between two processes running on these hosts.

==Extending host-to-host delivery to process-to-process delivery is called transport-layer multiplexing and demultiplexing.==

Process to process communication and error checking are the two minimal transport-layer services and the only services that are offered by UDP.

TCP on the other hand, offers several additional services to applications. First and foremost, Reliable Data Transfer, TCP converts IP’s unreliable service between two hosts into a reliable data transfer service between processes.

TCP also provides congestion control. TCP congestion control prevents any one TCP connection from flooding the links and routers. It gives each connection an equal share of the link bandwidth.


### Multiplexing and Demultiplexing:

The job of delivering the data in transport layer segment to the appropriate socket is called demultiplexing.

the job of gathering data chunks at the source host from different sockets, and passing the segments to the network layer is called multiplexing.

A process can have one or more sockets, through which data passes from the network to the process and through which data passes form the process to the network.

Transport layer multiplexing requires:
1. sockets have unique identifiers.
2. Each segment have special fields that indicate the socket to which the segment is to be delivered.

These special field is called the ==port number==.
When we develop a new network application, we must assign the application a port number.


[[Connectionless Transport - UDP]]

[[Principles of Reliable Data Transfer]]

[[Pipelined RDT]]

[[Connection Oriented Transport - TCP]]

### Principles of Congestion Control:

#### Congestion Collapse:
Increase in network load results in decrease of useful work done 

#### Costs of Congestion:

1. Large queuing delays are experienced as the packet arrival rate nears the link capacity
2. The sender must perform retransmissions in order to compensate for dropped packets due to buffer overflows
3. Un-needed retransmissions by the sender in the face of a large delays may cause a router to use its link bandwidth to forward un-needed copies of a packet.
4. When a packet is dropped, the transmission capacity that was used at each upstream links to forward the packet was wasted.

#### Approaches to Congestion Control:

We can distinguish among congestion control approaches by whether the network layer provides any explicit assistance to the transport layer.

##### 1. End-to-End congestion control:

The network layer provides no explicit support to the transport layer for congestion control purposes. The congestion in the network must be inferred by the hosts by observing network behavior.

##### 2. Network-Assisted Congestion Contol:

Network layer components provide explicit feedback to the sender regarding congestion.

The congestion information is fed to sender in two ways:

1. Direct feedback in form of a choke packet
2. router marks a field in a packet flowing from the sender to receiver to indicate congestion. Takes atleast $\text{RTT}$.


**ATM ABR Congestion Control**

Is a protocol that takes a network-assisted approach toward congestion control.

ATM takes a virtual-circuit oprient approach towards 
packet-switching. Each switch maintain state about the source-to-destination VC. This allows a switch to track the behavior of individual senders and to take source-specific congestion control actions.

The data packets are interspersed with $\texttt{RM}$ (resource-management) packets. These packets are used to convey congestion related information. 

When a $\texttt{RM}$ packet arrives at a destination is will be sent back to the sender. It is also possible for a switch to generate an $\texttt{RM}$ packet and send this to the source. Thus, $\texttt{RM}$ packet can be used to provide both direct feedback and feedback via the receiver.

ABR provides three mechanisms for signalling congestion-related information from the switches to the receiver:

1. $\texttt{RM}$ packets have a $\texttt{CI}$ and $\texttt{NI}$ bits:
    1. $\texttt{CI}$ - Congestion Indication set to $1$ under severe congestion
    2. $\texttt{NI}$ - No Increase set to $1$ under mild congestion
2. $\texttt{EFCI}$ - 
    
    Each data packet has a $\texttt{EFCI}$ - Explicit Forward Congestion Information bit which is set by a congested switch. 
    
    When such a datapacket arrives at a destination, it sets the $\texttt{CI}$ bit and send the $\texttt{RM}$ to the source.
    
3. $\texttt{ER}$ - Explicit Rate Setting - 
    
    2-Byte field in $\texttt{RM}$ packet.
    
    Congestion switch may lower the value in a passing $\texttt{RM}$ packet
