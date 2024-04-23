The hosts in datacenters, called ==blades== and resembling pizza boxes, are generally commodity hosts that include CPU, memory, and disk storage. 

The hosts are stacked in ==racks==, with each rack typically having 20 to 40 blades. At the top of each rack there is a switch, aptly named the ==Top of Rack (TOR) switch==, that interconnects the hosts in the rack with each other and with other switches in the datacenter.


To handle flows between external clients and internal hosts, the datacenter network includes one or more ==border routers==, connecting the datacenter network to the public Internet.

## Hierarchical Architecture - Fat Tree:
The network is divided into three main layers: the access layer, the aggregation layer, and the core layer.

![[Fat_Tree.png]]

1. **Access Layer**: At the bottom of the fat tree, the access layer ==consists of switches that connect servers, storage systems, and other network devices==. Each device is connected to a switch in this layer. The access layer switches are typically low-cost and have a relatively low number of ports.

2. **Aggregation Layer**: The aggregation layer sits above the access layer and acts as an intermediary between the access and core layers. It consists of switches that ==aggregate the traffic from multiple access layer switches==. These switches have a higher port density than the access layer switches. The aggregation layer helps distribute the traffic across the core layer and provides scalability and fault tolerance.

3. **Core Layer**: The core layer forms the backbone of the fat tree topology. It consists of ==high-performance switches that connect the aggregation switches together==. These switches provide high-bandwidth connectivity and serve as the main path for data transfer within the data center. The core layer switches are typically designed to handle heavy network traffic and minimise latency.

**Properties of Fat Tree Layout**:
- Multiple routes to the core. Once a core is chosen, path to the host is fixed.
- Multiple paths for data traffic, which helps in load balancing and fault tolerance
- Load balancing traffic from hosts. [[^314925|ECMP]] - Equal cost multi-path routing 

**Bisection Bandwidth**:
Sum of the bandwidths of the minimal number of links cut to create the bisection.

**Full Bisection Bandwidth**:
If aggregate bandwidth is equal to the bisection bandwidth

**Oversubscription**:
Aggregate bandwidth from a subnet is greater than the available bandwidth allocated to it.
Ex: 
When 10 hosts are connected to a ToR using 1Gbps link each thus aggregate bandwidth is 10Gbps. However, the link used to connect the ToR to an aggregate switch is just 5Gbps.

**Oversubscription Ratio**:
Ratio of worst case aggregation bandwidth among end hosts to the total bisection bandwidth

**ECMP**
ECMP requires having multiple paths available between a source and a destination. These paths typically have the same routing cost, achieved either through equal-cost links or using techniques like link cost adjustment.

When a packet arrives at a router or switch, ECMP uses a hashing mechanism to determine which path the packet should take. The hashing algorithm often uses information from the packet header, such as source and destination IP addresses, to create a unique identifier. This identifier is then mapped to one of the available paths.
