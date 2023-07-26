
Makes the programming of distributed systems look similar, if not identical, to conventional programming - achieving high level of distribution transparency.


#### RPC Call Semantics:
Request-reply protocols can be implemented in different ways to provide different delivery guarantees. The main choices are:

1. ==Retry Request message==: Retransmit the request until either a reply is received or the server is assumed to have failed.

2. ==Duplicate Filtering==: Controls when retransmissions are used and whether to filter out duplicate requests at the server.

3. ==Retransmission of Results==: keeps a local histroy of result messages to enable lost results to be retransmitted without reexecuting the operations at the server.

Combinations of these choices lead to a variety of possible semantics for the reliability of remote invocations.

![[RPC_semantics.svg]]


##### 1. Maybe Semantics:
Retry Request Message:   NO
Duplicate Filtering:     NO
Retransmission Results:  NO

The remote procedure call may be executed once or not at all.
Can suffer from the following types of failure:
1. Omission failures if the request or reply got lost. Either
	- the operation was not performed. (the request was lost).
	- the operation was performed (the reply was lost).
2. Crash failures when the server fails.


##### 2. At-least-once Semantics:
Retry Request Message:   ==YES==
Duplicate Filtering:     NO
Retransmission Results:  NO

At-least-once semantics can be acheived by retransmission of request messages. As there is no duplicate filtering, a duplicate request leads to re-execution of the procedure.

Can suffer from the following failures:
1. Crash failures when the server fails.
2. arbitrary failures: when the procedures are executed multiple times. One has to be careful, can modify the state multiple times.


##### 3. At-most-once Semantics:
Retry Request Message:   ==YES==
Duplicate Filtering:     ==YES==
Retransmission Results:  ==YES==

At-most-once semantics can be acheived by using all the fault-tolerance measures. The use of retries masks any omission failures. It prevents arbitrary failures by ensuring none of the operations are performed more than once.


#### RPC Implementation:

The client that accesses a service, includes one ==stub procedure== for each procedure in the service interface.

stub procedure is responsible for marshalling the procedure identifier and the arguments into a request message and sending the request message to the server. It also unmarshalls the response from the server.

The server process contains a ==dispatcher==, and for every procedure in the interface it has a ==stub procedure== and a ==service procedure==.

dispatcher is responsible for selecting the appropriate stub procedure according to the procedure identifier.

The stub procedure unmarshalls the arguments and calls the corresponding service procedure. It also marshals the return values for the reply.

RPC is generally implemented over a request-reply protocol. RPC may be implemented to have one of the choices of invocation semantics.