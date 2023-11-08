- **Unicast**: Communication between a single source and a single destination
- **Multicast**: Communication between a single source and multiple destinations
	- Useful for streaming and conferencing applications
	- Heavily used in IPv6 for signalling
- **Anycast**: Communication between a single source and ANY destination
	- Useful for communication to the closest server of a group of server


## IP Anycast:
For serving content from an anycast IP address we announce the same prefix from multiple locations in BGP.

BGP chooses the location that is the closest. However, many factors influence BGP routing decisions as we have seen in [[Routing Algorithms#BGP Route Selection|BGP Route Selection]]. Chosen anycast replica can change repeatedly. Connectionless service are best suited for IP anycast.


