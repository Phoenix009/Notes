Typically a host is attached directly to one router, the ==default router== for the host (aka. first-hop router). We refer to the default router of the source host as the source router and of the destination as destination router.

The purpose of a routing algorithm is then simple: given a set of routers, with links connecting the routers, a routing algorithm finds a “good” path from source to destination router.

**Classifying routing algorithms**:

1. Whether they are global or decentralised:
    1. global: 
       computes the least-cost path between a source and destination using complete, global knowledge about the network. These algorithms are often referred to as ==link-state== algorithms
	
    2. decentralised: 
       the calculation of the least-cost path is carried out in an iterative, distributed manner. These algorithms are often referred to as ==distance-vector== algorithms
       
       Each node begins with its local information and iteratively by sharing the information with the neighbouring nodes, a node gradually calculates the least-cost path to other nodes.

2. Whether they are static or dynamic
    1. static algorithms:
        routes change very slowly over time often as a result of human intervention.
        
    2. dynamic algorithms: 
       change the routing as the network traffic loads or topology changes.


#### Link-State Routing Algorithm:
The network topology and all link costs are available as input to the $\texttt{LS}$ algorithm.

This is accomplished by having each node broadcast link-state packets to all other nodes in the network, with each link-state packet containing the identities and costs of its attached links.

The result of the broadcasting is that all nodes have an identical and complete view of the network. Each node can then run the $\texttt{LS}$ algorithm and compute the same set of least-cost paths as every other node. In practice this is accomplished using a ==link-state broadcast algorithm==.

The result of the broadcast is that all nodes have an identical complete view of the network. Each node can then run the LS algorithm and compute the least cost paths from the current node to all other nodes in the network.

##### Problem:
One case to consider is when link costs are equal to the load carried on the link (ex. delay). In this case the link costs may not be symmetric, i.e. $c(x, y) \neq c(y, x)$. 
![[LS-osciallation.png]]
In the above example the paths are oscillating from clockwise paths to counter-clockwise paths after every iteration of shortest paths calculation. To prevent such oscillations one solution would be to ensure that not all routers run the LS algorithm at the same time. 


#### Distance-Vector Routing Algorithm:

The distance vector algorithm is 
1. **Distributed**: Each node receives some information from one or more of its directly attached neighbours, performs a calculation and distributes the result back to its neighbours.
2. **Iterative**: process continues until no more information is exchanged between neighboUrs
3. **Asynchronous**: Does not require all of the nodes to operate in lockstep with each other.

##### Bellman-Ford Equation:

$$
d_{x}(y) = \min_{v}\{c(x, v) + d_{v}(y)\}
$$

After travelling from $x$ to $v$, if we then take the least-cost path from $v$ to $y$, the path cost will be $c(x, v) + d_{v}(y)$.

Since we must begin by travelling to some neighbour $v$, the least cost from $x$ to $y$ is the minimum of the above equation taken over all neighbours.

Bellman-Ford equation has a significant practical importance. In particular, ==the solution to the Bellman-Form equation provides the entries in node $x$’s forwarding table==.

In $\texttt{DV}$ algorithm, each node $x$ maintains the following routing information:

1. For each neighbour $v$, the cost $c(x, v)$ from $x$ to directly attached neighbour $v$
2. Distance vector $D_{x} = [D_{x}(y) \mid \forall y \in N]$
3. The distance vector of each of its neighbours

In the distributed, asynchronous algorithm, from time to time, each node sends a copy of its $\texttt{DV}$ to each of its neighbours. When a node $x$ receives a new distance vector from any of its neighbours $v$, it uses it to update its own distance vector using the Bellman-Form equation.

If its own distance vector gets updated then it will send its updated distance vector to each of its neighbours.

##### Link Cost Changes and Link Failure:
The decreased link cost between nodes propagates quickly through the network. However, when the link cost increases we encounter the ==count-to-infinity== problem.


### Hierarchical routing:
We have studied the network as a collection of interconnected routers. This model and its view of homogeneous set of routers all executing the same routing algorithm is a bit simplistic for at least two reasons:
1. Scale: 
	As the number of routers becomes large, the overhead involved in computing storing and communicating routing information becomes prohibitive
   
2. Administrative Autonomy:
	Ideally, an organisation should be able to run and administer its network as it wishes, while still being able to connect it network to other outside networks.


These problems can be solved by organising routers into ==autonomous systems (AS)==, with each As consisting of a group of routers that are typically under the same administrative control. 

The routing algorithm running within an autonomous system is called ==INTRA-autonomous system routing protocol==. One of the routers in AS has the additional task of forwarding packets to destinations outside AS; these routers are called ==gateway routers==.

How does a router within some AS know how to route a packet to a destination that is outside the AS?

The problem of knowing where to forward the packet becomes significantly more challenging when there are multiple gateway routers. We need to know which destinations are reachable by each ASs reachable by each gateway router. We also need to propagate this information to all routers within the AS. These tasks are handled by the ==INTER-autonomous system routing protocol==. 

Thus, each router receives information from both inter and intra protocols, and uses this information to configure its forwarding table.

Suppose, there is a case where the destination is reachable by two different gateways in the AS. How would then we decide which gateway router to route the packet from? We use ==hot-potato routing==. The packet will be routed from the gateway that has the least-cost path from the source router.


### Routing in the Internet:

#### Intra-AS routing in the Internet:
Intra-AS routing protocols are also known as interior gateway protocols. Two protocols have been used extensively for routing within an AS in the internet: 
- Routing Information Protocol (RIP)
- Open Shortest Path First (OSPF)


##### RIP - Routing Information Protocol:

==RIP is a distance-vector protocol==. The RIP specified in RFC 1058 uses ==hop count as a cost metric==.

In RIP (and also in OSPF) costs are actually from source router to a destination subnet. RIP uses ==the term hop, which is the number of subnets traversed along the shortest path from source router to a destination subnet, including the destination subnet==. 

>[!note]
>The maximum cost of the path is limited to 15. Thus limiting the use of RIP to AS that are fewer than 15 hops in diameter.

The distance vector for any one router is the current estimate of the shortest path distances from the router to the subnets in the AS. In RIP routing updates are called RIP response message/ RIP advertisements.

Each router maintains a RIP table known as the ==routing table== which contains both the routers distance vector and its forwarding table. 

RIP advertisements are sent after every 30s. If a router does not head from neighbour at least once every 180s, that neighbour is considered to be no longer reachable; RIP then updates the routing information and sends advertisements.

>[!note]
> Each advertisement can list only up to 25 destination networks within AS 

A router can also request information about its neighbours cost to a given destination using RIP request message. RIP messages uses UDP. UDP segments are carried between routers in standard IP datagram. 

==RIP uses transport layer protocol UDP on top of network layer protocol IP to implement network layer functionality.==

##### OSPF - Open Shortest Path First

OSPF and IS-IS protocols are typically deployed in top-tier ISPs whereas RIP is deployed in lower tier ISPs and enterprise networks.

OSPF is a link-state protocol. A router constructs a complete topological map (that is, a graph) of the entire autonomous system. The router then locally runs Dijkstra’s shortest-path algorithm to determine a shortest-path tree to all subnets. 

With OSPF, a router broadcasts routing information to all other routers in the AS. A router broadcasts link state information whenever there is a change in a links state information and also periodically once at least once every 30s.

**Neighbour Discovery and Maintenance: HELLO Protocol**
- Ensures bidirectional communication between neighbours
- Ensures that neighbours agree on parameters (HELLO Interval, etc)

How?
- HELLO packets send to a fixed well known multicast address
- Periodic HELLOs

**OSPF Packets**
Five packet types:
1. HELLO
2. Database Description
3. LS Request
4. LS Update
5. LS ACK

**Link State Database**
- Consists of Link State
- LSA: Data unit describing local state of a network/router
- Must be kept synchronised to react to routing failures

**Database Synchronisation**:
All routers need to have identical databases

How to check if the database are consistent?
- Have the same \#LSA entries
- Have the same sum for checksum

Two types of synchronisation:
1. Initial Sync: After HELLO explicitly transfer of the database
2. Continuous Synchronisation: Flooding


Some of the advanced features in OSPF:
1. Security: Exchanges between OSPF routers can be authenticated. Only trusted routers can participate in the OSPF protocol within an AS.
   
2. Multiple same cost paths: When multiple paths to a destination have the same cost, OSPF allows multiple paths to be used. 
   
3. Integrated support for unicast and multicast routing: Multicast OSPF provides simple extension to OSPF providing multicast routing.
   
4. Support for hierarchy within a single routing domain: the most significant advantage in OSPF is the ability ti structure an AS hierarchically. 

#### Inter-AS Routing: BGP

![[bgp.png]]

BGP - Border Gateway Protocol is the de-factor standard inter-AS routing protocol. As an inter-AS routing protocol BGP provides each AS a means to 
1. Obtain subnet reachability information from neighbouring ASs
2. Propagate the reachability information to add routers internal to the AS.
3. Determine "good" routes to subnets based on the reachability information and on AS policy


In BGP, pairs of routers exchange routing information over semipermanent TCP connections. There is typically one such BGP TCP connection for each link that directly connects two routers in two different ASs; There are also semipermanent BGP TCP connections between routers within an AS. 

For each TCP connection, the two routers at the end of the connection are called ==BGP peers==, and the TCP connection along with all the BGP messages sent over the connection is called ==BGP Session==.

a BGP session that spans two ASs is called ==an external BGP (eBGP) session==, and a BGP session between routers in the same AS is called an ==internal BGP (iBGP) session==. 

BGP allows each AS to learn which destinations are reachable via its neighbouring ASs. ==In BGP, destinations are not hosts but instead are CIDRized prefixes, with each prefix representing a subnet or a collection of subnets==.

*How BGP distribute prefix reachability information over the BGP sessions?*
- the eBGP session is used to exchange prefix reachability information between ASs.
- When a gateway router in any AS receives eBGP-learned prefixes, the gateway router uses iBGP sessions to distribute the prefixes to the outer routers in the AS.
- Thus, all the routers in AS learn about the prefixes reachable from some another AS.


##### Path Attributes and BGP Routes:

In BGP, an autonomous system is identified by its ==globally unique autonomous system number (ASN)==. 

When a router advertises a prefix across a BGP session, it includes with the prefix a number of BGP attributes. In BGP jargon, a prefix along with its attributes is called a ==route==.

Two of the important attributes are:

##### AS-PATH:
The attribute contains the ASs through which the advertisement for the prefix has passed. When a prefix is passed into an AS, the AS adds its ASN to the AS-PATH attribute. ==Routers use the AS-PATH attribute to detect and prevent looping advertisements==; specifically, if a router sees that its AS is contained in the path list, it will reject the advertisement.

##### NEXT-HOP:
The NEXT-HOP is the router interface that begins the AS-PATH. It is the IP address of the router interface. In essence the NEXT-HOP attribute is used by routers to properly configure their forwarding tables.

When a gateway router receives a route advertisement, it uses its ==import policy== to decide whether to accept or filter the route and whether to set certain attributes such as the router preference metrics. The import policy may filter a route because the AS may not want to send traffic over one of the ASs in the route’s AS-PATH.
