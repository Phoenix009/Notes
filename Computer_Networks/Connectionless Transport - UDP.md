
UDP does just about as little as a transport protocol can do. Aside from the multiplexing/demultiplexing function and some light error checking, it adds nothing to IP.

UDP takes messages from the application process, attaches source and destination port number fields for the multiplexing/demultiplexing service, adds two other small fields and passes the resulting segment to the network layer.

With UDP there is no handshaking between sending and receiving transport layer entities before sending a segment. For this reason, UDP is said to be connectionless.

Many applications are better suited for using UDP for the following reasons:
1. ==Finer application level control== over what data is sent and when
2. ==No connection establishment==: UDP does not introduce any delay to establish a connection. Primary reason why DNS uses UDP over TCP.
3. ==No connection state==: UDP does not maintain connection state. For this reason, a server can support many more active clients when the application runs over UDP rather than TCP.
4. ==Small Packet Overhead==: UDP segments have only 8 Byte overhead.

It is possible for an application to have reliable data transfer when using UDP. This can be done if reliability is built into the application itself. Such an application can communicate reliably without being subjected to the transmission-rate constraints of TCP.

### Segment Structure:

![[udp_segment.png]]

The application data occupies the data field of the UDP segment.

The UDP header has only four fields
1. Port numbers allow multiplexing and demultiplexing.
2. length field specifies the number of bytes in the UDP segment (header + data)
3. Checksum is used to check whether errors have been introduced in the segment.


### UDP Checksum:

UDP checksum provides error detection. 

UDP at the sender side performs the 1’s complement of the sum of all 16-bit words in the segment, with any overflow encountered during the sum being discarded.

At the receiver, all 16-bit words are added, including the checksum. If no errors are introduced into the packet then result is all 1’s. If one of the bits is a 0, then we know that errors have been introduced.

==Although UDP provides error checking, it does not do anything to recover from an error.==