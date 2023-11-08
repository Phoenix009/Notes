## Introduction

When a server is distributing a large file with $F$ bits to $N$ receivers at rate $u_s$
- Time required = $NF/u_s$

Receiving the data at the slowest receiver:
- slowest receiver has download rate $d_{\min} = \min(d_i)$
- Time required = $F/d_{\min}$

Thus, File Distribution Time:
$$\max\{\frac{NF}{u_s}, \frac{F}{d_\min}\}$$
We can speed up the file distribution by:
1. Increasing the server upload rate: requires higher link bandwidth, multiple servers
2. **Have the receivers help**: Receivers redistribute to other receivers

Upload time using all upload resources:
- total upload bandwidth = $u_s + \sum_i(u_i)$
- time required = $NF/\{u_s + \sum_i(u_i)\}$

Thus, File Distribution Time:
$$\max\{\frac{NF}{u_s}, \frac{F}{d_\min}, \frac{NF}{u_s + \sum_i(u_i)}\}$$

**Challenges**:
- Peers come and go
- Peers need to find each other
- Peers need to be willing to help each other

## Finding Peers:
### 1. Central Directory- Napster:
**Operation**:
- JOIN: NO NEED
- PUBLISH: Update central directory on the available content 
- SEARCH: Ask central directory for peers,
- FETCH: get file directly from peers

**Comments:**
- Servers directory is continually updated
- Always know which content is available
- P2P File transfer, No load on the server

**Challenges**:
	- Locating content is highly centralised. SPOF, Performance bottleneck

### 2. Query Flooding - GNutella:
**Operation**:
- JOIN: Contact a few nodes to become neighbours
- PUBLISH: NO Need
- SEARCH: Ask neighbours,
- FETCH: get file directly from peers

**Comments**:
- Fully Decentralised

**Challenges**:
- Search scope can be large may take long time

### 3. Hierarchical Overlay - Kazaa
**Operation**:
- JOIN: contact the super node
- PUBLISH: Update super node on the available content
- SEARCH: Queries flooded among super nodes
- FETCH: Get file directly from peers

**Comments**:
- Supernode selection favours nodes with high up-time
- Propagating query to a sub-node may take more time than for the super-node to answer itself
- 

Read: [[BitTorrent]]