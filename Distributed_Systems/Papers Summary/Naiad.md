- It offers the high throughput of batch processors, the low latency of stream processors, and the ability to perform iterative and incremental computations.
- A new computational model, ==timely dataflow==, under- lies Naiad and captures opportunities for parallelism across a wide class of algorithms


## Introduction:
- Stream processors can produce low latency results for non-iterative algorithms
- Batch systems can iterate synchronously at the expense of latency
- Trigger based approaches support iteration with only weak consistency guarantees.

Our goal is to develop a general-purpose system that fulfills all of these requirements and supports a wide variety of high-level programming models, while achieving the same performance as a specialised system

We have developed a new computation model, timely dataflow, that supports the following features:
1. structured loops allowing feedback in the dataflow, 
2. stateful dataflow vertices capable of consuming and producing records without global coordination, and
3. notifications for vertices once they have received all records for a given round of input or loop iteration.

Timely dataflow exposes a principled set of low-level primitives to the programmer, who can use those primitives to build higher-level programming abstractions.

## Timely Dataflow:
Timely dataflow is a computational model based on a directed graph in which ==stateful vertices send and receive logically timestamped messages along directed edges==.

timestamps are used in order to distinguish data that arise in different input epochs and loop iterations.

The resulting model supports concurrent execution of different epochs and iterations, and ==explicit vertex notification after all messages with a specified timestamp have been delivered==

### Graph Structure:
A timely dataflow graph has ==input vertices== and ==output vertices==, where each input receives a sequence of messages from an external producer, and each output emits a sequence of messages back to an external consumer.

Timely dataflow graphs are directed graphs with the constraint that the vertices are organised into possibly nested ==loop contexts==, with three associated system-provided vertices:
1. **ingress vertex**: edges entering a loop context must pass through an ingress vertex
2. **egress vertex**: edges leaving a loop context must pass through an egress vertex
3. **feedback vertex**: cycles must include at least one feedback vertex that is not nested in any inner loop contexts

This looping structure allows us to design logical timestamps based on the dataflow graph structure. 
Every message bears a logical timestamp of type
$$\text{Timestamp}: (e \in \mathbb{N}, \langle c_1 \dots c_k \rangle \in \mathbb{N}^k)$$
where there is one loop counter for each of the $k$ loop contexts and each epoch $e$

==The ingress, egress, and feedback vertices act only on the timestamps of messages passing through them==. The vertices adjust incoming timestamps as follows:

| Vertex | Input Timestamp | Output Timestamp | 
|:------:|:---------------:|:----------------:|
|Ingress|$(e, \langle c_1 \dots c_k \rangle)$|$(e, \langle c_1 \dots c_k, 0 \rangle)$|
|Egress|$(e, \langle c_1 \dots c_k, c_{k+1} \rangle)$|$(e, \langle c_1 \dots c_k\rangle)$|
|Feedback|$(e, \langle c_1 \dots c_k, c_{k+1} \rangle)$|$(e, \langle c_1 \dots c_k+1 \rangle)$|


### Vertex Computation:
Vertices send and receive timestamped messages, and may request and receive notification that they have received all messages bearing a specific timestamp.

Each vertex $v$ implements two callbacks:
$$v\text{.onRecv}(e:edge, m:message, t:timestamp)$$
$$v\text{.onNotify}(t:timestamp)$$

A vertex may invoke two system-provided methods in the context of these callbacks.
$$v\text{.sendBy}(e:edge, m:message, t:timestamp)$$
$$v\text{.NotifyAt}(t:timestamp)$$

- $u\text{.sendBy}(e, m, t)$ results in a corresponding invocation of $v\text{.onRecv}(e, m, t)$ where $e$ is an edge form $u$ to $v$

- $v\text{.NotifyAt}(t)$ results in a corresponding invocation of $v\text{.onNotify}(t)$


### Achieving Timely DataFlow:
A timely dataflow system must reason about the ==impossibility of future messages bearing a given timestamp==.

At any point in an execution, the set of timestamps at which future messages can occur is constrained by the current set of unprocessed events

Since events cannot send messages backwards in time, we can use this structure to compute lower bounds on the timestamps of messages an event can cause.

Each event has a timestamp and a location (either a vertex or edge), and we refer to these as a ==pointstamp==:
$$\text{Pointstamp} = (t \in \text{Timestamp}, l \in \text{Edge} \cup \text{Vertex})$$


We say a pointstamp $(t_1, l_1)$ could-result-in $(t_2, l_2)$ iff there exists a path $\Psi = \langle l_1,\dots,l_2 \rangle$ in the dataflow graph such that the timestamp $\Psi(t_1)$ that results from adjusting $t1$ according to each ingress, egress, or feedback vertex occurring on that path satisfies $\Psi(t_1) \le t_2$

The structure of timely dataflow graphs ensures that, for any locations $l_1$ and $l_2$ connected by two paths with different summaries, one of the path summaries always yields adjusted timestamps earlier than the other. We record the minimal path summary over all paths from $l_1$ to $l_2$ and denote using $\Psi[l_1, l_2]$

To efficiently evaluate the *could-result-in* relation for two pointstamps $(t_1, l_1)$ and $(t_2, l_2)$ we test whether $\Psi[l_1, l_2](t_1) \le t_2$

For each active pointstamp the scheduler maintains two counts: 
1. an occurrence count of how many outstanding events bear the pointstamp, and 
2. a precursor count of how many active pointstamps *could-result-in* this pointstamp.


As vertices generate and retire events, the occurrence counts are updated as follows:

| Operation | Update |
|---------|:------:|
|$v.\text{sendBy}(e, m, t)$|$\text{OC}[(t, e)] \leftarrow \text{OC}[(t, e)] + 1$|
|$v.\text{onRecv}(e, m, t)$|$\text{OC}[(t, e)] \leftarrow \text{OC}[(t, e)] - 1$|
|$v.\text{notifyAt}(t)$|$\text{OC}[(t, v)] \leftarrow \text{OC}[(t, v)] + 1$|
|$v.\text{onNotify}(t)$|$\text{OC}[(t, v)] \leftarrow \text{OC}[(t, v)] - 1$|


When a pointstamp $p$ becomes active:
1. initialise $p$'s precursor count to the number of existing active pointstamps that *could-result-in* $p$. 
2. increments the precursor count of any pointstamp that $p$ *could-result-in*.

When a pointstamp $p$ becomes inactive ($\text{OC}[p] = 0$):
1. decrement the precursor count for any pointstamp that $p$ *could-result-in*.


When an active pointstamp $p$’s pre-cursor count is zero, there is no other pointstamp in the active set that *could-result-in* p, and we say that $p$ is in the frontier of active pointstamps. 
==The scheduler may deliver any notification in the frontier.==


## Distributed Implementation:
- A group of processes hosting workers manage a partition of the timely dataflow vertices. 
- Workers exchange messages locally using shared memory, and remotely using TCP connections between each pair of processes. 
- Each process participates in a distributed progress tracking protocol, in order to coordinate the delivery of notifications.

### Data Parallelism:
A program ==specifies its timely dataflow graph as a logical graph== of stages linked by typed connectors. Each connector optionally has a partitioning function to control the exchange of data between stages.

A programmer can use partitioning functions to hash or range partition incoming messages by a key, in order to implement “group by” or “reduce” functionality. When no partitioning function is supplied, the system delivers messages to a local vertex.

At execution time, Naiad expands the logical graph into a physical graph where each stage is replaced by a set of vertices and each connector by a set of edges.

Naiad projects each pointstamp $p$ from the physical graph to a pointstamp $\hat{p}$ in the logical graph, and evaluates the *could-result-in* relation on the projected pointstamps. 

This projection leads to a loss of resolution, since there are cases where $p_1$ cannot-result-in $p_2$ but $\hat{p_1}$ could-result-in $\hat{p_2}$.

### Distributed Progress Tracking:
We adapt the approach for progress tracking based on a single global frontier ([[#Achieving Timely DataFlow]]) to a distributed setting in which multiple workers coordinate independent sets of events using a local view of the global state. 

We base our initial protocol on broadcasting occurrence count updates, and then refine it with two optimisations.

For each pointstamp each worker maintains a 
- local `occurrence_count`: global occurrence count
- local `precursor_count`: computed from the `occurrence_count`
- local `frontier`: defined using *could-result-in* relation on local active pointstamps

Worker broadcasts (to all workers, including itself) progress updates, which are pairs $(p \in \text{Pointstamp}, \delta \in \mathbb{Z})$ with $\delta$ chosen according to the update rules from [[#Achieving Timely DataFlow]].

The broadcasts from a given worker to another must be delivered in FIFO order. When a worker receives a progress update $(p, \delta)$, it adds $\delta$ to the local occurrence count for $p$


### Fault Tolerance and Availability:
Each stateful vertex implements a ==CHECKPOINT== and ==RESTORE== ==interface==, and the system invokes these as appropriate to produce a consistent checkpoint across all workers.

Each vertex may either 
1. log data as computation proceeds, and thus respond to checkpoint requests with low latency; or 
2. write a full, and potentially more compact, checkpoint when requested. 

When the system periodically checkpoints, 
1. all processes pause worker
2. message delivery threads, flush message queues by delivering outstanding ON-RECV events (buffering and logging any messages that are sent by doing so)
3. invoke CHECKPOINT on each stateful vertex

Our current design favours performance in the common case that there are no failures, at the expense of availability in the event of a failure.

