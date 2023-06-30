
### INTRODUCTION:
Remote Procedure Calls is based on the observation that procedure calls are a well-known and well-understood mechanism for transfer of control within a program running on a single computer.

It is proposed that this same mechanism be extended to provide for transfer of control and data across a communcication network.

There are many attractive aspects to this idea:
	1. Clean and Simple semantics
	2. Efficient
	3. Generality

We understood the choices the designer of an RPC facility must make. In practice we found that several areas were inadequately understood, and produced a system whose design has several novel aspects.

This paper includes a discussion of the issues and their major decisions about them, and describe the overall structure of our solution.


#### AIMS:
The existing communication mechanisms appeared to be a major factor constraining further development of distributed computing. Thus we aim to:
	1. make RPC communication highly efficient
	2. make semantics of RPC package as powerful as possible, without the loss of simplicity or efficiency.
	3. provide secure communication with RPC.

An important issue in design is resolving the tension between powerful semantics and efficiency.


#### FUNDAMENTAL DECISIONS:
It is not an immediate consequence of our aims that we should use procedure calls as the paradigm for expressing control and data transfer. However, regardless of the mechanism used, the problem of passing arguments and results and of network security are essentially unchanged.

We discarded the possibility of emulating some form of shared address space among computers. There are two major challenges:
whether representation of remote addresses can be integrated into our programming languages without undue upheaval, second whther acceptable effeciency can be acheived. 

A principle that we used several times in making design choices is that the semantics of remote procedure calls should be as close as possible to those of local procedure calls. 

Violation of this principle seemed likely to lead us into complexities that have made previous communication packages and protocols difficult to use.


#### STRUCTURE:
When making remote calls, five peices of program are involved: the `user`, the `user-stub`, the `RPCRuntime`, the `server-stub` and the `server`

The `user`, the `user-stub` and one instance of `RPCRuntime` execute in the caller machine; the `server`, `server-stub` and another instance of `RPCRuntime` execute in the callee machine.

`user-stub`:
is responsible for placing the specification of the target procedure and their arguments into one or more packets and asking the `RPCRuntime` to transmit these reliably to the callee machine.

`server-stub`:
is responsible for unpacking the information sent by the `user-stub` and calling the appropriate procedure in the server. When the call in the server completes, it returns to the `server-stub` and the results are passed back to the suspended process in the caller machine.

`RPCRuntime`:
is responsible for retransmissions, acknowledgements, packet routing and encryption.

A programmer does not need to build detailed communication-related code. After designing the interface, he need only write the user and server code.

An interface module is mainly a list of procedure names, together with the types of their arguments and results.

A program module that implements procedures in an interface is said to export that interface.

A program module calling the procedures from an interface is said to import that interface.


#### BINDING:
How does a client specify what he wants to be bound to?
How does a caller determine the machine address of the callee
How to specify to the callee the procedure to be invoked?

The binding operation offered by the RPC package is to bind an importer of an interface to an exporter of an interface. After binding, calls made by the importer invokes the procedures implemented by the exporter.

##### *Naming:*
There are two parts to the name of an interface:
	1. `type`: which interface the caller expects the callee to implement. Ex: mail server
	2. `instance`: which particular implementor of an abstract interface is desired: Ex: some particular mail server selected from many

##### *Locating an Exporter:*
We use the Grapevine distributed database for our RPC binding.
Grapevines database consists of a set of entries each having a key value known as Grapevine `RName`. 

There are two types of entries: `individual` and `groups`

Grapevine keeps several information for each entry but we are concerned with only two:
	1. `connect-site` for each `individual` entry: network address
	2. `member-list` for each `group`: list of `RName`

RPC package maintains two entries in the Grapevine database for each interface name:
	1. `type`
	2. `instance`
Thus, `type` and `instance` are both `RName`'s

The database entry for the `instance` is a `connect-site` and is a network address of the machine on which that instance was last exported. The database entry for the `type` is a `member-list` whose members are the Grapevine RNames of the instances of that type.

When an exporter wishes to make his interface available to remote clients, the server code calls the server-stub which in turn calls a procedure `ExportInterface` in `RPCRuntime`.

`ExportInterface` is given the interface name, a procedure (dispatcher) implemented in server-stub which will handle incoming calls for the interface.

It calls Grapevine and ensures that appropriate entries exists for the instance and type of the interface.

RPCRuntime then records information about this export in a table maintained on the exporting machine. For each currently exported interface, this table contains the interface name, the dispatcher and a 32-bit value that serves as a permanently `unique_identifier` of the export.

When an importer wants to bind to an exporter, the user code calls its `user-stub` which calls a procedure, `ImportInterface` with the `type` and `instance` information in the `RPCRuntime`. `RPCRuntime` queries Grapevine and gets the network address of the exporter to make RPC for the binding information.

Depending on whether the machine is currently exporting the interface, appropriate binding information (the `unique_identifier` ) is sent back to the importing machine and the binding succeeds. The exporter network address, identifier and table index are remembered by the user-stub for use in remote calls.


##### *Discussion about the scheme:*
Importing an interface has no effect on the data structures in the exporting machine.

Use of the unique identifier scheme means that bindings are implicitly broken if the exporter crashes and restarts. This scheme allows calls to be made only on procedures that have been explicitly exported through the RPC mechanism.

the access controls that restric updates to the Grapevine database have the effect of restricting the set of users who will be able to export particular interface names. This is desired: it should bot be possible for a random user to claim that his workstation is a mail server and to thereby be able to intercept message traffic.


#### *TRANSPORT PROTOCOL:*
The particular nature of RPC communication means that there are substantial perrformance gains possible if one designs and implements a transport protcol specifically for RPC.

One aim we emphasized in our protocol design was minimizing the elapsed time between initiating a call and getting results and also minimize the load imposed on a server by substantial number of users.

With protocols for bulk data transfers this is not important: most of the time is spent in setting up and taking down connections, and require maintainence of state information.

==We envisage our machines being able to serve substantial number of clients and it would be unacceptable to require either a large amount of state information or expensive connection handshaking.==

We guarantee that if the call returns to the user then the procedure in the server has been invoked ==precisely once==, otherwise an exception is reported to the user. 

If an exception is sent, then the user has no idea whether the server has crashed or if there is a problem in the network.


##### *SIMPLE CALLS:*
The machine that transmits a packet is responsible for retransmitting it until an acknowledgement is received. 

The result of a call is sufficient acknowledgement that the call was received and a call packet is sufficient acknowledgement for the previous call made.

to make a call the caller sends a ==call packet== containting *call identifier*, data specifying the desired procedure and the arguments. When a procedure returns, a ==result packet== containing the *same call identifier*, and the results is sent back to the caller.

The call identifier consists of:
	1. machine_identifier: permanent and globally unique
	2. machine relative process_identifier for the calling process
	3. sequence number

We define an activity as a tuple (machine_identifier, process_identifier). the important property of an activity is that each activity has ==at most one== outstanding packet.

When a call packet is received, it can be discarded as a duplicate by examining its caller_identifier.

We can compare this arrangement with that of a heavy weight transport protocol.

==*Connection*==: We think of a connection as the shared state information betwen an activity of a calling machine and the RPCRuntime package on the server machine.

When the connection is active both machine maintain significant amount of information. When the connection is idle, the only state information in the server machine is the entry in its table of sequence numbers.

==*Connection Establishment*==: Receipt of a call packet form a previously unknown activity is sufficient to create the connection.

==*Connection Termination*==: No explicit connection termination protocol. If a connection is idle, the server machine may discard its state information after an intercal when there is no longer any danger of receiving retrainsmitter call packets, and it can do so without interacting with the caller machine.


This scheme gurantees of traditional connection-oriented protocols without the costs. We are assuming that the call sequence number from an activity does not repeat even if the calling machine is restarted. We generate converstation identifier based on a 32-bit clock maintained by every machine (initialized form network time servers when a machine restarts).


##### *COMPLICATED CALLS*:
To handle lost packets, long duration calls and long gaps between calls, the packet is modified to request an explicit acknowledgement. 

When the caller gets the acknowledgement it waits for the result packet. While waiting, the caller periodically send a proble packet to the callee, which it is expected to receive an ACK. 
This helps to detect if the callee has crashed or if there is a serious issue with the communication network. 

Provided the caller receives ACK for the probes it continues waiting. Notice this does not detect if the callee has deadlocked. This is in keeping with the semantics of local procedure calls.

If the arguments or results are too large to fit in a single packet, they are sent in multiple packets with each but the last packet requiring an explicit ACK. This allows the implementation to use only one packet buffer at each end for the call and avoids the necessity of including the buffering and flow control strategies found in normal bulk data-transfer protocols. 

Thus, if the call requires more than one packet for its arguments or results, our protocol sends more packets than are logically required.

