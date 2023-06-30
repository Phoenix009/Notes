### Problem:
A collection of processes that can propose values. Consensus ensures that a single one among the proposed value is chosen. 


WE have three agents in the consensus algorithm
- proposers
- acceptors
- learners


### Choosing a Value:
WE have multiple acceptor agents. A proposer sends a proposed value to a set of acceptors. An acceptor may or may not accept the proposed value. A value is chosen when a majority of acceptors have accepted the value.

==P1: An acceptor must accept the first proposal that it receives.==

Problem: There arises a situation where each acceptor has accepted a value, but no single value is accepted by a majority.

P1 and the above problem implies that an acceptor should be allowed to accept more than one proposal. ==This is required to change the earlier accept that did not reach majority==

Each proposal consists of a proposal number and the value. 

==A value is chosen when a single proposal with that value has been accepted by the majority==

We can allow multiple proposals to be chosen, but we must guarantee that all chosen proposals have the same value. Thus it suffices to guarantee

P2: If a proposal with value v is chosen, then every higher-numbered proposal accepted by any acceptor has value v.

Since the communication is asynchronous, a proposal could be chosen without some acceptors receiving the proposal. 
Suppose a new proposer wakes up and issues a high numbered proposal with a different value. Thus, the acceptors that did not receive the previous proposal and received the new proposal have to accept this new value violating P2.

Thus, we reframe P2:

==P2: If a proposal with value v is chosen, then every high-numbered proposal issued by any proposer has value v.==


### Takeaways:

#### Algorithm for issuing proposals:

A proposer chooses a new proposal number n and sends a request to each member of some set of acceptors, asking it to respond with:
(a) A promise never again to accept a proposal numbered less than n, and

(b) The proposal with the highest number less than n that it has accepted, if any.

I will call such a request a prepare request with number n.

If the proposer receives the requested responses from a majority of the acceptors, then it can issue a proposal with number n and value v, where v is the value of the highest-numbered proposal among the responses, or is any value selected by the proposer if the responders reported no proposals.
