---
title: "Crash Recovery"
weight: 5
---
**Underlying Question:**

How do we make a system robust against software and hardware crashes

**Learning Objective:**

crash recover: failover, hot standby, loging, redo, undo, ARIES, elasticity

# Introduction:

## What is recovery?

By recovery we meant hat we can get back to a consistent state of the database according to the ACID properties.

## What is the impact of failures on ACID?

Error scenarios affect the ataomicity i.e. only a part of the transaction is executed, and durability, i.e. only some of the actions of a transaction were made durable. These problems then affect the consistency of the database

## REDO:

Redo, in general means that we hace to reapply the actions to the database state as those actions were not reflected in the database state.

## UNDO:

Undo, in general means that we have to remove effects of actions done to the database state as these actions belong to an uncommited transaction

# Log based recovery:

## Stable Storage:

Stable storage is typically an additional storage device that is used to persist the log-file.

## Write Ahead Logging:

WAL introduces two constraints on the order of persisting data in the store and the log.

First, before persisting any change in the store, the log records that reflect the changes to the state of the database has to be first persisted.

Second, a transaction is considered committed only after all log records that correspond to that transaction are persisted on the stable storage.

## Physical Logging:

In physical logging a snapshot of the database state is logged. The *before image* logs the state os of before applying a change. The *after image* logs the state as of after applying a change.

This is only efficient if the amount of data recorded in the images is relatively small.

## Logical Logging:

Logical logging logs the transition between database states. We log the operation that is required to move from an older state to a newer state.

We use dufferent granules. One example is logging the entire SQL command. Another example is logging the transitions operating on atomic data values.

The transition operations may affect multiple pages. Think of this as *replace all* feature.

## Physiological Logging:

Logical logging is not restricted to a particular page. This means, the transition recorded in a logical logging may affect multiple pages. This is not the case for physiological logging: the transition may only affcet one particular page.

*log example:* `Page#42: update('intital', 'update');`

# ARIES:

There are three phases of ARIES:

1. Analysis Phase: construct meta data on dirty pages and non-committed transactions as of the time of the crash; compute where to start the Redo phase (the min dirty page LSN)
2. REDO Phase: We want to go to the database state when the crash occured. Thus, we repeat all actions including those from loser transactions and changes undone previously (CLRs).
3. UNDO Phase: Undo actions of all loser transactions; log these undos to special log records CLRs.

## What is the purpose of a Transaction Table (TT)?

The transaction table TT maintains the list of ongoing (uncommitted) transactions. This is useful to identify the loser transactions. **In TT, for each transaction we keep the last log record triggered by that transaction.** The entry for a transaction is removed when that transaction commits.

## What is the purpose of a Dirty Page Table (DPT)?

The Dirty Page Table maintains the list of pages in the database buffer which have not yet been written back to the database store.

This is useful in identifying the pages that were changed but not persisted in the store. Thus the changes on these pages has to be redone.

For each page in the DPT we store the first LSN that modified that page.

The entries for a page is removed when the page is written back to the store.

## CLRs - Compensation Log Records:

A compensation log record reflects a change done to the database store that removes the change done by a loser transaction earlier.
