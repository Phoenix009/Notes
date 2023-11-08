$\texttt{RDT3.0}$ is a functionally correct protocol but has performance issues. $\texttt{RDT3.0}$’s performance problem is the fact that it is a stop and wait protocol.

The solution of this particular performance problem is simple: the sender is allowed to send multiple packets without waiting for acknowledgments. This technique is known as pipelining.

Pipelining has the following consequences:

1. The range of sequence numbers must be increased for uniquely assigning sequence numbers to unacknowledged packets in transit.
2. Sender and Receiver sides have to buffer more than one packet. sender has to buffer transmitted packets that are not yet acknowledged.
3. Range of sequence numebrs and buffering requirements depend on the manner in which a data transfer protocol responds to lost, corrupted and delayed packets.

**Go-Back-N (GBN):**
In GBN protocol, the sender is allowed to transmit multiple packets without waiting for an acknowledgement, but is constrained to have no more than some maximum allowable number $N$, of unacknowledged packets in the pipeline.

$N$ is often referred to as the window size and the GBN protocol itself is a sliding window protocol.

The GBN sender must respond to three types of events:

1. Invocation from above:
	
	Check to see if the window is full. i.e if there are $N$ outstanding packets. if not full, create a packet and send. if full return the data back to the upper layer.
	
2. Receipt of an $\texttt{ACK}$:
	
	An acknowledgement for a packet with sequence number $n$ will be taken as a cumulative $\texttt{ACK}$, indicating that all packets with a sequence number up to and including $n$ have been correctly received.
	
3. Timeout event:
	
	Uses only a single timer, which can be thought of as a timer for the oldest transmitted but not yet acknowledged packet.
	
	If $\texttt{ACK}$ is received but there are still not acknowledged packets then the timer is restarted. else timer is stopped.
	

The GBN receiver is also simple.

If a packet with sequence number $n$ is received correctly and in order, the receiver sends an $\texttt{ACK}$. In all other cases, the receiver discards the packet and resends and $\texttt{ACK}$ for the most recently received in-order packet.
    
2. **Selective Repeat (SR):**
    
    In particular, when the window size and bandwidth delay products are both large, many packets can be in the pipeline. A single packet error can thus cause the. GBN to retransmit a large number of packets.
    
    A selective repeat protocol avoids unnecessary retransmissions by having the sender retransmit only those packets that it suspects were received in error.
    
    This requires that the receiver individually acknowledges correctly received packets.
    
    SR Sender:
    
    1. Data received from above, checks the next sequence number. If is within the senders window, the data is packetized and sent.
    2. Timeout, each pakcet must have its own timer, since only single pakcet willbe transmitted on timeout.
    3. $\texttt{ACK}$ received, Sender marks the packet as received, provided its in the window. If the packets sequence number is equal to the send base then the window is shifted ahead to the unacknowledged packet with the smallest sequence number.
    
    SR Receiver:
    
    1. Packet with sequence number in the receiver window is correctly received. $\texttt{ACK}$ packet is sent. If the packet was received for the first time then it is buffered. If the sequence number is equal to the rcv_base then all the consecutive packets from rcv-base are sent to the upper layer and the window is shifted.
    2. Packet which are already received and acknowledged are received again are to be acknowledged again.
    
    One thing to understand is that the sender and receiver will not always have an identical view of what has been received correctly and what has not. the lack of synchronization between sender and receiver windows has important consequences when we are faced with the reality of a finite range of sequence numbers.
    
    ![Untitled](Pipelined%20RDT%20fcfbb7b29c424bb58fac15c2e6e699e3/Untitled.png)
    
    In the above case (a) and (b), the sender has no way of distinguishing between the retransmission of the first packet from the original transmission of the fifth packet.
    
    **For this reason the window size must be less than or equal to half the size of the sequence number space for SR protocols.**
    
    The approach taken in practice is to ensure that a sequence number $x$ is not reused until the sender is sure that any previously sent packet with sequence number $x$ is no longer in the network.
    
    This is done by assuming that a packet cannot live in the network for longer than some fixed maximum amount of time.