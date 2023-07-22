## Separation of control and data

*Why?*
- Allows concurrent control and data flow
- simplifies processing of data/control streams
- Provide QoS appropriate for control/data streams

*Why not?*
- Separate channels complicate management, increases resource requirements
- can increase latency

## Maintaining network state
#definition **State**: Information stored in network nodes by network protocols
- Updated when network "conditions" change
- Stored in multiple nodes
- Often associated with end-systems generated call or sessions

In this context:
- **Sender**: Node that generates signalling messages to install, keep-alive, remove state from other nodes
- **Receiver**: Node that creates, maintains, removes state based on signalling messages received from sender


## Hard State vs Soft State
### Hard State:
- State installed by receiver via ==setup messages== from sender
- State removed by receiver via ==teardown message== from sender

### Soft State:
- State installed by receiver via setup message from sender
- State removed by receiver via timeout
- Sender sends periodic refresh message indicating receiver should continue to maintain state

#### Allows "Easy" handling of changes:
- No need for network to signal failure/change to end system
- Less signalling than hard-state from network to end system
- More signalling than hard-state from end-system to network for refreshes

*How to set the refresh/timeout timers?*
- State timeout interval = n * refresh-interval
- the value of n determines the amount of signalling traffic
	- Small timers: Fast response to changes, more signalling
	- Long timers: Slow response to changes, less signalling

