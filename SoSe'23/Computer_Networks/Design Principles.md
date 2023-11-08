### Goals:
- Identify, study common architectural components, protocol mechanisms, approaches we find in network architectures

### Design Principles:
- [[State#Separation of control and data|Separation of data and control]]
- [[State#Hard State vs Soft State|Hard State vs Soft State]]
- Randomisation
- Indirection
- Network Virtualisation
- Resource Sharing
- Design for scale

## [[Signalling]]
## [[State]]


## Internet Design Philosophy:
### Survivability:
- Continue to operate even under network failures
	- As long as the network is not partitioned, two endpoints should be able to communicate
- Maintain end-to-end transport state only at endpoints
- Eliminates the problem of handling state inconsistency and performing state restoration
- Stateless network architecture

### Types of Services:
- datagram abstraction
- Lowest common denominator on which services can be built - IP
- Service differentiation was considered

### Variety of networks:
- Internet was very successful to provide this feature
- It requires minimal service from the underlying network. It requires the underlying network to deliver the packets with a "reasonable" probability of success
- No requirement for Reliability and In-order delivery

### Distributed Management
- Administrative Autonomy

### Cost Effectiveness
- Sources of Inefficiency
	- Headers
	- Retransmission
	- Routing
- optimal performance was never a priority for design

### Low cost of attaching a new host:
- Not the best
- Things are improving - DHCP auto configuration

### Accountability:
- Anyone can share the packets in the internet
- NO ACCOUNTABILITY
