RTP runs in end-systems. 

### RTP QoS:
- Protocol does not provide any QoS guarantees
- RTP encapsulation is only seen at the end systems. Routers provide best-effort-service making no special effort


### RTP on top of UDP
- RTP packets are encapsulated in UDP segments
- RTP libraries provide transport-layer interface that extends UDP
	- Port numbers, IP addresses
	- Payload type identification
	- Packet sequence numbering
	- time stamping
- Application collects encoded data in chunks, ex: every 20ms 160B in a chunk
- Audio chunk + RTP Header form RTP packet, encapsulated in UDP segment
- RTP header indicates type of audio encoding in each packet
- Header also contains sequence number and time stamps

### RTP Header:
![[RTP-header.svg]]
1. Payload Type (7b):
	- Indicates type of encoding currently being used. If sender changes encoding during call, sender informs receiver via payload-type field
2. Sequence # (16b):
	- Increment by one for each RTP packet sent
	- Detect packet loss, restore packet sequence
3. Timestemp (32b):
	- Sampling instant of the first byte in the packet


### RTCP - Real time control protocol:
- Works in conjunctions with RTP
- Each participant in RTP session periodically sends RTCP control packets to all other participants
- Each RTCP packet contains sender and/or receiver reports
- Feedback used to control performance

Each RTP session:
	- Typically a single multicast address
	- All RTP/RTCP packet belonging to the session use multicast address
- RTP and RTCP packets are distinguished via distinct port numbers

