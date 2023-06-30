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
