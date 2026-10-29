---
title: "Data Layout"
weight: 2
---
**Underlying Question:**

How to speed up the system by improving its data throughput

**Learning Objectives:**

1. Data Layouts
2. Row, Column, Hybrid
3. Compression
4. Bitlists, Approximations etc

## What are the principal mapping steps to map a relation to a device?

**1a. linearize:**

To dimensional relations are mapped to a one dimensional sequence of values

**1b. serialize:**

a one dimensional sequence of values is ampped to bytes on virtual pages

**2. devirtualize:**

virtual pages are mapped to physical pages

**3. materialize:**

phyiscal pages are mapped to storage devices

# Table Layouts:

## Row Layout:

We linearize the tuples one by one.

We dont specify the ordering in which the tuples are ordered

We specify the ordering of the attributes inside the tuple

*Row Store:* Database stores that use Row Layout for linearization

*Why would we linearize the tuples in row layout?*

Row wise linearization is useful for queries that access a large number of attributes of the tuples but at the same time access only few rows

Useful for insert, delete and update operations

## Column Layout:

We linearize the attributes of columns one by one.

We dont specify the order in which the attributes are linearize

*Column Store:* Databases that store the data in Column Layout

*Why would we linearize the tuples in column layout?*

Column Layout is useful for queries that need to access a few attributes for each tuple but at the same time access a large number of tuples

Useful for analytical queries, grouping / aggregation

## Correlated columns:

Two columns are correlated if they store the attribute values of the rows in the same order.

Meaning in C1 if the values are store for rows in ascending order of the row id The same ordering is followed for storing the attribute values in C2

When accessing data from correlated columns we just access the data on the corresponding indexes as they belong to the same tuple

However its possible that the columns are not correlated. This means that we need to store the rowID with each attribute value in each column. Thus we attribute of the original table is now representedby a column having to values (explicit_id, actual_value). Thus tuple reconstruction joins get more expensive.

## Fractured Mirrors:

In this layout scheme we store the relation in both Row and Column Layout.

This is an attempt to get the best of both worlds but also brings in the worst of both worlds

This makes sense when some queries are executed against a row layout and others on the column layout. Since the data is available in both formats we can route the queries to the preferred layout

This layout requires more storage space and updates need to be made on both the layouts

## Vertical Partitioning / Column Grouping:

In this layout given a source relation, we can partition it vertically such that each vertical partition contains a subset of that source relation.

How that vertical partition is implemented in another story. A column ayout is a conceptually similar to a vertical paritioning of the source relation where aech partition containst one attribute each

The other extreme of the vertical partitioning is a row layout. there is only a single vertical partition containin all the attributes of the source relation

Queries that access a subset of attributes of the source relation where that subset contains more than one attribute may benefit from such a layout

In vertical partitioning there may be cases where we would like a subset of attributes to be part of more than one vertical partition. This introduces redundancy

## PAX Layout:

PAX LAyout can be considered as a heirarchical partitioning: at the top levell we apply horizontal paritioning and underneath at the leaf nodes we apply vertical partitioning

The sizes of the horizontal partition may be chosen to be equal to the database pages. We can layout the data differently within each page.

the overall system would behave unchanged wrt Page I/O's, however wrt reads we may observe some performance improvements as querues benefit from the column layout inside a page
