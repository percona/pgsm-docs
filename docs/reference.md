# `pg_stat_monitor` view reference

`pg_stat_monitor` provides a view where the statistics data is displayed. To see all available columns, run the following command from `psql`:

```sql
postgres=# \d pg_stat_monitor
```

Some column names differ depending on the PostgreSQL version. 

## PostgreSQL 15

The following table describes the `pg_stat_monitor` view for PostgreSQL 15 and higher versions.

???+ admonition "PostgreSQL 15"

     | Column             |  Type                    | Description
     |--------------------|--------------------------|------------------
      bucket              | bigint                  | Data collection unit. The number shows what bucket in a chain a record belongs to
     bucket_start_time    | timestamp with time zone | The start time of the bucket|
     userid               | regrole                  | An ID of the user who run a query |
     datname              | name                        | The name of a database where the query was executed
     toplevel             | bool                     | True means that a query was executed as a top-level statement
     client_ip          | inet                       | The IP address of a client that run the query
     queryid            | text                       | The internal hash code serving to identify every query in a statement
     top_queryid        | text             | The internal hash code serving to identify a top query in a statement|
     planid             | text                       | An internally generated ID of a query plan
     query_plan         | text                       | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
     top_query          | text                       | Shows the top query used in a statement |
     query              | text                       | The actual text of the query |
     comments           | text                       | Comments about the query
     application_name   | text                       | Shows the name of the application connected to the database
     relations          | text[]                     | The list of tables involved in the query
     cmd_type           | integer                    | Type of the query executed
     cmd_type_text      | text                       | The description of the query executed
     elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)
     sqlcode            | text                       | SQL error code
     message            | text                       | The error message
     bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
     plans_calls        | bigint                     | The number of times the statement was planned
     total_plan_time    | double precision           | The total time (in ms) spent on planning the statement
     min_plan_time      | double precision           | Minimum time (in ms) spent on planning the statement
     max_plan_time      | double precision           | Maximum time (in ms) spent on planning the statement
     mean_plan_time     | double precision           | The mean (average) time (in ms) spent on planning the statement
     stddev_plan_time   | double precision           | The standard deviation of time (in ms) spent on planning the statement
     calls              | bigint                     | The number of times a particular query was executed
     total_exec_time    | double precision           | The total time (in ms) spent on executing a query
     min_exec_time           | double precision  | The minimum time (in ms) it took to execute a query
     max_exec_time           | double precision           | The maximum time (in ms) it took to execute a query
     mean_exec_time          | double precision           | The mean (average) time (in ms) it took to execute a query
     stddev_exec_time        | double precision           | The standard deviation of time (in ms) spent on executing a query
     rows_retrieved     | bigint                     | The number of rows retrieved when executing a query
     shared_blks_hit    | bigint                     | Shows the total number of shared memory blocks returned from the cache
     shared_blks_read   | bigint                     | Shows the total number of shared blocks returned not from the cache
     shared_blks_dirtied | bigint                     | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     shared_blks_written | bigint                     | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
     local_blks_hit     | bigint                      | The number of blocks which are considered as local by the backend and thus are used for temporary tables
     local_blks_read    | bigint                      | Total number of local blocks read during the query execution
     local_blks_dirtied | bigint                      | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     local_blks_written | bigint                      | Total number of local blocks  written simultaneously to a drive during the query execution
     temp_blks_read     | bigint                      | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
     temp_blks_written  | bigint                      | Total number of blocks of temporary files written to a drive
     blk_read_time      | double precision            | Total waiting time (in ms) for reading blocks
     blk_write_time     | double precision            | Total waiting time (in ms) for writing blocks to a drive
     resp_calls         | text[]                      | Call histogram
     cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
     cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
     wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
     wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
     wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
     state_code          | bigint                | Shows the state code of a query
     state               | text                      | The state message 

## PostgreSQL 14

The following is the view for PostgreSQL 14

??? admonition "PostgreSQL 14"
    
    | Column             |  Type                    | Description
    |--------------------|--------------------------|------------------
     bucket              | bigint                  | Data collection unit. The number shows what bucket in a chain a record belongs to
    bucket_start_time    | timestamp with time zone | The start time of the bucket|
    userid               | regrole                  | An ID of the user who run a query |
    datname              | name                        | The name of a database where the query was executed
    toplevel             | bool                     | True means that a query was executed as a top-level statement
    client_ip          | inet                       | The IP address of a client that run the query
    queryid            | text                       | The internal hash code serving to identify every query in a statement
    top_queryid        | text             | The internal hash code serving to identify a top query in a statement|
    planid             | text                       | An internally generated ID of a query plan
    query_plan         | text                       | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
    top_query          | text                       | Shows the top query used in a statement |
    query              | text                       | The actual text of the query |
    comments           | text                       | Comments about the query
    application_name   | text                       | Shows the name of the application connected to the database
    relations          | text[]                     | The list of tables involved in the query
    cmd_type           | integer                    | Type of the query executed
    cmd_type_text      | text                       | The description of the query executed
    elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)
    sqlcode            | text                       | SQL error code
    message            | text                       | The error message
    bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
    plans_calls        | bigint                     | The number of times the statement was planned
    total_plan_time    | double precision           | The total time (in ms) spent on planning the statement
    min_plan_time      | double precision           | Minimum time (in ms) spent on planning the statement
    max_plan_time      | double precision           | Maximum time (in ms) spent on planning the statement
    mean_plan_time     | double precision           | The mean (average) time (in ms) spent on planning the statement
    stddev_plan_time   | double precision           | The standard deviation of time (in ms) spent on planning the statement
    calls              | bigint                     | The number of times a particular query was executed
    total_exec_time    | double precision           | The total time (in ms) spent on executing a query
    min_exec_time           | double precision  | The minimum time (in ms) it took to execute a query
    max_exec_time           | double precision           | The maximum time (in ms) it took to execute a query
    mean_time          | double precision           | The mean (average) time (in ms) it took to execute a query
    stddev_exec_time        | double precision           | The standard deviation of time (in ms) spent on executing a query
    rows_retrieved     | bigint                     | The number of rows retrieved when executing a query
    shared_blks_hit    | bigint                     | Shows the total number of shared memory blocks returned from the cache
    shared_blks_read   | bigint                     | Shows the total number of shared blocks returned not from the cache
    shared_blks_dirtied | bigint                     | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    shared_blks_written | bigint                     | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
    local_blks_hit     | bigint                      | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    local_blks_read    | bigint                      | Total number of local blocks read during the query execution
    local_blks_dirtied | bigint                      | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    local_blks_written | bigint                      | Total number of local blocks  written simultaneously to a drive during the query execution
    temp_blks_read     | bigint                      | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
    temp_blks_written  | bigint                      | Total number of blocks of temporary files written to a drive
    blk_read_time      | double precision            | Total waiting time (in ms) for reading blocks
    blk_write_time     | double precision            | Total waiting time (in ms) for writing blocks to a drive
    resp_calls         | text[]                      | Call histogram
    cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
    cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
    wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
    wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
    wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
    state_code          | bigint                | Shows the state code of a query
    state               | text                      | The state message 


## PostgreSQL 13

The following is the view for PostgreSQL 13
    
??? admonition "PostgreSQL 13"

    |      Column         |       Type       | Description
    |---------------------|------------------|------------------------------|
    | bucket              | bigint           |Data collection unit. The number shows what bucket in a chain a record belongs to| 
    | bucket_start_time   | text             | The start time of the bucket|
    | userid              | regrole          | An ID of the user who run a query|
    | datname             | name             | The name of a database where the query was executed|
    | client_ip           | inet             | The IP address of a client that run the query|
    | queryid             | text             | The internal hash code serving to identify every query in a statement|
    | top_queryid         | text             | The internal hash code serving to identify a top query in a statement|
    | query               | text             | The actual text of the query
    | comments            | text             | Comments about the query
    | planid              | text             | An internally generated ID of a query plan|
    | query_plan          | text             | The sequence of steps used to execute a query. This parameter is available only when the pgsm_enable_query_plan is enabled.|
    | top_query           | text             | Shows the top query used in a statement
    | application_name    | text             | Shows the name of the application connected to the database
    | relations           | text[]           | The list of tables involved in the query
    |cmd_type             | integer          | Type of the query executed|
    |cmd_type_text        | text             | The description of the query executed|
    |elevel               | integer          | Shows the error level of a query (WARNING, ERROR, LOG)|
    |sqlcode              | text             | SQL error code
    |message              | text             | The error message
    |calls                | bigint           | The number of times a particular query was executed| 
    | total_exec_time     | double precision | The total time (in ms) spent on executing a query
    | min_exec_time       | double precision | The minimum time (in ms) it took to execute a query
    | max_exec_time       | double precision | The maximum time (in ms) it took to execute a query
    | mean_exec_time      | double precision | The mean (average) time (in ms) it took to execute a query
    | stddev_exec_time    | double precision | The standard deviation of time (in ms) spent on executing a query
    | rows_retrieved      | bigint           | The number of rows retrieved when executing a query
    | bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
    | plans_calls         | bigint           | The number of times the statement was planned
    | total_plan_time     | double precision | The total time (in ms) spent on planning the statement
    | min_plan_time       | double precision | Minimum time (in ms) spent on planning the statement
    | max_plan_time       | double precision | Maximum time (in ms) spent on planning the statement
    | mean_plan_time      | double precision | Mean (average) time (in ms) spent on planning the statement
    | stddev_plan_time    | double precision | The standard deviation of time (in ms) spent on planning the statement
    | shared_blks_hit     | bigint           | Shows the total number of shared memory blocks returned from the cache
    | shared_blks_read    | bigint           | Shows the total number of shared blocks returned not from the cache
    | shared_blks_dirtied | bigint           | Shows the number of shared memory blocks “dirtied” by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | shared_blks_written | bigint           | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
    | local_blks_hit      | bigint           | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    | local_blks_read     | bigint           | Total number of local blocks read during the query execution
    | local_blks_dirtied  | bigint           | Total number of local blocks “dirtied” during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | local_blks_written  | bigint           | Total number of local blocks written simultaneously to a drive during the query execution
    | temp_blks_read      | bigint           | Total number of blocks of temporary files read from a drive. Temporary files are used when there’s not enough memory to execute a query
    | temp_blks_written   | bigint           | Total number of blocks of temporary files written to a drive
    | blk_read_time       | double precision | Total waiting time (in ms) for reading blocks
    | blk_write_time      | double precision | Total waiting time (in ms) for writing blocks to a drive
    | resp_calls          | text[]           | Call histogram
    | cpu_user_time       | double precision | The time (in ms) the CPU spent on running the query
    | cpu_sys_time        | double precision | The time (in ms) the CPU spent on executing the kernel code
    | wal_records         | bigint           | The total number of WAL (Write Ahead Logs) generated by the query
    | wal_fpi             | bigint           | The total number of WAL FPI (Full Page Images) generated by the query
    | wal_bytes           | numeric          | Total number of bytes used for the WAL generated by the query
    | state_code          | bigint           | Shows the state code of a query
    | state               | text             | The state message


## PostgreSQL 11 - 12

The following is the view for PostgreSQL 11 and 12:

??? admonition "PostgreSQL 11 and 12"

    | Column             |  Type                    | Description
    |--------------------|--------------------------|------------------
    |bucket              | bigint                   | Data collection unit. The number shows what bucket in a chain a record belongs to|
    |bucket_start_time   | text                     | The start time of the bucket|
    |userid              | regrole                  | An ID of the user who run a query |
    |datname             | name                     | The name of a database where the query was executed
    |client_ip           | inet                     | The IP address of a client that run the query|
    |queryid             | text                     | The internal hash code serving to identify every query in a statement
    |top_queryid         | text                     | The internal hash code serving to identify a top query in a statement|
    |query               | text                       | The actual text of the query |
    |comments            | text                     |Comments about the query |
    |planid              | text                       | An internally generated ID of a query plan|
    |query_plan          | text                     | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.|
    |top_query           | text                     | Shows the top query used in a statement |
    |application_name   | text                      | Shows the name of the application connected to the database|
    |relations          | text[]                     | The list of tables involved in the query|
    |cmd_type           | integer                    | Type of the query executed|
    |cmd_type_text      | text                     | The description of the query executed|
    |elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)|
    |sqlcode            | text                    | SQL error code
    |message            | text                       | The error message
    |bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
    |calls              | bigint                     | The number of times a particular query was executed|
    |total_time    | double precision           | The total time (in ms) spent on the statement
    |min_time      | double precision           | Minimum time (in ms) spent on the statement
    |max_time      | double precision           | Maximum time (in ms) spent on the statement
    |mean_time     | double precision           | The mean (average) time (in ms) spent on the statement
    |stddev_time   | double precision           | The standard deviation of time (in ms) spent on the statement|
    |rows_retrieved     | bigint                     | The number of rows retrieved when executing a query|
    |shared_blks_hit    | bigint                     | Shows the total number of shared memory blocks returned from the cache|
    |shared_blks_read   | bigint                     | Shows the total number of shared blocks returned not from the cache
    |shared_blks_dirtied | bigint                     | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)|
    |shared_blks_written | bigint                     | Shows the number of shared memory blocks written simultaneously to a drive during the query execution|
    |local_blks_hit     | bigint                      | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    |local_blks_read    | bigint                      | Total number of local blocks read during the query execution
    |local_blks_dirtied | bigint                      | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    |local_blks_written | bigint                      | Total number of local blocks  written simultaneously to a drive during the query execution
    |temp_blks_read     | bigint                      | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
    |temp_blks_written  | bigint                      | Total number of blocks of temporary files written to a drive
    |blk_read_time      | double precision            | Total waiting time (in ms) for reading blocks
    |blk_write_time     | double precision            | Total waiting time (in ms) for writing blocks to a drive
    |resp_calls         | text[]                      | Call histogram
    |cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
    |cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
    |wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
    |wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
    |wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
    |state_code          | bigint                | Shows the state code of a query
    |state               | text                      | The state message 

