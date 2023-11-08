
### 1. RDT 1.0: RDT over a perfectly reliable channel.

We first consider the simplest case, in which the underlying channel is completely reliable.

![[rdt1.0.png]]

All packet flow is from sender to receiver, with a perfectly reliable channel there is no need for the receiver side to provide any feedback to the sender since nothing can go wrong.

We have assumed that the receiver is able to receive as fast as sender can send. Thus, no need for receiver to ask the sender to slow down.


### RDT 2.0: RDT over a channel with bit errors.

A more realistic model of the underlying channel is one in which bits in a packet may be corrupted.

We’ll continue to assume that all transmitted packets are received in the order in which they were sent.

We will use positive and negative acknowledgements that will allow the receiver to let the sender know what has been received correctly, and what has been received in error and needs retransmission.

Reliable data transfer protocol based on such retransmission are known as ARQ (Automatic Repeat Request).

![[rdt2.0.png]]


Three additional protocol capabilities are required in ARQ protocols to handle the presence of bit errors:

1. **Error Detection**: a mechanism to allow the receiver to detect when bit errors have occured
2. **Receiver feedback**: Our $\texttt{RDT 2.0}$ protocol will send $\texttt{ACK}$ and $\texttt{NAK}$ packets from the receiver to the sender.
3. **Retransmission**: A packet that is received in error will be retransmitted by the sender.

Protocol $\texttt{RDT 2.0}$ may look as if it works but, unfortunately, has a fatal flaw. In particular we have not accounted for the possibility that the $\texttt{ACK}$ or $\texttt{NAK}$ packet could be corrupted. In this case the sender has no way of knowing whether or not the receiver has correctly received the last piece of data.

#### RDT2.1:

How should the protocol recover from errors in acknowledgements?

An alternative is to add error correction that allows the sender to detect as well as correct the bit errors. This solves the immediate problem for a channel that can corrupt packets but not lose them

==A second approach is for the sender to simply resent the current data packet when it receives a garbled acknowledgement.==

The fundamental difficulty with duplicate packets is that the receiver does not know whether the packet contains new date or is it a retransmission.

#### RDT2.2:

A simple solution to this new problem is to add a new field to the data packet and have the sender number its data packet by putting a sequence number field. 


### RDT3.0: RDT over a Lossy channel with Bit errors:

Now in addition to corrupting bits, the underlying channel can lost packets as well, a not uncommon event.

Two additional concerns musst be addressed by the protocol:

1. how to detect packet loss
2. what to do when packet loss occurs

We put the burden on detecting and recovering from lost packets to the sender. If the seder is waiting long enough that it is certain that a packet has been lost, it can simply retransmit the data packet,

How long must the sender wait to be certain that something has been lost? The sender must atleast wait as long as $\texttt{RTT}$.

The approach thus adopted in practice is for the sender to judiciously choose a time value such that packet loss is likely, although not guaranteed. If an $\texttt{ACK}$ is not received withing this time, the packet is retransmitted.

This leads to duplicate packets, which can be handled by the sequence numbers.

Implementing a time-based retransmission mechanism requires countdown timer that can interrupt the sender after a given amount of time has expired. The sender will then need to be able to

1. start the timer after each packer is transmitted
2. respond to timer interrupt
3. stop the timer


We have now assembled the key elements of a data transfer protocol.

1. Checksum
2. Sequence Numbers
3. Timers
4. Acknowledgements

each play a crucial role in the operation of protocol.