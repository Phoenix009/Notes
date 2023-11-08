## Design Goals:
- Accommodate heterogeneous receivers
- Accommodate different applications with different resource requirements
- Make multicast a first class service, with adaptation to multicast group membership
- Leverage existing multicast/unicast routing with adaptation to changes in underlying unicast, multicast routes
- Control protocol overhead to grow linearly as # receivers
- modular design for heterogeneous underlying technologies

## Overview of Operation:
### 1. Senders and receivers join a multicast group
- Done outside of RSVP
- Senders need not join group

### 2. Sender-to-Network signalling:
- **Path Message**: Communicates sender info, and reverse-path-to-sender routing info
- Path teardown: delete senders path state from routers

### 3. Receiver-to-Network signalling:
- **Reservation Message**: Reservations flow upstream from receiver to sender, reserving resources, creating additional receiver related state at routers
- Reservation teardown: remove receiver reservations

### 4. Network-to-End system signalling:
- Path Error
- Reservation Error

**RSVP uses soft-state**:
- Senders periodically resend path messages to refresh state
- Receivers periodically resent reservations messages to refresh state
- Path and reserve messages have TTL field, specifying refresh intervals


