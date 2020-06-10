
# Architecture of TinyDB

Parser
  |
Optimizer   
  |
Executor
  |
Access
  |
Storage
  |
Utils(Memory management, data structurs, ...) 

## Parser
given a sql string, do lex & grammer & sanity analyze, produce a list of Query Tree 
API:
```c
List* parse(char *sql);
```
## Optimizer
given a Query, do query optimize, produce a Plan Tree 
```c
Plan* optimize(Query *query);
```
## Executor
given a Plan, do execution, produce result set
```c
Result* execute(Plan *plan);
```
supported execution operators include:

```c

```

## Access/Storage
provide access to tables/index
```c
Table*  open(char* name);
Row*    read(Table* table,  Row* key);
void    insert(Table* table, Row* result); 
void    delete(Table* table, Row* result); 
void    update(Table* table, Row* old, Row* new); 
void    flush(Table* table); // flush a table to disk
void    close(Table* table);
```
### table memory layout
each column of table is fixed size, for example
```
create table t1(col1 int, col2 double, col3 char(10));
-----------------------------------------------------------------
|HEADER|col1 int,size=4|col2 double,size=8|col3 char(10),size=10| 
-----------------------------------------------------------------
|HEADER|col1 int,size=4|col2 double,size=8|col3 char(10),size=10|
-----------------------------------------------------------------
|HEADER|col1 int,size=4|col2 double,size=8|col3 char(10),size=10|
-----------------------------------------------------------------

HEADER(row_id, is_deleted, null_bits[]) 
row_id: id of the row, start from 0
id_deleted: delete mark the row as deleted
null_bits[i]: indicate the column i is null

```
### table disk layout
ever n rows(default=1000) are compressed and written to a file
the disk layout and memory layout are the same

```
tablename.dat1 1-999 rows
tablename.dat2 1000-1999 rows
tablename.dat3 2000-2000 rows
```

### read

### insert

### update

### delete

### durability
the in memory buffer of table is flushed to disk upon flush().
because tinydb is single threaded , the flush is invoked after write is done.
the buffers flushed to disk is always consistent and does not contain partial write 
