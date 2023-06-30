Byzantine Faults?
Byzantine Fault Tolerance

Problems:
- Forgery: Sign transactions.
  
- Double Spending: Ledger. 
	Nodes verify each block to check for double spending?

- Theft: 

Transaction:
`<destination-public>, <transaction-id>, <source-signature>`

Solutions:
- Forgery: Assumes participants keep their private key secure. Signature can verify transactions
- Double Spending: Public Log is required to check if the referred source transaction of bitcoin is not used twice
	- Consensus is required to agree upon a common history of transactions. Consistency is required


Approach for Consensus:
	1. Centralized Server: Byzantine Fault
	2. Centralized server only to store logs: server might provide different view of logs to different clients


Bootstrapping problem

What is selfish mining?
 
How is PoS better than PoW?
- Rich gets richer?