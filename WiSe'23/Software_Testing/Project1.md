- create a function to generate new table names and store them
- create a function to generate new column names under a particular table and store this information

- [ ] fuzzing with generators
- [ ] a reasonable state setup that would give your fuzzer a head start is permitted.

Resources:
- https://www.sqlite.org/lang_expr.html

## Semantic Information:
### create-table-stmt:
- must specify a name for the new table
- table names that begin with `sqlite_` are reserved for internal use and results in an error.
- schema-name
	- must be either "main", "temp" or name of an ==attached database==
- It is an error to specify both a schema-name and the TEMP or TEMPORARY keyword unless schema-name is "temp"
- It is usually an error to attempt to create a new table in a database that already contains a table, index or view of the same name.
- 




## Changes:
- [x] ensure proper `schema-name` for  TEMP, TMEPORARY, schema-name from create-table-stmt
- [x] Get rid of the column constraints to ease the addition of data FOR NOW
- [x] Ensure the default value is not an expression
- [x] Do not add PRIMARY KEY constraint in the column-def
- [x] PRIMARY KEY clause can appear only once
- [ ] if WITHOUT ROWID then PRIMARY KEY must exist
- [ ] PRIMAY KEY column must have UNIQUE values
- [x] Columns for a table must be stored in the dict
- [x] WE need to order the expansions
- [x] We need post functions for `alter-stmt` grammar to update state.
- [x] How to create phases?
	- [x] Let the grammar have phases-1, phases-2...
	- [x] Let the fuzzer move through the phases while fuzzing
- [ ] Grammar
	- [x] create grammar for `create-view-stmt`
	- [ ] crete grammar for `insert-stmt`


- [ ] CreateTableProcessor
	- [ ] We dont allow column constraints, we just do table_constraints
	- [ ] Allow all columns to have default value. possibly makes insertion easier.

DEFAULT:
- NULL, 
- a string constant, 
- a blob constant, 
- a signed-number,
- any constant expression enclosed in parentheses. 
- CURRENT_TIME, 
- CURRENT_DATE,
-  CURRENT_TIMESTAMP,
- an expression is considered constant 
- if it contains no sub-queries, column or table references, [bound parameters](https://www.sqlite.org/lang_expr.html#varparam), or string literals enclosed in double-quotes instead of single-quotes.

- [ ] TableConstraintProcessor
	- [ ] How are we saving the declared type of column?
	- [ ] NOT NULL
	- [ ] CHECK
	- [ ] UNIQUE
	- [ ] PRIMARY KEY
	- [ ] FOREIGN KEY

#### Random ideas worth trying:
- [x] add a `id` primary key column on every table by default. allows easier insertions
- [ ] fix the number of columns of each table. allows using compound operator


### For insert-stmt:
- [x] just add default values
- [ ] store the types of the columns in create-table


### Doing now :
- [x] add the update_table_name to the `QueryProcessor`
- [x] add the update_table_name to the `CreateTableProcessor`
- [x] add `post` functions to `create_table_stmt` grammar
- [x] add `get_schema_name` to `QueryProcessor`
- [x] add `get_schema_name` to `CreateTableProcessor`




### Statements Covered:
- [x] alter-table-stmt
- [x] analyze-stmt
- [x] attach-stmt
- [x] begin-stmt
- [x] commit-stmt
- [x] create-index-stmt
- [x] create-table-stmt
- [ ] create-trigger-stmt
- [x] create-view-stmt
- [ ] create-virtual-table-stmt
- [ ] delete-stmt
- [ ] delete-stmt-limited
- [x] detach-stmt
- [x] drop-index-stmt
- [x] drop-table-stmt
- [ ] drop-trigger-stmt
- [x] drop-view-stmt
- [x] insert-stmt
- [x] pragma-stmt
- [ ] reindex-stmt
- [ ] release-stmt
- [x] rollback-stmt
- [ ] savepoint-stmt
- [x] select-stmt
- [x] update-stmt
- [ ] update-stmt-limited
- [x] vacuum-stmt
