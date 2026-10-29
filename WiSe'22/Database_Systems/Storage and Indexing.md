---
title: "Storage and Indexing"
weight: 3
---
## Cost Model:

We will use the following notations and assumptions in our analysis:

$B$ is the number of data pages

$R$ is the nnumber of records per page

$D$ is the time required to read/write one page

$C$ is the time to process a record

The cost of I/O to dominates all other cost of a database opearations. Thus we use the number of page I/O’s as out cost metric.

## Three File Organization Strategies:

Our goal is to emphasize how important the choice of an appropriate file organization can be considering some of the most frequently performed database operations

1. Scan: Fetches all the records in a file. The page in the file must be fetched into the main memory.
2. Point Query / Search with equality condition: Fetch records that satisfy an equality selection. Pages that contain the qualifying records must be fetched in the main memory and the qualifying records must be located in the pages.
3. Range Query / Search with range selection: Fetch records that satisfy a range selection
4. Insert: Insert a given recods into the file. We identify the page where a new record must be inserted, fetch that page form the disk, modify that page and write that page back in the disk.
5. Delete: Delete a record that is specified. We must identify the page that contains the specified record. Fetch that page in the main memory, modify that page and write it back.

## A. Heap Files:

**Scan**: $B(D + RC)$. We have to fetch every page for the given file from the disk and process every tuple in each page

**Point Query**: $0.5B(D + RC)$. As there is no particular ordering to guide our search we have to retreive all the pages and find the tuple.

**Range Query**: $B(D + RC)$. Again qualifying tuples can be in any page thus all the pages are fetched and processed.

**Insert**: The records are always inserted at the end of the file. Thus we fetch the last page insert the tuple and write back. $2D + C$

**Delete**: The record must be first fetched and then deleted. We assume there is no compaction performed. The cost is $\text{Search Cost} + C + D$

## B. Sorted Files:

**Scan:** The cost is $B(D + RC)$.

**Point Query:** $\log_{2}(B) + \log_{2}(R)$. Assuming the query is based on the search key we can do binary search to retreive the tuple that satisfy the search condition.

**Range Query:** $\log_{2}(B) + \log_{2}(R)$

**Insert:** $\text{Search} + 2(0.5B(D + RC))$. When inserting a new tuple care must be taken to not violate the sorted order. Thus, on average half of the records must be shifted by one position to make space for the new tuple

**Delete:** $\text{Search and Delete} + 0.5B(D+ RC)$ We must shift half of the tuples on an average to fill the hole created by deletion.

## C. Hashed Files:

The pages in hashed file are grouped into buckets. The bucket to whic the record belongs to can be obtained by applying the hash function $h$ on the search key.

**Scan:** $B(D + RC)$

**Point Query:** $H + D + 0.5RC$. We can use the hash to find the bucket in which the record exists and fetch onlny that page in main memory and fetch the record in that page.

**Range Query:** Range Queries are not typically supported in a Hash File Organization. However range queries can be modified as a sequence of point queries.

**Insert:** $\text{Search for Bucket} + C + D$

**Delete:** $\text{Search for Bucket} + C + D$

## Indexes:

An index on a file is a data structure that is designed to speed up operations.

An index can be viewed as a collection of data entries, with an efficient way to locate all data entries with search key value $k$.

Each such data entry $k^{*}$ has enough information to allow us retreival of all the data records having the search key value $k$.

## What information must be stored in each index data entry?

We have three options:

1. A data entry $k^*$ is an actual data record
2. $(k, r_{id})$ where $r_{id}$ is the data record pointer
3. $(k, r_{id}\text{-list})$ the second entry is a list of data record pointers

If The First approach is used then there is not need to store the data records seperately.

The options 2 and 3 are independent of the organization used to store the file that is indexed.

## Properties of Index:

### Clustered and UnClustered Index:

When a file is organized such that the data records ordering is same as the ordering of index entries it is called a clustered index. Option 1 discussed above is a clustered index. When the ordering is not maintatined then the index is unClustered.

If the index is clustered then the data records retreival is mostly sequential, however for unclustered index the data retreival is random and leads to high access times.

### Dense and Sparse Index:

An index is said to be dense if it contains one data entry for every search key value that appears in a record in the indexed file.

A sparse index contains only one entry for each page in the index file.

We can also create a clustered sparse index.

### Primary and Secondary Index:

An index on a set of fields that includes the primary key is called a primary index. All other index are secondary.

In some cases the indexes that use option 1 are referred as primary indices as the data records are clustered / ordered based on these indices.

A primary index is guaranteede to not have any duplicate due to the presence of the primary key. But secondary index in general can conatin duplicates.

# Tree Structured Indexing:

## ISAM - Indexed Sequential Access Method:

Can be understood as creating page tables recursively until the final page table can be accomodated in a single page.

We start with a sorted file sorted on a particular search key. Now to perform a search query we must perform a binary search. This binary search can be quite expensive because of the randomm IOs if the file is large.

One idea is to create a sparse index on the sorted file. (This eventually creates a clustered sparse index). Thus we create a second file with one record per page in the original data file of the form **(anchor record key, page pointer).**

The format of the page in the second index file is

$$
(p_{0}, k_{0}, p_{1}, k_{1} \dots)
$$

As the size of an entry in the index file is much smaller than the size of the page, the index file is much smaller than the data file.

Thus, binary search of the index file is much faster than a binary search of the data file.

However, the index file can still be fairly large, so we apply the previous step of building sparse indexes recursively until the final index file can fit on a single page.

The ISAM structure is completely **static**. The number of disk IOs is equal to the number of levels fo the tree and is equal to $\log_{F}(N)$ where $N$ is the number of primary leaf pages and $F$ is the fan-out.

For a point query, we start at the root node and determine which subtree to search by comparing the value in the search field of the given record with the key values in the node.

For a range query we again perform the point query to find the first record in the range and then perform sequential access to get the results.

Notice that for ISAM, once the structure is created, inserts and deletes affect only the leaf pages. A consequence of this is that long overflow chains could develop. These can **significantly affect the time to retreive a record**.

The fact that only the leaf pages are modified also has an important advantage with respect to concurrency. In ISAM, we can safely omit the locking step for index pages. This is an **important advantage of ISAM** over a dynamic structure like a B+Tree.

# B+ Trees:

The B+ tree search structure is a balanced tree in which the internal nodes direct the search and the leaf nodes contain the data entries.

B+Trees have a parameter called $d$, called the order of the tree. Every node contains $m$ entries, where $d \le m \le 2d$. the root node is the only exception to this requirement on the nuber of entries; for the root is imply required that $1 \le m \le 2d$.

## Format of a Node:

Non-leaf nodes with $m$ index entries conatain $m+1$ pointers to children. Pointer $P_{i}$ points to a subtree in which all key  values $K$ are such that $K_{i} \le K \lt K_{i+1}$

As special cases the first pointer $P_{0}$ points to the subtree such that $K \lt K_{i+1}$ and the last pointer  points to subtree having values $K_{m} \le K$

Only leaf nodes conatain data entries. The leaf pages are chained together in a doubly linked list. Thusm the leaves form a sequence, which can be used to answer range queries efficiently.

## Search:

The algorithm for search finds the leaf node in which a given data entry belongs. Note that finding $i$ in tree_search requires us to search within the node, which can be done with either a linear or a binary search.

```py
def search(K: key) -> NodePointer:
	return tree_search(root, K)

def tree_search(root: NodePointer, K: key):
	if root is a leaf: return root
	if K < K1: return tree_search(P0)
	if K >= Km: return tree_search(Pm)
	for i in range(m):
		if K[i] < K <= K[i+1]: return tree_search(P[i], K)
```

## Insert:

The algorithm for insertion takes an entry, finds the leaf node where it belongs, and inserts it there.

Occasionally a node is full and it must be split. When the node is split, an entry pointing to the node created by the split must be inserted into its parent.

In general we have to split a non-leaf node when it is full, containing 2d keys and 2d+1 pointers, and we have to insert another index entry.

Thus now we have 2d+1 keys and 2d+2 pointers, which get split into two nodes each with d keys and d+1 pointers. One middle element is chosen as the splitting key. This middle element along with a pointer to the left split node is pair that is to be inserted in the parent node.
