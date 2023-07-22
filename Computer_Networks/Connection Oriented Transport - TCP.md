TCP is said to be a ==connection-oriented== because before one application process can begin to send data to another, the two processes must first establish a connection using a “handshake”. 

Provides a ==full duplex communication== that is always point-to-point, ==multicasting is not possible with TCP==.

The client first sends a special TCP segment; the server responds with a special TCP segment; and finally the client responds again with a third special segment.

The first two segments carry no payload, the third of these segments may carry payload.

Since three segments are sent between client and host for connection establishment, this is called ==three-way-handshake==.

Once a TCP connection is established, the two application processes can send data to each other.

### TCP Segment Structure:
![TCP Segment Structure](file:///Users/phoenix/Documents/Obsidian%20Vault/Computer_Networks/images/tcp_segment.png)

The header includes the following fields:

1. `Source Port#`, `Destination Port#`: for multiplexing and demultiplexing
2. `SEQ#`, `ACK#`: for implementing $\texttt{RDT}$
3. 4b `Header length`: specifies the length of the TCP header in `32b` words. Header length can be variable due to the `options` field.
4. The `flag` field contains 6b.
    1. `ACK` the segment contains an ACK
    2. `RST`, `SYN` and `FIN` are used for connection setup and teardown
    3. `PSH` indicates the receiver should pass the data to the upper layer immediately.
    4. `URG` is used to indicate there is data in the segment that the sending side upper layer has marked as urgent.
5. 16b `Receive Window` is used for flow control. indicates the number of bytes the receiver is willing to accept.

#### SEQ# and ACK#:

Two of the most important fields in the TCP segment header are the `SEQ#` and `ACK#`. These fields are critical part of TCPs RDT.

TCPs `SEQ#` are over the stream of transmitted bytes are not over the transmitted segments. The `SEQ#` for a segment is therefore the byte-stream number of the first byte in the segment.

For the `ACK#`, the `ACK#` that a receiver puts in its segment is the `SEQ#` of the next byte the receiver is expecting from sender.

==TCP provides cumulative `ACK`== and acknowledges up to first missing byte in the stream.


### RTT estimation and timeout:

the most obvious question is the length of the timeout intervals. Clearly, ==the timeout should be larger than the connections RTT==. 


#### Estimating RTT:

$\texttt{SampleRTT}$ for a segment is the amount of time when the segment is send till when an acknowledgement for the segment is received.

$\texttt{SampleRTT}$ values will fluctuate for segment to segment due to congestion in the routers and to the varying load on the hosts.

It is therefore ==natural to take an average of the $\texttt{SampleRTT}$ values==. TCP maintains an average called $\texttt{EstimatedRTT}$ 

$$
\texttt{EstimatedRTT} = (1-\alpha) * \texttt{EstimatedRTT} + \alpha * \texttt{SampleRTT}
$$

In addition to having an estimate of the $\texttt{RTT}$, it is also valuable to have a measure of the variability of $\texttt{RTT}$.

$$
\texttt{DevRTT} = (1-\beta) * \texttt{DevRTT} + \beta * |\texttt{SampleRTT}-\texttt{EstimatedRTT}|
$$


#### Setting and Managing the timeout:
$$
\texttt{Timeout} = \texttt{EstimatedRTT} + 
4 * \texttt{DevRTT}
$$

An initial timeout value of $1s$ is recommended. Also, when a timeout occurs the value of the timeout is doubled. However, as soon as segment is received and $\texttt{EstimatedRTT}$ is updated, the $\texttt{Timeout}$ is again computed.

A few modifications that most TCP implementations employ:

1. *Doubling Timeout Interval*:
    Each time TCP retransmits, it sets the next timeout interval to twice the previous value, rather than deriving it from the last $\texttt{EstimatedRTT}$ and $\texttt{DevRTT}$.
    
    Thus, the intervals grow exponentially after each retransmission. However, whenever the timer is started after either of the two other events(data received from upper layer, $\texttt{ACK}$ received), the $\texttt{Timeout}$ is derived from the $\texttt{EstimatedRTT}$ and $\texttt{DevRTT}$.
    
    This ==modification provides a limited form of congestion control==.

2. *Fast Retransmit*:
    One of the problems with timeout-triggered retransmission is that the timeout period can be relatively long. Fortunately, the sender can often detect packet loss well before the timeout event occurs by noting duplicate $\texttt{ACK}s$
    
    Because a sender often sends a large number of segments back to back, if one segment is lost there will likely be many back to back duplicate $\texttt{ACK}$.
    
    In case three duplicate $\texttt{ACK}s$ are received, the TCP sender performs a fast retransmit - retransmitting the missing segment before the segments timer expires.


### GBN or SR?:

TCP sender need only maintain the smallest $\texttt{SEQ\#}$ of a transmitted but unacknowledged byte ($\texttt{SendBase}$) and the sequence number of the next byte to be sent($\texttt{NextSeqNum}$). In this sense the TCP looks a lot like GBN.

A proposed modification to TCP, the so called selective acknowledgement, allows TCP receiver to acknowledge out of order segments selectively rather than just cumulatively.

When combined with selective retransmission, TCP looks a lot like SR.

Thus, TCPs error-recovery mechanism is probably best categorised as a hybrid of GBN and SR protocols.

### Flow Control:

Hosts on each side of a TCP connection set aside a receive buffer for the connection. The sender can very easily overflow the connections receive buffer by sending too much data.

TCP provides flow-control service to its applications to eliminate the possibility of the sender overflowing the receivers buffer. It thus matches the rate at which the sender is sending against the rate at which the receiving application is reading the data.

TCP provides flow control by having the ==sender maintain a variable called the receive window==.

![[rcv_window.png]]

Receiver tells the sender how much spare room it has in the connection buffer by placing its $\texttt{rwnd}$ value in the $\texttt{receive\_window}$ field for every segment is sends to sender.

==There is one technical problem with this scheme==. 
Suppose,
1. Receiver $\texttt{rwnd} = 0$ is send to the sender
2. Sender stops sending data to the receiver
3. Receiver application consumes data and spare room is available but there is not way of letting the sender know of this spare room if there is no data to be sent by the receiver to the sender
4. Communication Blocked

To avoid this TCP specification requires the sender to send segments with one byte data when $\texttt{rwnd} = 0$. These segments will be acknowledged by the receiver. Eventually the buffer will have space.


### Connection Management:

![TCP Connection Management](file:///Users/phoenix/Documents/Obsidian%20Vault/Computer_Networks/images/tcp_client_states.png)


#### 3-way handshake steps:

1. The client-side TCP first sends a special $\texttt{SYN}$ segment to the server-side TCP.
    1. $\texttt{SYN = 1}$
    2. $\texttt{SEQ\# = client\_isn}$
2. Server allocates buffer and variables to the connection.
    
    Sends $\texttt{SYN-ACK}$ segment
    
    1. $\texttt{SYN = 1}$
    2. $\texttt{ACK\# = client\_isn + 1}$
    3. $\texttt{SEQ\# = server\_isn}$
3. Client allocates buffers and variables to the connection. 
    
    Sends another $\texttt{ACK}$ segment to the server. May also send data.


#### Connection teardown:

Either of the two processes participating in the TCP connection can end the connection. The resources in the hosts are deallocated.

Assume client TCP wants to end the connection

1. $\texttt{FIN}$ segment is sent to the server, $\texttt{FIN}$ is set to 1
2. Server sends $\texttt{ACK}$ segment and its own $\texttt{FIN}$ segment
3. Client acknowledges the servers $\texttt{FIN}$ segment.

The resources in the two hosts are now deallocated.


### Congestion Control:

TCP must use end-to-end congestion control rather than network-assisted congestion control, since the IP layer provides no explicit feedback to end systems regarding network congestion.

The approach taken by TCP is to have each sender limit the rate at which sends traffic into its connection as a function of perceived network congestion.

1. How Does a TCP sender limit the rate at which it sends traffic?
    
    TCP congestion control mechanism at the sender maintains an addiitional variable the $\texttt{cwnd}$ congestion window. This imposes a constraint on the rate at which a TCP sender can send traffic into the network.
    
    $$
    \texttt{\#UnACK bytes <= min\{cwnd, rwnd\}} 
    $$
    
2. How does a TCP sender perceive that there is congestion in the network?
    
    TCP defines a “Loss” event as the occurence of either a timeout or the receipt of three duplicate $\texttt{ACK}s$. This is taken by the sender to be an indication of congestion.
    

1. How should the TCP sender determine the rate at which it should send?
    1. A Lost segment implies congestion and hence the TCP’s sender rate should be decreased.
    2. an $\texttt{ACK}$ segment indicates that the network has no congestion and the senders rate can be increased.
    3. Bandwidth Probing - TCPs strategy for adjusting its transmission rate is to increase its rate in response to arriving $\texttt{ACK}s$ until a loss event occurs, at which point the transmission rate is decreased.


#### Congestion Control Algorithm

![TCP congestion control](file:///Users/phoenix/Documents/Obsidian%20Vault/Computer_Networks/images/tcp_congestion_control.png)

##### 1. Slow Start:

In the slow start state, the value of $\texttt{cwnd}$ begins at $1 \texttt{MSS}$ and increases by $1 \texttt{MSS}$ every time a transmitted segment is acknowledged.

This process results in a doubling of the sending rate every $\texttt{RTT}$. Thus, TCP send rate starts slow but grown exponentially. 

When should this exponential growth end?

1. Loss event - Timeout
	TCP sender sets $\texttt{cwnd = 1}$ and $\texttt{ssthresh = cwnd/2}$.
	Retransmit missing packet.
	Begin slow start phase again.

2. When $\texttt{cwnd >= ssthresh}$ :
	Slow start ends and TCP transitions into congestion avoidance mode.

3. Loss Event - $3$ duplicate $\texttt{ACK}s$:
	TCP sender sets $\texttt{cwnd' = cwnd/2}$ and $\texttt{ssthresh = cwnd/2}$ 
	Retransmit missing packet.
	Transition to Fast Recovery.


##### 2. Congestion Avoidance:

Rather than doubling the value of $\texttt{cwnd}$ every $\texttt{RTT}$, TCP adopts more conservative approach and increases the value of $\texttt{cwnd}$ by $\texttt{MSS}$ every $\texttt{RTT}$.

When should congestion avoidance’s linear increase end?

1. Loss event - Timeout
	TCP sender sets $\texttt{cwnd = 1}$ and $\texttt{ssthresh = cwnd/2}$
	Retransmit missing segment
	Begin slow start phase again

2. Loss Event - $3$ duplicate $\texttt{ACK}s$:	
	$\texttt{cwnd' = cwnd / 2}$
	$\texttt{ssthresh = cwnd / 2}$
	Enter Fast Recovery

##### 3. Fast Recovery:

Recovery means becoming inactive and not transmitting any new packet for some time.

Since the $\texttt{cwnd}$ was halved when entering the FastRecovery mode there were still packets that were unacknowledged (we received duplicate $\texttt{ACK}s$).

TCP sender becomes inactive until the number of unacknowledged packets become equal to the new $\texttt{cwnd}$.

Once, it equals the $\texttt{cwnd}$ and receives another duplicate $\texttt{ACK}$ it sends a new packet and waits for a new $\texttt{ACK}$. If it does receive a new $\texttt{ACK}$ it transitions to the Congestion Avoidance mode.

If a timeout occurs then goes to the Slow Start phase with regular updates.


the average throughput of a TCP connection is

$$
\texttt{average throughput =} \frac{0.75W}{RTT}
$$

$W$ is the value of the window when a loss event occurs due to 3 duplicate $\texttt{ACK}s$.

Also, 

$$
\texttt{average throughput =} \frac{1.22 \cdot MSS}{RTT \sqrt{L}}
$$

$L$ is the loss rate.



#### Fairness
![tcp fairness](file:///Users/phoenix/Documents/Obsidian%20Vault/Computer_Networks/images/fairness.png)

It has been shown that when multiple connections share a common bottleneck, those sessions with a smaller $\texttt{RTT}$ are able to grab the available bandwidth at that link more quickly as it becomes free and thus will enjoy higher throughput than those connections with larger $\texttt{RTT}$. 

##### Fairness and UDP:

Many multimedia applications, such as internet phone and video conferencing, often do not run over TCP as they do not want their transmission throttled, even if the network is very congested. 

When running over UDP, applications can pump their content at a constant rate and occasionally lose packets rather than reduce their rates to fair levels at times of congestion and not lose any packets.

##### Fairness and parallel TCP:

Even if we force the UDP traffic to behave fairly, the fairness problem would still not be completely solved. This is because there is nothing to stop a TCP-based application from using multiple parallel connections.

When an application uses multiple parallel connections, it gets a larger fraction of the bandwidth in a congested link.