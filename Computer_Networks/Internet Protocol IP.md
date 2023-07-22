
Internet addressing and forwarding are important components of the Internet Protocol (IP).


### Internet Protocol IP

Internet addressing and forwarding are important components of the Internet Protocol (IP).

![[netwokr_layer_functions.png]]

The Internet’s network layer has three major components. 

1. IP Protocol
2. Routing Component
3. Facility to report errors in datagrams and respond to requests of certain network layer information.

### Datagram Format:
![[ip4_datagram.png]]

1. $\texttt{}$$\texttt{Version\#}$ - $\texttt{4b}$ specify the IP Protocol version
   
2. $\texttt{Header Length}$ - $\texttt{4b}$ Header length is variable due to $\texttt{Options}$

3. $\texttt{Type of Service}$ - $\texttt{4b}$ allows different types of IP datagrams (requiring low delay, high throughput) to be distinguished from each other

4. $\texttt{Datagram Length}$ - $\texttt{16b}$  is the total length of the IP Datagram ($\texttt{header + data}$). Imposes an upper bound to the datagram length ($\texttt{65535B}$).

5. $\texttt{Identifier, FLG, Fragmentation Offset}$ - These fields are related to IP Fragmentation

6. $\texttt{TTL}$ - $\texttt{8b}$ used to ensure that datagrams do not circulate forever in the network. The field is decremented by each router. If $\texttt{TTL == 0}$ then drop then datagram.

7. $\texttt{Upper Layer Protocol}$ - Value indicates the specific transport layer protocol the data should be passed

8. $\texttt{Header Checksum}$ - Helps in detecting error bits in a received IP datagram
    Router computes the header checksum for each received IP dataframe and detects an error condition.
    
    Drop the datagram for which error is detected
    
    Checksum must be recomputed and stored again at each router, as $\texttt{TTL}$ field and $\texttt{Options}$ may change

9. $\texttt{SRC and DEST Address}$

10. $\texttt{Options}$ - Allows IP header to be extended

11. $\texttt{DATA}$


### IP Fragmentation:

Not all Link Layer protocols can carry network layer packets of the same size.

The maximum amount of data that a link layer frame can carry is called the ==maximum transmission unit $\texttt{MTU}$==  and places a hard limit on the length of an IP datagram.

Problem is that each of the links along the route between the source and destination can have different $\texttt{MTU}$s

The solution is to fragment the data in the IP datagram, encapsulate each of these smaller IP datagrams in a separate link layer frame and send these frames over the outgoing link. Each of these smaller datagrams is referred to as $\texttt{fragment}$.

Reassembling datagrams in the routers would introduce significant complication into the protocol and reduce router performance. $\texttt{IPv4}$ designers thus decided to ==put the job of reassembling data on the end-systems==.

To allow destination hosts to perform these reassembly tasks, the designers of IP put $\texttt{Identification, FLG}$ and $\texttt{Fragmentation Offset}$ fields in the IP datagram header.

When a IP datagram header is created it is stamped with a $\texttt{Identification\#}$. When a router needs to fragment a datagram, each resulting datagram has the same $\texttt{Identification\#}$ of the original datagram.

This helps the destination host to recognise fragments of the bigger original datagram.

The $\texttt{FLG}$ is used to identify if there are any more fragments of the datagram. $\texttt{FLG = 1}$ means that there are succeeding fragments. $\texttt{FLG = 0}$ means that it is the last fragment of the datagram

The $\texttt{Fragmentation Offset}$ field is used for ordering the fragments

### IPv4 Addressing:

An IP Address is a N-bit address

1. The first $\texttt{k}$ bits represent the $\texttt{NetworkID}$ 
2. The remaining $\texttt{32 - k}$ bits represent the $\texttt{HostID}$ in the network


##### Casting:
Ways to send the message is casting

1. $\texttt{Unicast}$: $\texttt{1-to-1}$

2. $\texttt{Broadcast}$: $\texttt{1-to-*}$
    1. $\texttt{Limited Broadcast}$: 
        
        From $\texttt{1}$ host to all other hosts of the its own network
        
        The $\texttt{NetworkID}$ and $\texttt{HostID}$ part is set to $\texttt{1}s$ ($\texttt{255.255.255.255}$)
        
    2. $\texttt{Directed Broadcast}$:
        
        From $\texttt{1}$ host to all other hosts of some other network

3. $\texttt{Multicast}$: Done using Class D Addresses


##### Subnetting:
The process of diving the network into subnetworks

For dividing the network, bits are borrowed from the $\texttt{HostID}$ part

If we want to divide the network into $\texttt{2}$ subnets then $\texttt{1b}$ will be borrowed. If we want to divide the network into $\texttt{4}$ subnets then $\texttt{2b}$ will be borrowed.

Ex:
Consider a $\texttt{Class C}$ Address
$\texttt{NetworkId = 24b; HostID = 8b}$$\texttt{}$
For dividing the network into $\texttt{4}$ subnets
$\texttt{NetworkID = 24b; SubnetID = 2b; HostId = 6b}$

**Subnet Mask**:
$\texttt{32b}$ Mask is used to generate the $\texttt{NetworkID + SubnetID}$ from an IP Address. For the above example the $\texttt{Subnet Mask}$ will be 

$\texttt{(11111111.11111111.11111111.11000000) ==> (255.255.255.192)}$

To get the $\texttt{NetworkID + SubnetID}$ from the $\texttt{IP Address}$ we do
$\texttt{NetworkID + SubnetId = IP Address AND SubnetMask }$

Subnets can be formed inside a subnet leading to ==Variable Subnetting==

In this case the $\texttt{SubnetMask}$ will have more bits


##### CIDR - Classless Inter Domain Routing:

Improved IP Addressing System. makes IP Allocation more effecient.

$$
\texttt{a.b.c.d/n}
$$

$\texttt{a.b.c.d}$ is regular dotted decimal representation; $\texttt{/n}$ is network prefix and represents the $\texttt{\#bits}$ in $\texttt{NetworkID}$.

Ex: for the IP Address $\texttt{20.10.50.0/20}$, the first $\texttt{20b}$ are for $\texttt{NetworkID}$ and remaining $\texttt{32 - 20 = 12b}$ are for $\texttt{HostID}$.

**Rules For a $\texttt{CIDR}$ Block**:

1. All $\texttt{IP Address}'s$ should be contigous
2. Block Size should be a integral power of 2
3. First $\texttt{IP Address}$ should be divisible by the size of the block. So for a block size $\texttt{2}^\texttt{k}$ the first IP address should have least significant $\texttt{k}$ bits 0.

##### Supernetting:
Supernetting is the process of combining smaller networks into a single super network

**Rules for Aggregation**:
1. Networks to be combined should be contiguous
2. Block Size of each Network should be of the same size
3. First $\texttt{NetworkID}$ should be divisible by the total size of the super net

 
### DHCP - Dynamic Host Configuration Protocol

$\texttt{DHCP}$ allows a host to obtain an IP address automatically. It can be configured such that a host receives the same IP address each time it connects to the network, or a host may be assigned a temporary IP address that will be different each time the host connects to the network.

$\texttt{DHCP}$ also allows a host to learn additional information, such as the subnet mask, the address of the first-hop-router aka ==default gateway==, and the address of its local $\texttt{DNS}$ server.

It is a ==client-server protocol==. A client is typically a new arriving host waiting to obtain network configuration information.

The $\texttt{DHCP}$ protocol is a four step process:

1. $\texttt{DHCP}$ server discovery: The first task is to find a $\texttt{DHCP}$ server. Done using $\texttt{DHCP discover message}$, broadcasted ($\texttt{255.255.255.255}$) by the client.
   
2. $\texttt{DHCP}$  server offer(s): A $\texttt{DHCP}$ server receiving a $\texttt{DHCP}$ discover message responds to the client with a $\texttt{DHCP offer message}$, which is also broadcasted on the subnet.
	Each server offer message contains:
	1. $\texttt{transactionID}$ of the received discover message
	2. $\texttt{IP Address}$
	3. $\texttt{Network Mask}$
	4. $\texttt{IP Address Lease Time}$ - Amount of time for which the address is valid.
   
3. $\texttt{DHCP}$ Request: The client will choose form among one or more server offers and respond to its selected offer with a $\texttt{DHCP request message}$, echoing back the configuration parameters
   
4. $\texttt{DHCP ACK}$: The server responds to the $\texttt{DHCP request message}$ with a $\texttt{DHCP ACK}$, confirming the requested parameters.

### NAT - Network Address Translation:

#### Private Addressing:
If we want to design a network for an institution such that messages can flow within the network but not outside, in such cases we use Private Addressing

Private IP Address Range:
1. $\texttt{10.0.0.0}$ to $\texttt{10.255.255.255}$
2. $\texttt{172.16.0.0}$  to $\texttt{172.31.255.255}$
3. $\texttt{192.168.0.0}$ to $\texttt{192.168.255.255}$       

Private addresses only have meaning within a given network, *how is addressing handled when packets are sent to or received from the global internet?* The answer lies in $\texttt{NAT}$.

$\texttt{NAT}$ router behaves to the outside world as a single devide with a single $\texttt{}$$\texttt{IP Address}$. In essence, the $\texttt{NAT}$ router is hiding the details of the network from the outside world.

![[NAT_translation.png]]

$\texttt{NAT}$ router maintains a $\texttt{NAT Translation Table}$.

Suppose a host ($\texttt{10.0.0.1}$) makes a requests a WebPage on WebServer ($\texttt{Port\# = 80}$) with IP Address $\texttt{128.119.40.186}$

The host chooses a random port, suppose $\texttt{Port\# = 3345}$, and sends the datagram into the LAN.

The $\texttt{NAT}$ router receives the datagram, generates a new $\texttt{Port\#}$ say $\texttt{5001}$, replaces the $\texttt{Port\#}$ and the source IP address with its WAN-side IP address $\texttt{(138.76.29.7)}$.

$\texttt{NAT}$ also adds an entry in its translation table.
$\texttt{Response on (138.76.29.7:5001), Forward to (10.0.0.1:3345)}$

When the response datagram arrives, $\texttt{NAT}$ router uses the $\texttt{Destination IP Address}$ and $\texttt{Port\#}$ to index the translation table to obtain the appropriate $\texttt{IP Address}$ and destination $\texttt{Port\#}$.


### ICMP - Internet Control Message Protocol:

$\texttt{ICMP}$ is used by hosts and routers to communicate network-layer information to each other. The most typical use of $\texttt{ICMP}$ is for error reporting.

$\texttt{ICMP}$ is often considered part of the $\texttt{IP}$ but architecturally lies above $\texttt{IP}$, as $\texttt{ICMP}$ messages are carried inside $\texttt{IP}$ datagrams

$\texttt{ICMP}$ messages have a $\texttt{type}$ and a $\texttt{code}$ field, and contain the $\texttt{header}$ and the first $\texttt{8B}$ of the $\texttt{IP}$ datagram for which the $\texttt{ICMP}$ message is being sent.

#### PING Program:
1. source host sends an ECHO Request $\texttt{ICMP: Type=8; Code=0}$ message to the destination host
2. destination host responds with ECHO REPLY $\texttt{ICMP: Type=0; Code=0}$ message to the source host.

#### `traceroute` Program:

![[traceroute.png]]

Used to determine the name and the addresses of the routers between source and destination hosts

Implemented using $\texttt{ICMP}$ messages.

1. Source sends series of ordinary $\texttt{IP}$ datagrams to destination with increasing order of the $\texttt{TTL}$ field.

$$\texttt{TTL = 0, 1, 2,...}$$

1. When the $n^{\text{th}}$ datagram arrives at the $n^{\text{th}}$ router, $\texttt{TTL=0}$, thus the datagram has to be discarded and a $\texttt{ICMP}$ message is send back to the source with network information.

Thus information is incrementally collected about each router


### IPv6

A prime motivation for this effort was the realisation that the $\texttt{32b}$ IP address space was beginning to be used up. The designers of $\texttt{IPv6}$ took this oppotunity to tweak and augment other aspects of the $\texttt{IPv4}$.

The most important changes introduced in $\texttt{IPv6}$ are evident in the datagram format:

1. Expanded Addressing Capabilities:
    
    $\texttt{IP}$ address size is not $\texttt{128b}$. This ensures that the world won’t run out of $\texttt{IP}$ addresses.
    
    In addition to the unicast and multicast addresses, $\texttt{IPv6}$ has introduced anycast address, which allows a datagram to be delivered to any one of a group of hosts.
    
2. Streamlined $\texttt{40B}$ header:
    
    The $\texttt{40B}$ fixed length header allows for faster processing of the $\texttt{IP}$ datagram.
    
3. Flow labeling and priority:
    
    $\texttt{IPv6}$ has an elusive definition of a flow and states that this allows labelling of packets for which sender request special handling requirements.
    
    It is possible that the traffic carried by a high-priority user (premium subscriber) might also be treated as a flow.


$\texttt{IPv6}$ Datagram Format:

![[ip6_datagram.png]]

1. $\texttt{Version}$: $\texttt{4b}$ field identifies the $\texttt{IP}$ version.
2. $\texttt{Traffic Class}$: $\texttt{8b}$ field is similar in spirit to the $\texttt{TOS}$
3. $\texttt{Flow Label}$: $\texttt{20b}$ field is used to identify the flow of the datagram
4. $\texttt{Payload Length}$: $\texttt{16b}$ value is treated as unsigned integer giving the number of bytes in the $\texttt{IPv6}$ datagram following the fixed $\texttt{40B}$ header.
5. $\texttt{Next Header}$: field is similar in spirit to the $\texttt{Protocol}$ field
6. $\texttt{Hop Limit}$: Similar to $\texttt{TTL}$ field
7. $\texttt{SRC and DEST Address}$: various formats of $\texttt{IPv6}$ address
8. $\texttt{Data}$

#### Fragmentation and Reassembly:

$\texttt{IPv6}$ does not allow for fragmentation and reassembly. If an $\texttt{IPv6}$ datagram received by a router is too large to be forwarded, the router simply drops the datagram and sends a $\texttt{ICMP}$ error message.

#### Header Checksum:

Because transport layer and link layer protocols in the internet layers perform checksumming, the designers of $\texttt{IP}$ felt this was redundant.

#### Transitioning from IPv4 to IPv6:

$\texttt{IPv6}$ systems can be made backward compatible, so they can send, route and receive $\texttt{IPv4}$ datagrams however, already deployed $\texttt{IPv4}$ systems are not capable of handling $\texttt{IPv6}$ datagrams.

Probably the most straightforward way to introduce $\texttt{IPv6}$ capable nodes is a ==dual-stack approach== where, $\texttt{IPv6}$ nodes also have a complete $\texttt{IPv4}$ implementation. Such a node can operate on both $\texttt{IPv4 and IPv6}$ datagrams.

![[ip_tunnelling.png]]

While using dual-stack approach, it is possible that two $\texttt{IPv6}$-capable nodes would have to communicate using $\texttt{IPv4}$. This happens when the two nodes are intervened by $\texttt{IPv4}$ nodes.

An alternative to the dual-stack approach is called ==tunnelling==. We refer to the intervening set of $\texttt{IPv4}$ nodes between two $\texttt{IPv6}$ routers as a tunnel.

Tunnelling works as follows:

1. The $\texttt{IPv6}$ sending node takes the entire $\texttt{IPv6}$ and puts it in the data field of the $\texttt{IPv4}$ datagram.
2. The $\texttt{IPv6}$ node on the receiving side of the tunnel extracts the $\texttt{IPv6}$ datagram and then routes it if it had received the $\texttt{IPv6}$ datagram from a directly connected $\texttt{IPv6}$ neighbour. 