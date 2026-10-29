---
title: "Cheatsheet"
weight: 6
---
**Read Uncommited:**

READ uncommited

DONT WRITE uncommiteed

**Read Commited:**

DONT READ commited

**Repeatable Read:**

DONT ALLOW WRITE ON READ rows

**Serializable:**

No Concurrent executions on a common table

**RAID5:**

```rs
deviceID = LBA MOD (k-1);
internalBlockID = LBA DIV (k-1);
deviceID_parity = (internalBlockID+k-1) MOD k
if deviceID >= deviceID_parity:
		deviceID = deviceID + 1;
ret = [ (deviceID, internalBlockID, false) ];
ret.extend([ ( deviceID_parity, internalBlockID, true) ]);
return ret;
```

**What are characteristics of workloads where the following layouts typically perform well: row layout, colum layout, and PAX?**

- *Row layout:* Online Transaction Processing (OLTP), i.e. write/update-heavy workloads with queries that each refer to single tuples (point queries).
- *Column layout:* Online Analytical Processing (OLAP), i.e. analytical and compute-heavy workloads with queries that compute aggregates over attributes of multiple tuples (range queries).
- *PAX:* Any mixture of OLTP and OLAP.

Using PAX to arrange data on disk pages improves **inter-record spatial locality** (compared to a column layout) and improves data cache performance leading to fewer cache misses and lower number of stalled μ-ops.

Conditions for **REDO?**

if $\text{LSN} \lt \text{RecoveryLSN}$: NO REDO

if $\text{LSN} \le \text{SoD}$: NO REDO
