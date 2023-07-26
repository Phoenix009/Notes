We propose an alternative that leaves the training data distributed on the mobile devices, and learns a shared model by aggregating locally-computed updates. We term this decentralised approach ==Federated Learning==.

## Introduction:
A principal advantage of this approach is the decoupling of model training from the need for direct access to the raw training data.

Our primary contributions are 
1) the identification of the problem of ==training on decentralised data== from mobile devices as an important research direction; 
2) the selection of a straightforward and practical algorithm that can be applied to this setting; and 
3) an extensive empirical evaluation of the proposed approach.

Federated learning has distinct privacy advantages compared to datacenter training on persisted data.

### Federated Optimisation:
federated optimisation has several key properties that differentiate it from a typical distributed optimisation problem:
1. Non-IID: The training data on a client is not representative of the entire population
2. Unbalanced: Users will generate varied amounts of data according to the usage
3. Massively distributed: We expect the number of clients participating in a optimisation to be much large than the average of examples per client.

We use a controlled environment that is suitable for experiments, but still addresses the key issues of client availability and unbalanced and non-IID data.

- There is a fixed set of $K$ clients, each with a fixed local dataset. 
- At the beginning of each round, a random fraction $C$ of clients is selected, and the server sends the current global algorithm state to each of these clients (e.g., the current model parameters). 
- We only select a fraction of clients for efficiency, as our experiments show diminishing returns for adding more clients beyond a certain point (incast?). 
- Each selected client then performs local computation based on the global state and its local dataset, and sends an update to the server.
- The server then applies these updates to its global state, and the process repeats.


In datacenter optimisation, communication costs are relatively small, and computational costs dominate. In contrast, in federated optimisation communication costs dominate.

Thus, our goal is to use additional computation in order to decrease the number of rounds of communication needed to train a model. 

There are two primary ways we can add computation:
1) increased parallelism, where we use more clients working independently between each communication round; and, 
2) increased computation on each client, where rather than performing a simple computation like a gradient calculation, each client performs a more complex calculation between each communication round. 

We investigate both of these approaches, ==but the speedups we achieve are due primarily to adding more computation on each client, once a minimum level of parallelism over clients is used==.

## `FederatedAveraging` Algorithm:
The recent multitude of successful applications of deep learning have almost exclusively relied on variants of stochastic gradient descent (SGD) for optimisation. Thus, it is natural that we build algorithms for federated optimisation by starting from SGD.

SGD can be applied naively to the federated optimisation problem, where a single batch gradient calculation (say on a randomly selected client) is done per round of communication. This approach is computationally efficient, but requires very large numbers of rounds of training to produce good models.

To apply this approach in the federated setting, we select a $C$ - fraction of clients on each round, and compute the gradient of the loss over all the data held by these clients. 

Thus, $C$ controls the global batch size, with C = 1 corresponding to full-batch (non-stochastic) gradient descent. We refer to this baseline algorithm as ==FederatedSGD== (or FedSGD).

A typical implementation of `FedGSD` with $C = 1$ and a fixed learning rate $\eta$ has each client $k$ compute the average gradient $g_k$ on its local data at the current model $w_t$
$$g_k = \nabla F_k(w_t)$$

and take a step of the gradient descent on the current model.
$$w^k_{t+1} \leftarrow w_t - \eta g_k$$
and then the central server aggregates these and applies the update
$$w_{t+1} \leftarrow w_{t} =\eta \sum_{k=1}^K \frac{n_k}{n}w^k_{t+1}$$


The amount of computation is controlled by three key parameters:
1. $C$ the fraction of clients that perform computation on each round
2. $E$ the number of training passes each client makes over its local dataset on each round
3. $B$ the local minibatch size used for the client updates. 

Thus, for a client with $n_k$ local examples, the number of local updates per round is given by
$$u_k = E \frac{n_k}{B}$$

When we start two models from the same random initialisation and then again train each independently on a different subset of the data (as described above), we find that naive parameter averaging works surprisingly well.