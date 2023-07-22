## Traditional Computer Networks:
### Data Plane:
- Fast but dumb elements of the network. Perform forwarding, filtering, buffering etc.

### Control Plane:
- Consists of distributed and sophisticated algorithms.
- Tracks topology changes, compute routes and installing forwarding rules.
- Slow but smart

### Management Plane:
- Human time scale
- Collect measurements and configure the equipment

| | Data | Control | Management |
|-|:----:|:-------:|:----------:|
|**Time Scale**| Packet(ns)| Event(10ms-1s) | Human (mins - hr)|
| **Tasks** | Forwarding, Buffering, filtering and scheduling| Routing, circuit set-up|Analysis configuration|
|**Location**|Line-card Hardware|Router Software|Humans or scripts|


## SDN:
The idea is to separate the data plane and control plane and provide an open standard API for their communication. Enables the network control to become directly programmable and the underlying infrastructure to be abstraction from applications and network services.

*Why?*
- Closed Hardware/Software stack hurts innovation
- Researchers cannot try their ideas at scale
*Solution:*
- Open Hardware/Software interfaces
- Build a Network Operating System

### Event Driven Paradigm:

| Plane | Actions |
|:-----:|:-------:|
|Data Plane| Switches generate events and send it to the controller|
|Control Plane| Forwards events to one or more applications. Forward response to switches|
| Application Plane | Application subscribes to events. Respond the the events given by the controller |


### SDN Stack:
#### Infrastructure Layer - Data Plane:
- Is made up of networking elements responsible for forwarding and data processing abilities. 

#### Control Layer - Control Plane:
- Is a logically centralised entity in charge of
	- translating the requirements from the SDN application layer to the data layer
	- Providing application layer with an abstraction of the data layer

#### Application Layer:
- Are programs that explicitly, directly and programmatically communicate their network requirements and desired network behaviour to SDN controller.



### Open Flow:
- Protocol that provides access to the forwarding plane of network switches
- Is considered as the enabler of SDN
- Standardised by ONF

Supports three message types:
1. **Controller to switch**: Initiated by the controller and are used to manage switches
2. **Asynchronous**: Initiated by the switch and are used to inform the controller on event and changes on the switch state
3. **Symmetric**: Initiated by either the switch or the controller and sent without solicitation (HELLO, ECHO, etc)

#### Flow Entries:
- Rather than react to every packet from the switch, we can install a flow entry that matches a certain packet headers. 
- Flow entries are installed by the controller and maintained in the flow table

Consists of the following information:
1. **Rule**:
2. **Action**: What to do with the packet:
	1. Forward to port XX
	2. Encapsulate and forward to controller
	3. Drop
	4. ...
3. **Statistics**
4. **Priority**:  to resolve the flow table entry when multiple matches are found
5. **Timeout**

#### Granularity:
1. **Microflow**
	- One flow entry matches one flow
	- Precision oriented
2. **Aggregated Rules**
	- Wildcards
	- One flow entry matches a group of flows

#### Flow insertion approaches
1. **Reactive Flow Insertion**: 
	- First packet of the flow is sent to the controller for which not flow entry is found
	- Controller installs flow entries
	- Subsequent packets match flow entry
	- Setup time overhead, loss of connection between controller/switch affects the network
2. **Proactive Flow insertion**:
	- Controller pre-populates flow entries on the switch
	- Packets that do not match any flow are dropped
3. **Hybrid Flow insertion**:
	- Pre-population
	- Consult controller for packets that do not have a match


### Challenges
1. Ensure that SDN is available, fault tolerant and secure
2. Need to map policies to the low-level API
3. Need to compose modular applications, debug and verify
