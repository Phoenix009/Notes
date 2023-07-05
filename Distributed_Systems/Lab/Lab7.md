

Join Flow:
1. The first view contains only node \[1\]
2. If a new node joins (it downloads the view from node 1)
3. Invoke PAXOS to create the next view
	Ask the nodes in the current view to agree on the new view
4. If PAXOS is successful then we move the next view with \[1, 2\] as the new view

Remove Flow:
1. A node discovers that some node of the view is not responding
2. kicks off PAXOS to agree on the new view
3. 
