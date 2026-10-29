---
title: "Query Processing and Optimization"
weight: 4
---
**Underlying Question:**

How to compute complex queries at scale?

**Learning Objective:**

query optimization, query processing, operator models, pull, push, vectorization, rule vs cost; intra- vs inter-query parallelization , accross threads vs machines; mu*t*able.

## Computing the results of a query:

We have two options

1. Brute Force (Scan): we inspect each and every tuple and check whether the query predicate holds for that tuple and add it to the result set.
2. Index (Index Scan): We organixe the contents of the realation such that we do not have to inspect each and every tuple to determine whether the tuple belongs to the result set.

### Partitioning Function:

Given a domain D, any function p: [R] → D is called a partitioning function.

### Horizontal Partitioning:

Given a relation R any assignment of the tuples into sets Ri is called a horizontal partitioning of R. Ri’s are called the horizontal partitions of R

Each and every tuple must belong to atleast one partition

### Disjoint Horizontal Partitioning:

Is a horizontal partitioning of a relation with each tuple being mapped to exactly one partition.

### Induced Horizontal Partitioning:

Given a partitioning function *p:[R] → D,* the horizontal partitioning of R into Ri is called a induced horizontal partitioning.

### Induced Recursive Horizontal Partitioning:

We have multiple partitioning function *pi: [R] → Di*

We iteratively apply the partitioning function on the partitions created by the previous partition function.

### Index:

Any set of horizontal partitioning (recursive or non-recursive) that can be exploited by query to reduce its search space is called and index.

> 💡 Almost all operations used for querying the database can be reduced to one or combination of the following operations:
>
> 1. Indexing
> 2. Column Layout
> 3. Join Algorithms

# JOIN Algorithms:

## Applications for JOIN Algorithms:

JOIN ON

INTERSECT, EXCEPT, …

GROUP BY [HAVING]

subqueries may be translated to JOIN

This makes the study of JOIN algorithms important.

The choice of the JOIN algorithm on query performamce is substantial and may lead to several orders of magnitude performance difference.

## FOUR Principal classes of JOIN Algorithms:

1. Nested Loop
2. Index Nested Loop
3. Sort-Merge
4. Hash based

## Nested Loop JOIN:

Assume there are two relations $R$ and $S$ and a join predicate $\text{JP}$ then, a nested loop join simply implements the cross product of $R$ and $S$ followed by a selection

$$
\sigma_{\text{JP}}(R \times S)
$$

for every tuple in the left relation we scan every tuple in the right relation and check if they satisfy the join condition. If they do then they are added in the output relation.

```py
def join_condition(r_tuple: Tuple, s_tuple: Tuple) -> bool:
	# can be any join condition

def nested_loop_join(R:Relation, S:Relation, join_condition):
	for r_tuple in R:
		for s_tuple in S:
			if join_condition(r_tuple, s_tuple):
				output(r_tuple, s_tuple)
```

Nested Loop JOIN should only be used for:

1. small input sets
2. cases where no other join algorithms are applicable
3. for testing purposes. obtain baseline results.

The Nested Loop JOIN algorithm works for every type of $\text{JP}$

## Index Nested Loop JOIN:

In an indexed nested loop join we replace one of the loops of the nested loop join with an index.

This works only when the join predicate $\text{JP}$ can be evaluated efficiently by that index.

```py
def join_condition(r_tuple: Tuple, s_tuple: Tuple) -> bool:
	# is an equality condition on the indexed attribute
	# Can be a range query as well :O
	r_tuple[attr] == s_tuple[attr]

def get_tuples(attr_value: attr_domain) -> List[Tuple]:
	# return all the tuples that have value `attr_value` 
	# for the indexed attribute.

def index_nested_loop_join(R:Relation, S:Relation):
	# the join is on the indexed attribute `attr`
	for s_tuple in S:
		for r_tuple in get_tuples(s_tuple.attr):
			output(s_tuple, r_tuple)
```

Indexed nested looop join is typically a good choice if one of the inputs $R$ or $S$ already has a suitable index available.

This algorithm works for $\text{JP}$ that may be translated into a sequence of index lookups. This is not only the case of equi joins.

## Simple Hash JOIN:

SHJ builds or uses an existing hash table on one of the input relations say $R$. All elements of $S$ are then probed against that hash table.

```py
def join_condition(r_tuple: Tuple, s_tuple: Tuple) -> bool:
	# has to be an equality condition
	r_tuple[attr] == s_tuple[attr]

def get_hash_table(relation, key_attr) -> List[Tuple]:
	# return a hash table computed on the key_attr 

def index_nested_loop_join(R:Relation, S:Relation):
	# the join is on the attribute `attr`
	R_ht = get_hash_table(R, attr)

	for s_tuple in S:
		for r_tuple in R_ht[s_tuple.attr]:
			output(s_tuple, r_tuple)
```

We decide what to do with the computed hash table. We can either delete them or save them in the catalog to be used in future query processing.

## Sort-Merge JOIN:

We sort the relations on the joining attribute and exploit the sorted order to compute the result of the JOIN.

For the code the following assumptions hold:

1. The Relation R has `attr` as the primary key => the attribute has unique values for each tuple
2. The Relation S has `attr` as the foreign key referencing R[attr] => there can exist duplicate values for tuple for that attribute

```py
def sort_merge_join(R: Relation, S: Relation):
	sort(R, attr)
	sort(S, attr)

	r_pointer = 0
	s_pointer = 0
	
	while r_pointer < len(R) and s_pointer < len(S):
		if R[r_pointer][attr] == S[s_pointer][attr]:
			output(R[r_pointer], S[s_pointer])
			s_pointer += 1
		else:
			r_pointer += 1
```

It is possible that there may exist index on the `attr` for the relation then, sort is not needed.

## Generalized Co-Group JOIN:

The main idea is to partition both the joining relation using a partitioning function into groups. Groups that are partitioned into the same partition for both the relation are called co-groups. Then we can perform the join operation between each co group using any JOIN algorithm we discussed.

```py
def generalized_co_group_join(R:Relation, S:Relation):
	R_groups = partitioning_function(R)
	S_groups = partitioning_function(S)
	
	for each group_id:
		r_group = R_groups[group_id]
		s_group = S_groups[group_id]
		cross_product(r_group, s_group)
```

CoGroups are a general property of a equiJoin rather than any other join.

To express equi join through a co grouping operation can be done in two ways:

1. Grouping with equality: The partition function simply return the joining attribute. Thus all tuples with the same joining attribute come under the same group. thus to compute the result we just perform cross product among the corresponding groups
2. Grouping with partition: All the partition functions return a value that is a function of the join attribute. Thus all the tuples that have the same join key will return the same value and end up in the same group.

   However there can be tuples that do not have the same join key but end up in the same group. Thus we again need to perform JOIN operation with the corresponding co groups.

### Can a Cross Product be ALWAYS used to compute the result of the JOIN operation?

No, The cross Product can only be used to compute the result when the partition is done using equality of the join keys

### How can we optimize generalized CoGroup Join?

1. When partitioning relation $R$ we can create a bloom filter for the keys of $R$. Now while partitioning $S$, we cam use the bloom filter to check if there are any tuples in $R$ that satisfy this key. Thus, we can discard all the keys in $S$ that do not have a key in $R$.
2. While partitioning, if there is any partition that has a cansiderably big size can be recursively partitioned to form smaller partitions that can be directly joined in the main memory.

# Grouping and Aggregation:

### Which phases are present in hash-based grouping?

1. grouping phase: we build a hash table on the input relation
2. aggregation phase: for each distinct key in the hash table, we call the aggregation function on elements of that group.

   There can be tuples with different keys being mapped to same bucket??

### Which phases can be identified in sort-based grouping?

1. grouping phase: sort table on the grouping key. if it is not already sorted wrt that key
2. aggregation phase: for each distinct key we call aggregation function on elements of that group

# External Merge Sorting:

### Why do we need external merge sort?

We use external merge sort when a dataset is too large to be sorted in main memory.

### Why not simply use quicksort on a memory-mapped file?

The critical part for quicksort is the initial phase when it is recursively partitioning the data and those partitions do not yet fit in the main memory.

for partitioning one single partition we read the entire partition (consisting of multiple pages). during the partition the tuples from one page can be swapped into some other page (Causing a lot of random access). Hence the entire partition is to be written back in the disk.

This partition is done recursively until the partitions created can be accomodated in the avaiable memory. After than we can run quicksort on individual partitions.

The number of passes needed for a relation with $n$ pages and memory having $m$ pages is $\log_{2}(\lceil{\frac{n}{m}}\rceil)$

For each of these passes the entire dataset is read and written causing a lot of memory access.

Thus, a better strategy is to partition by reading one input and writing $F$ output partitions. $F$ is called the fan-out. this reduces the number of passes required.

For external merge sort:

### Phase 0:

Also called the run generation. We fill the entire available main memory with data, quicksort the data, and write the sorted data back in the disk. We call this sorted sequence as a *run*

### Phase 1:

In the merge phase, We repeatedly merge multiple inpout runs into one larger run. We continue this process until we end up with a single sorted run on disk

$F$ is called the fan-in and is the number of input runs we merge in a single merge operation.

### Why is it a bad idea to implement merge sort with a $F = 2$?

This leads to a higher number of passes and thus more read and write opearations. the number of passes / merge levels are given by $\log_{F}(\lceil\frac{n}{m}\rceil)$

### Why is is a bad idea to implement merge sort with a $F = m-1$ where $m$ is the numberof pages available in the main memory?

This is only optimal wrt the number of merge levels, however, this leads to a lot of random i/o which may lead to slower runtimes.

## Replacement Selection:

# Late, Online and Early Grouping with Aggregation:

### Late Grouping:

We first fully sort the data using external sorting and write the sorted data to disk. After that the sorted data is read and aggregated.

### Online Grouping:

In online grouping we perform expternal sorting partially until the final merge. For the last merge when we read the remaining runs from disk, we merge them in main memory and feed them direactly to the grouping and aggregation operation.

### Early Grouping:

In early grouping and aggregation whenever we generate a run or whenever we execute a merge we feed the sorted output stream into the grouping and aggregation oepration.

> 💡 The aggregating functions need to be distributive or algebraix for early grouping.

### Disadvantages of Early aggregation:

When we do groouping and aggregation we create a sorted version of the input table. This sorted table can be saved and exploited to evaluate future queries. This table is however not materialized in early grouping and aggregation.

# Query Optimization:

The Query Parser is responsible in converting the given declarative (WHAT part) SQL query into a corresponding relational algebraic form.

The high level task of the query optimizer is to translate the algebraix expression into an executable problem (HOW part). The goal of the query optimizer is to generate an optimal program wrt runtime.

The query optimizer requires statistics about the data stored in the database as the decision for an optimal executable program may depend on those statistics. Ex: In order to decide whether to access rows in a table through full scan or an unclustured index depends on the selectivity of the query.

An important consideration is whether statistics are computed and updated automatically or whether the user has to trigger statistics collection manually.

A query plan is a DAG where the nodes are operators and the edges implement any variant of the pipelining interface. That query plan is than interpreted by an executable program called the query plan interpreter

# Challenges in Query Optimization:

## Join Order:

For a query plan that contains multiple joins, we often have a choice in which order we execute the different join operations.

A particular order assumes a suitable join predicate on all pairwise joins. If no such predicate exists, then the join is equivalent to a cross product and such a join order does not have to be considered for a query optimization.

### How do we select a right access path?

The issue is I/O costs of a particular access method rather than the computational costs.

In order to answer this question, the query optimized requires statistics about the data distribution and good estimates on predicate selectivities.

### How does the query optimizer decide which access plan to take?

The query optimizer has to estimate the costs of the different alternatives. Based on these estimates, the query optimizer picks the plan with the lowest estimated costs.

## Cost Based Optimization:

The overall idea of the cost based optimization is:

1. enumerate the set of all plan alternatives
2. For each plan estimate the costs of executing it
3. pick the plan with the lowest estimated costs and execute it.

The number of possible bushy trees for $n$ input relations is given by the Catalan number **$C_{n-1}$**

$$
C_{n} = \frac{1}{n+1}{{2n}\choose{n}}
$$

## Join Graph:

The core idea of a join graph is to represent the join predicates for a given query. The join graph allows us to restrict the join enumeration to plans without cross products.

As any pair of relations may trivially be joined through a cross products, a join graph can be considered to be dense. However, typically only the non-trivial join predicates are considered and displayed in a join graph.

## Dynamic Programming approach to find the optimal plan:

1. for each input table compute the set of subplans that acess a single input table (different access paths for a single relation)
2. for each relation keep the subplan with the lowest estimated costs
3. set $\text{plansize} = 2$
4. compute all subplan that access a subset of size $\text{plansize}$ from the input relations; use smaller plans as building blocks
5. for each subset: only keep the plans with the lowest estimated costs (Pruning)
6. if $\text{plansize} = n \text{(number of input relations)}$ then stop else increment plansize and go to step 4

### Why and how would we exploit the join graph in dynamic programming?

The join graph allows us to check if two given relations can be joined using either a selective join or a cross product. Thus we can skip all the pairs of relations that do not have a selective join.

# Query Execution Models:

A query plan is not directly executable. We have to someow translate it into executable code.

We have the folowing options:

1. Function Libraries: We wrap physical operation into a function. Then each query plan can be simply translated into nested function calls.

   *What is the problem with this approach?*

   If we translate a query into a series of function calls, each function will run to its completion materialize its entire result and only then return its results to a possible next function call.

   This is a bad idea when the intermediate results are very large.
2. Code Compilation: We compile the physical query plan into executable code.
3. Precompiled Modules: We wrap each phyhsical algorithm into an operator. Operators are furthen connected through some sort of pipelining mechanism

### What is the core idea of pipelining?

The core idea of pipelining is to allow opeartors to return some of their results before moving to completion. **Whether a function can return their results before completion depends on the type of computation performed.**

### What is a pipeline breaker?

Pipeline breakers are the operators that cannot return any meaningful result before going to its completion. ex: aggregation.

This implies that the these functions wait for their entire input to be “materialized” before doing their own computation.

### Why is it important to consider pipeline breakers in query processing?

They do not offer any advantage over simple function calls.

## Operator Interface:

```java
Interface Operator<Chunk> {
	void open();        // Functions as a constructor

	Chunk next();
	boolean hasNext();
 
	void close();       // Functions as a destructor
}
```

### What happens in the `next()` - call?

Each call to the `next()` returns the results in chunks where each chunk represents a subset of the entirie result.

The `next()` has to be implemented in a way that triggers exactly as much work as is required to form the next chunk of result.

### What happes in the `hasNext()` - call?

The sole purpose of the `hasNext()` function is to determine whether the iterator can deliver the next chunk of the result or has it already exhausted.

### What is a chunk?

A chunk represents a subset of a meaningful intermediate result on which a function can do its computation. We can decide what a chunk represents, either a tuple, a horizontal parition, a horizontal partition of a column etc.
