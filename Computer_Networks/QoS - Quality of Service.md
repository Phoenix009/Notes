## Network support for Multimedia
![[qos.svg]]


### Dimensioning Best Effort Networks:
**Approach**: Deploy enough link capacity so that congestion does not occur, and multimedia traffic flows without delay or loss
- low complexity of network mechanisms
- Challenge: *How much bandwidth is enough?*

#### Providing multiple classes of service:
The earlier approach is a "one-size-fits-all" approach.

Alternative: provide multiple classes of services
- Partition traffic into classes
- Network treats different classes of traffic differently

- ==Packet marking needed== for router to distinguish between different classes, and new router policy to treat packets accordingly.
- ==Traffic Policing==: Force a source to adhere to bandwidth allocations.

#### Policing Mechanism:
**Goal**: Limit traffic to not exceed declared parameters

Three commonly used criteria:
1. **Average rate**: How many packets can be sent per unit time
2. **Peak rate:** What is the max rate at which the flow can send packets
3. **Burst Size**: Max. number of packets sent consecutively

##### Token Bucket:
Limit input to specified ==burst size== and ==average rate==.

*How?*
- Bucket can hold `b` tokens
- Tokens are generated at rate `r` tokens/sec
- Over interval of length `t`:  $$\text{\#packets admitted} \le rt + b $$

### Differentiate Services:
- Want qualitative service classes

Scalability: simple functions in network core; relatively complex functions at edge