#### Application Layer:

|Type|Name|Value|
|:--:|--|--|
| `A`|hostname|IPv4 Address|
| `AAAA`|hostname|IPv6 Address|
| `NS` | Domain | hostname of Authoritative DNS Server |
| `CNAME` | alias hostname | Canonical hostname |
| `PTR` | IP Address | hostname |


#### Transport Layer:

- **UDP header**:
	1. $\texttt{Port number}$ allow multiplexing and demultiplexing.
	2. $\texttt{length}$ field specifies the number of bytes in the UDP segment (header + data)
	3. $\texttt{Checksum}$ is used to check whether errors have been introduced in the segment.

- **TCP Header**:
	1. $\texttt{Source Port\#}$, $\texttt{Destination Port\#}$: for multiplexing and demultiplexing
	2. $\texttt{SEQ\#}$, $\texttt{ACK\#}$: for implementing $\texttt{RDT}$
	3. $\texttt{Header length 4b}$: specifies the length of the TCP header in $\texttt{32b}$ words. Header length can be variable due to the $\texttt{Options}$ field.
	4. The $\texttt{flag}$ field contains $\texttt{6b}$.
	    1. $\texttt{ACK}$ the segment contains an ACK
	    2. $\texttt{RST}$, $\texttt{SYN}$ and $\texttt{FIN}$ are used for connection setup and teardown
	    3. $\texttt{PSH}$ indicates the receiver should pass the data to the upper layer immediately.
	    4. $\texttt{URG}$ is used to indicate there is data in the segment that the sending side upper layer has marked as urgent.
	5. $\texttt{Receive Window 16b}$ is used for flow control. indicates the number of bytes the receiver is willing to accept.

- **Timeout**:
	- $\texttt{EstimatedRTT} = (1-\alpha) * \texttt{EstimatedRTT} + \alpha * \texttt{SampleRTT}$
	- $\texttt{DevRTT} = (1-\beta) * \texttt{DevRTT} + \beta * \mid \texttt{SampleRTT}-\texttt{EstimatedRTT} \mid$
	- $\texttt{Timeout} = \texttt{EstimatedRTT} + 4 * \texttt{DevRTT}$

- **Congestion control**
	![[tcp_congestion_control.png]]


#### Network Layer

- **Classful Addressing**:

| CLASS  | \#bits NID  | \#bits HID  | \#IP ADDr   | 1st Octet Range \#N/W   | \#N/Ws      | \#HOSTS per N/W  |
|-|-|-|-|-|-|-|
| A      | 8          | 24         | 2^31       | 0 - 127                | 2^7        | 2^24 - 2        |
| B      | 16         | 16         | 2^30       | 128 - 191              | 2^14       | 2^16 - 2        |
| C      | 24         | 8          | 2^29       | 192 - 223              | 2^21       | 2^8 - 2         |
| D      | 24         | 8          | 2^28       | 224 - 239              | X          | X               |
| E      | 24         | 8          | 2^28       | 240 - 255              | X          | X     << RESERVED FOR SPECIAL PURPOSE |

- **Private IP Address Range**:
	1. $\texttt{10.0.0.0}$ to $\texttt{10.255.255.255}$
	2. $\texttt{172.16.0.0}$  to $\texttt{172.31.255.255}$
	3. $\texttt{192.168.0.0}$ to $\texttt{192.168.255.255}$

- **IPv4 Segment**:
	1. $\texttt{Version\#}$ - $\texttt{4b}$ specify the IP Protocol version
	2. $\texttt{Header Length}$ - $\texttt{4b}$ Header length in $\texttt{Bytes}$ is variable due to $\texttt{Options}$
	3. $\texttt{Type of Service}$ - $\texttt{4b}$ allows different types of IP datagrams (requiring low delay, high throughput) to be distinguished from each other
	4. $\texttt{Datagram Length}$ - $\texttt{16b}$  is the total length of the IP Datagram ($\texttt{header + data}$). Imposes an upper bound to the datagram length ($\texttt{65535B}$).
	5. $\texttt{Identifier, FLG, Fragmentation Offset}$ - These fields are related to IP Fragmentation
	6. $\texttt{TTL}$ - $\texttt{8b}$ used to ensure that datagrams do not circulate forever in the network. The field is decremented by each router. If $\texttt{TTL == 0}$ then drop then datagram.
	7. $\texttt{Upper Layer Protocol}$ - Value indicates the specific transport layer protocol the data should be passed
	8. $\texttt{Header Checksum}$ - Helps in detecting error bits in a received IP datagram
	    Router computes the header checksum for each received IP dataframe and detects an error condition.
	    Drop the datagram for which error is detected
	    Checksum must be recomputed and stored again at each router, as $\texttt{TTL}$ field and $\texttt{Options}$ may change
	9. $\texttt{SRC and DEST Address}$
	10. $\texttt{Options}$ - Allows IP header to be extended
	11. $\texttt{DATA}$

- **IPv6 Segment**:
	1. $\texttt{Version}$: $\texttt{4b}$ field identifies the $\texttt{IP}$ version.
	2. $\texttt{Traffic Class}$: $\texttt{8b}$ field is similar in spirit to the $\texttt{TOS}$
	3. $\texttt{Flow Label}$: $\texttt{20b}$ field is used to identify the flow of the datagram
	4. $\texttt{Payload Length}$: $\texttt{16b}$ value is treated as unsigned integer giving the number of bytes in the $\texttt{IPv6}$ datagram following the fixed $\texttt{40B}$ header.
	5. $\texttt{Next Header}$: field is similar in spirit to the $\texttt{Protocol}$ field
	6. $\texttt{Hop Limit}$: Similar to $\texttt{TTL}$ field
	7. $\texttt{SRC and DEST Address}$: various formats of $\texttt{IPv6}$ address
	8. $\texttt{Data}$


- **DHCP**
	1. $\texttt{DHCP}$ server discovery: Done using $\texttt{DHCP discover message}$, broadcasted ($\texttt{255.255.255.255}$) by the client.
	2. $\texttt{DHCP}$  server offer(s): A $\texttt{DHCP}$ server responds to the client with a $\texttt{DHCP offer message}$, which is also broadcasted on the subnet.
	3. $\texttt{DHCP}$ Request: client responds a selected offer with a $\texttt{DHCP request message}$, echoing back the configuration parameters
	4. $\texttt{DHCP ACK}$: The server responds with a $\texttt{DHCP ACK}$, confirming the parameters.



Steps in adding an outside-AS destination in a router’s for- warding table, Pg410
Add flowchart for determining the best path for BGP [[Routing Algorithms#BGP Route Selection|BGP Route Selection]]

| | Own routes| Customers routes| Siblings routes | Providers Routes | Peers routes|
|--|--|--|--|--|--|
| Exporting to Provider|Y|Y|Y|N|N|
| Exporting to Customer|Y|Y|Y|Y|Y|
| Exporting to Peer|Y|Y|Y|N|N|


#### Link Layer
- **Multiple Access Protocols**:
	- ALOHA: $0.18 \times B$
	- Slotted ALOHA: $0.37 \times B$
	- CSMA/CD: $\frac{1}{1 + 5t_{prop} / t_{trans}}$

- **Ethernet Frame Structure**:
	1. $\texttt{Data (46-1500B)}$ : $\texttt{MTU}$ - Maximum Transmission Unit for ethernet is $\texttt{1500B}$
	2. $\texttt{Destination Address (6B)}$: Contains the MAC address of the destination adapter
	3. $\texttt{Source Address (6B)}$: Contains the MAC address of the adapter that transmits the frame into the LAN
	4. $\texttt{Type (2B)}$: The type field permits ethernet to multiplex the network layer protocols.
	5. $\texttt{CRC}$: Cyclic Redundancy Check is used for error detection
	6. $\texttt{Preamble (8B)}$: The ethernet frame begins with an $\texttt{8B}$ preamble field.




#### Multimedia Networking
- **DASH Working**:
	- The client first requests the manifest file and learns about the various versions. 
	- The client then selects one chunk at a time by specifying a URL and a byte range in an HTTP GET request message for each chunk. 
	- While downloading chunks, the client also measures the received bandwidth and runs a ==rate determination algorithm== to select the chunk to request next.

- **VOIP - Removing Jitter**:
	- $p$ - playtime, $t$ - time of generating, $q$ - fixed delay usually $400ms$
	- Fixed Playout Delay: $p = t + q$ 
	- Adaptive Playout Delay: For each talk spurt: $p_i = t_i +d_i +Kv_i$


#### Datacenter Networking
- Bisection Bandwidth
- Full Bisection Bandwidth
- Over subscription Ratio