# `pg_stat_monitor` view reference

`pg_stat_monitor` provides a view where the statistics data is displayed. To see all available columns, run the following command from `psql`:

```sql
\d pg_stat_monitor;
```

Some column names differ depending on the PostgreSQL version. 

## PostgreSQL 17

The following table describes the `pg_stat_monitor` view for PostgreSQL 17 and higher versions.

???+ admonition "PostgreSQL 17"

     | Column             |  Type                    | Description
     |--------------------|--------------------------|------------------
     | bucket              | bigint                  | Data collection unit. The number shows what bucket in a chain a record belongs to
     | bucket_start_time    | timestamp with time zone | The start time of the bucket|
     | userid               | oid                  | An ID of the user who run a query |
     | username             | text                | The name of the user who run a query|
     | dbid                 | oid                  | the ID of the database where the query was executed
     | datname              | text                        | The name of a database where the query was executed
     | client_ip          | inet                       | The IP address of a client that run the query
     | pgsm_query_id       | bigint                   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. It is calculated on the normalized query text. Comments within the query text are ignored and all spaces within the query text are normalized to a single space character before calculating the query hash. The `pgsm_query_id` provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior, however, it affects the database performance. When needed, it can be disabled with the ` pg_stat_monitor.pgsm_enable_pgsm_query_id` configuration parameter
     | toplevel             | bool                     | True means that a query was executed as a top-level statement
     | top_queryid        | bignit             | The internal hash code serving to identify a top query in a statement|
     | query              | text                       | The actual text of the query |
     | comments           | text                       | Comments about the query
     | planid             | text                       | An internally generated ID of a query plan
     | query_plan         | text                       | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
     | top_query          | text                       | Shows the top query used in a statement |
     | application_name   | text                       | Shows the name of the application connected to the database
     | relations          | text[]                     | The list of tables involved in the query
     | cmd_type           | integer                    | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
     | cmd_type_text      | text                       | The type of the query executed
     | elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)
     | sqlcode            | text                       | SQL error code
     | message            | text                       | The error message
     | bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
     | plans              | bigint                     | The number of times the statement was planned
     | total_plan_time    | double precision           | The total time (in ms) spent on planning the statement
     | min_plan_time      | double precision           | Minimum time (in ms) spent on planning the statement
     | max_plan_time      | double precision           | Maximum time (in ms) spent on planning the statement
     | mean_plan_time     | double precision           | The mean (average) time (in ms) spent on planning the statement
     | stddev_plan_time   | double precision           | The standard deviation of time (in ms) spent on planning the statement
     | calls              | bigint                     | The number of times a particular query was executed
     | total_exec_time    | double precision           | The total time (in ms) spent on executing a query
     | min_exec_time           | double precision  | The minimum time (in ms) it took to execute a query
     | max_exec_time           | double precision           | The maximum time (in ms) it took to execute a query
     | mean_exec_time          | double precision           | The mean (average) time (in ms) it took to execute a query
     | stddev_exec_time        | double precision           | The standard deviation of time (in ms) spent on executing a query
     | rows                    | bigint                     | The number of rows retrieved when executing a query
     | shared_blks_hit    | bigint                     | Shows the total number of shared memory blocks returned from the cache
     | shared_blks_read   | bigint                     | Shows the total number of shared blocks returned not from the cache
     | shared_blks_dirtied | bigint                     | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     | shared_blks_written | bigint                     | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
     | local_blks_hit     | bigint                      | The number of blocks which are considered as local by the backend and thus are used for temporary tables
     | local_blks_read    | bigint                      | Total number of local blocks read during the query execution
     | local_blks_dirtied | bigint                      | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     | local_blks_written | bigint                      | Total number of local blocks  written simultaneously to a drive during the query execution
     | temp_blks_read     | bigint                      | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
     | temp_blks_written  | bigint                      | Total number of blocks of temporary files written to a drive
     | shared_blk_read_time      | double precision            | Total waiting time (in ms) for reading shared blocks
     | shared_blk_write_time     | double precision            | Total waiting time (in ms) for writing shared blocks to a drive
     | local_blk_read_time       | double precision  | Total time the statement spent on reading local blocks, in ms
     | local_blk_write_time      | double precision  | Total time the statement spent on writing local blocks, in ms
     | temp_blk_read_time | double precision            | Total number of temp blocks read by the statement 
     | temp_blk_write_time| double precision            | Total number of temp blocks written by the statement
     | resp_calls         | text[]                      | Call histogram
     | cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
     | cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
     | wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
     | wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
     | wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
     | jit_functions        | bigint               | Total number of functions JIT-compiled (Just-in-Time compiled) by the statement
     | jit_generation_time  | double precision     | Total time spent by the statement on generating JIT code, in milliseconds
     | jit_inlining_count   | bigint               | The number of times functions have been inlined
     | jit_inlining_time    | double precision     | Total time spent by the statement on inlining functions, in milliseconds
     | jit_optimization_count| bigint              | Number of times the statement has been optimized
     | jit_optimization_time| double precision     | Total time spent by the statement on optimizing, in milliseconds 
     | jit_emission_count   | bigint               | Number of times code has been emitted
     | jit_emission_time    | double precision     | Total time spent by the statement on emitting code, in milliseconds
     | jit_deform_count     | bigint               | Total number of tuple deform functions JIT-compiled by the statement | 
     jit_deform_time       | double precision | Total time spent by the statement on JIT-compiling tuple deform functions, in milliseconds | 
     | stats_since         | timestamp with time zone| Time at which statistics gathering started for this statement|
     | minmax_stats_since  | timestamp with time zone| Time at which min/max statistics gathering started for this statement (fields min_plan_time, max_plan_time, min_exec_time and max_exec_time) |
     

## PostgreSQL 16

??? admonition "PostgreSQL 16"

     | Column             |  Type                    | Description
     |--------------------|--------------------------|------------------
     | bucket              | bigint                  | Data collection unit. The number shows what bucket in a chain a record belongs to
     | bucket_start_time    | timestamp with time zone | The start time of the bucket|
     | userid               | oid                  | An ID of the user who run a query |
     | username             | text                | The name of the user who run a query|
     | dbid                 | oid                  | the ID of the database where the query was executed
     | datname              | text                        | The name of a database where the query was executed
     | client_ip          | inet                       | The IP address of a client that run the query
     | pgsm_query_id       | bigint                   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. It is calculated on the normalized query text. Comments within the query text are ignored and all spaces within the query text are normalized to a single space character before calculating the query hash. The `pgsm_query_id` provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior, however, it affects the database performance. When needed, it can be disabled with the ` pg_stat_monitor.pgsm_enable_pgsm_query_id` configuration parameter
     | toplevel             | bool                     | True means that a query was executed as a top-level statement
     | top_queryid        | bignit             | The internal hash code serving to identify a top query in a statement|
     | query              | text                       | The actual text of the query |
     | comments           | text                       | Comments about the query
     | planid             | text                       | An internally generated ID of a query plan
     | query_plan         | text                       | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
     | top_query          | text                       | Shows the top query used in a statement |
     | application_name   | text                       | Shows the name of the application connected to the database
     | relations          | text[]                     | The list of tables involved in the query
     | cmd_type           | integer                    | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
     | cmd_type_text      | text                       | The type of the query executed
     | elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)
     | sqlcode            | text                       | SQL error code
     | message            | text                       | The error message
     | bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
     | plans              | bigint                     | The number of times the statement was planned
     | total_plan_time    | double precision           | The total time (in ms) spent on planning the statement
     | min_plan_time      | double precision           | Minimum time (in ms) spent on planning the statement
     | max_plan_time      | double precision           | Maximum time (in ms) spent on planning the statement
     | mean_plan_time     | double precision           | The mean (average) time (in ms) spent on planning the statement
     | stddev_plan_time   | double precision           | The standard deviation of time (in ms) spent on planning the statement
     | calls              | bigint                     | The number of times a particular query was executed
     | total_exec_time    | double precision           | The total time (in ms) spent on executing a query
     | min_exec_time           | double precision  | The minimum time (in ms) it took to execute a query
     | max_exec_time           | double precision           | The maximum time (in ms) it took to execute a query
     | mean_exec_time          | double precision           | The mean (average) time (in ms) it took to execute a query
     | stddev_exec_time        | double precision           | The standard deviation of time (in ms) spent on executing a query
     | rows                    | bigint                     | The number of rows retrieved when executing a query
     | shared_blks_hit    | bigint                     | Shows the total number of shared memory blocks returned from the cache
     | shared_blks_read   | bigint                     | Shows the total number of shared blocks returned not from the cache
     | shared_blks_dirtied | bigint                     | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     | shared_blks_written | bigint                     | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
     | local_blks_hit     | bigint                      | The number of blocks which are considered as local by the backend and thus are used for temporary tables
     | local_blks_read    | bigint                      | Total number of local blocks read during the query execution
     | local_blks_dirtied | bigint                      | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
     | local_blks_written | bigint                      | Total number of local blocks  written simultaneously to a drive during the query execution
     | temp_blks_read     | bigint                      | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
     | temp_blks_written  | bigint                      | Total number of blocks of temporary files written to a drive
     | blk_read_time      | double precision            | Total waiting time (in ms) for reading blocks
     | blk_write_time     | double precision            | Total waiting time (in ms) for writing blocks to a drive
     | temp_blk_read_time | double precision            | Total number of temp blocks read by the statement 
     | temp_blk_write_time| double precision            | Total number of temp blocks written by the statement
     | resp_calls         | text[]                      | Call histogram
     | cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
     | cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
     | wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
     | wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
     | wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
     | jit_functions        | bigint               | Total number of functions JIT-compiled (Just-in-Time compiled) by the statement
     | jit_generation_time  | double precision     | Total time spent by the statement on generating JIT code, in milliseconds
     | jit_inlining_count   | bigint               | The number of times functions have been inlined
     | jit_inlining_time    | double precision     | Total time spent by the statement on inlining functions, in milliseconds
     | jit_optimization_count| bigint              | Number of times the statement has been optimized
     | jit_optimization_time| double precision     | Total time spent by the statement on optimizing, in milliseconds 
     | jit_emission_count   | bigint               | Number of times code has been emitted
     | jit_emission_time    | double precision     | Total time spent by the statement on emitting code, in milliseconds

## PostgreSQL 15

??? admonition "PostgreSQL 15"

     | Column             |  Type                    | Description
     |--------------------|--------------------------|------------------
      bucket              | bigint                  | Data collection unit. The number shows what bucket in a chain a record belongs to
     bucket_start_time    | timestamp with time zone | The start time of the bucket|
     userid               | oid                  | An ID of the user who run a query |
     user                 | regrole              | The name of the user who run a query
     dbid                 | oid                  | the ID of the database where the query was executed
     datname              | text                        | The name of a database where the query was executed
     toplevel             | bool                     | True means that a query was executed as a top-level statement
     client_ip          | inet                       | The IP address of a client that run the query
     pgsm_query_id       | bigint                   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. It is calculated on the normalized query text. Comments within the query text are ignored and all spaces within the query text are normalized to a single space character before calculating the query hash. The `pgsm_query_id` provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior, however, it affects the database performance. When needed, it can be disabled with the ` pg_stat_monitor.pgsm_enable_pgsm_query_id` configuration parameter
     queryid            | bigint                       | The internal hash code serving to identify every query in a statement
     top_queryid        | bignit             | The internal hash code serving to identify a top query in a statement|
     planid             | text                       | An internally generated ID of a query plan
     query_plan         | text                       | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
     top_query          | text                       | Shows the top query used in a statement |
     query              | text                       | The actual text of the query |
     comments           | text                       | Comments about the query
     application_name   | text                       | Shows the name of the application connected to the database
     relations          | text[]                     | The list of tables involved in the query
     cmd_type           | integer                    | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
     cmd_type_text      | text                       | The type of the query executed
     elevel             | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)
     sqlcode            | text                       | SQL error code
     message            | text                       | The error message
     bucket_done        | boolean                    | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
     plans              | bigint                     | The number of times the statement was planned
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
     rows                    | bigint                     | The number of rows retrieved when executing a query
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
     temp_blk_read_time | double precision            | Total number of temp blocks read by the statement 
     temp_blk_write_time| double precision            | Total number of temp blocks written by the statement
     resp_calls         | text[]                      | Call histogram
     cpu_user_time      | double precision            | The time (in ms) the CPU spent on running the query
     cpu_sys_time       | double precision            | The time (in ms) the CPU spent on executing the kernel code
     wal_records         | bigint                | The total number of WAL (Write Ahead Logs) generated by the query
     wal_fpi             | bigint                | The total number of WAL FPI (Full Page Images) generated by the query
     wal_bytes           | numeric               | Total number of bytes used for the WAL generated by the query
     jit_functions        | bigint               | Total number of functions JIT-compiled (Just-in-Time compiled) by the statement
     jit_generation_time  | double precision     | Total time spent by the statement on generating JIT code, in milliseconds
     jit_inlining_count   | bigint               | The number of times functions have been inlined
     jit_inlining_time    | double precision     | Total time spent by the statement on inlining functions, in milliseconds
     jit_optimization_count| bigint              | Number of times the statement has been optimized
     jit_optimization_time| double precision     | Total time spent by the statement on optimizing, in milliseconds 
     jit_emission_count   | bigint               | Number of times code has been emitted
     jit_emission_time    | double precision     | Total time spent by the statement on emitting code, in milliseconds


## PostgreSQL 14

The following is the view for PostgreSQL 14

??? admonition "PostgreSQL 14"
    
    | Column             |  Type                    | Description
    |--------------------|--------------------------|------------------
    | bucket             | bigint                   | Data collection unit. The number shows what bucket in a chain a record belongs to
    | bucket_start_time  | timestamp with time zone | The start time of the bucket|
    | userid             | oid      | An ID of the user who run a query |
    | username           | text     | The name of the user who run a query
    | dbid               | oid      | the ID of the database where the query was executed
    | datname            | text     | The name of a database where the query was executed
    | client_ip          | inet     | The IP address of a client that run the query
    | pgsm_query_id      | bigint   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. This provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior
    | queryid            | bigint   | The internal hash code serving to identify every query in a statement
    | toplevel           | boolean  | True means that a query was executed as a top-level statement
    | top_queryid        | bigint   | The internal hash code serving to identify a top query in a statement|
    | query              | text     | The actual text of the query |
    | comments           | text     | Comments about the query
    | planid             | bigint   | An internally generated ID of a query plan
    | query_plan         | text     | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
    | top_query          | text     | Shows the top query used in a statement |
    | application_name   | text     | Shows the name of the application connected to the database
    | relations          | text[]   | The list of tables involved in the query
    | cmd_type           | integer  | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
    | cmd_type_text      | text     | The type of the query executed
    | elevel             | integer  | Shows the error level of a query (WARNING, ERROR, LOG)
    | sqlcode            | text     | SQL error code
    | message            | text     | The error message
    | calls              | bigint   | The number of times a particular query was executed 
    | total_exec_time    | double precision | The total time (in ms) spent on executing a query
    | min_exec_time      | double precision | The minimum time (in ms) it took to execute a query
    | max_exec_time      | double precision | The maximum time (in ms) it took to execute a query
    | mean_exec_time     | double precision | The mean (average) time (in ms) it took to execute a query 
    | stddev_exec_time   | double precision | The standard deviation of time (in ms) spent on executing a query
    | rows               | bigint       | The number of rows retrieved when executing a query
    | shared_blks_hit    | bigint       | Shows the total number of shared memory blocks returned from the cache
    | shared_blks_read   | bigint       | Shows the total number of shared blocks returned not from the cache
    | shared_blks_dirtied | bigint      | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | shared_blks_written | bigint      | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
    | local_blks_hit     | bigint       | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    | local_blks_read    | bigint       | Total number of local blocks read during the query execution
    | local_blks_dirtied | bigint       | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | local_blks_written | bigint       | Total number of local blocks  written simultaneously to a drive during the query execution
    | temp_blks_read     | bigint       | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
    | temp_blks_written  | bigint       | Total number of blocks of temporary files written to a drive
    | blk_read_time      | double precision  | Total waiting time (in ms) for reading blocks
    | blk_write_time     | double precision  | Total waiting time (in ms) for writing blocks to a drive
    | resp_calls         | text[]       | Call histogram
    | cpu_user_time      | double precision  | The time (in ms) the CPU spent on running the query
    | cpu_sys_time       | double precision  | The time (in ms) the CPU spent on executing the kernel code
    | wal_records         | bigint      | The total number of WAL (Write Ahead Logs) generated by the query
    | wal_fpi             | bigint      | The total number of WAL FPI (Full Page Images) generated by the query
    | wal_bytes           | numeric     | Total number of bytes used for the WAL generated by the query
    | bucket_done        | boolean      | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
    | plans              | bigint  | The number of times the statement was planned
    | total_plan_time    | double precision | The total time (in ms) spent on planning the statement
    | min_plan_time      | double precision  | Minimum time (in ms) spent on planning the statement
    | max_plan_time      | double precision  | Maximum time (in ms) spent on planning the statement
    | mean_plan_time     | double precision  | The mean (average) time (in ms) spent on planning the statement
    | stddev_plan_time   | double precision  | The standard deviation of time (in ms) spent on planning the statement
    

## PostgreSQL 13

The following is the view for PostgreSQL 13
    
??? admonition "PostgreSQL 13"

    | Column             |  Type                    | Description
    |--------------------|--------------------------|------------------
    | bucket             | bigint                   | Data collection unit. The number shows what bucket in a chain a record belongs to
    | bucket_start_time  | timestamp with time zone | The start time of the bucket|
    | userid             | oid      | An ID of the user who run a query |
    | username           | text     | The name of the user who run a query
    | dbid               | oid      | the ID of the database where the query was executed
    | datname            | text     | The name of a database where the query was executed
    | client_ip          | inet     | The IP address of a client that run the query
    | pgsm_query_id      | bigint   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. This provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior
    | queryid            | bigint   | The internal hash code serving to identify every query in a statement
    | toplevel           | boolean  | True means that a query was executed as a top-level statement
    | top_queryid        | bigint   | The internal hash code serving to identify a top query in a statement|
    | query              | text     | The actual text of the query |
    | comments           | text     | Comments about the query
    | planid             | bigint   | An internally generated ID of a query plan
    | query_plan         | text     | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.
    | top_query          | text     | Shows the top query used in a statement |
    | application_name   | text     | Shows the name of the application connected to the database
    | relations          | text[]   | The list of tables involved in the query
    | cmd_type           | integer  | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
    | cmd_type_text      | text     | The type of the query executed
    | elevel             | integer  | Shows the error level of a query (WARNING, ERROR, LOG)
    | sqlcode            | text     | SQL error code
    | message            | text     | The error message
    | calls              | bigint   | The number of times a particular query was executed 
    | total_exec_time    | double precision | The total time (in ms) spent on executing a query
    | min_exec_time      | double precision | The minimum time (in ms) it took to execute a query
    | max_exec_time      | double precision | The maximum time (in ms) it took to execute a query
    | mean_exec_time     | double precision | The mean (average) time (in ms) it took to execute a query 
    | stddev_exec_time   | double precision | The standard deviation of time (in ms) spent on executing a query
    | rows               | bigint       | The number of rows retrieved when executing a query
    | shared_blks_hit    | bigint       | Shows the total number of shared memory blocks returned from the cache
    | shared_blks_read   | bigint       | Shows the total number of shared blocks returned not from the cache
    | shared_blks_dirtied | bigint      | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | shared_blks_written | bigint      | Shows the number of shared memory blocks written simultaneously to a drive during the query execution
    | local_blks_hit     | bigint       | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    | local_blks_read    | bigint       | Total number of local blocks read during the query execution
    | local_blks_dirtied | bigint       | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | local_blks_written | bigint       | Total number of local blocks  written simultaneously to a drive during the query execution
    | temp_blks_read     | bigint       | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
    | temp_blks_written  | bigint       | Total number of blocks of temporary files written to a drive
    | blk_read_time      | double precision  | Total waiting time (in ms) for reading blocks
    | blk_write_time     | double precision  | Total waiting time (in ms) for writing blocks to a drive
    | resp_calls         | text[]       | Call histogram
    | cpu_user_time      | double precision  | The time (in ms) the CPU spent on running the query
    | cpu_sys_time       | double precision  | The time (in ms) the CPU spent on executing the kernel code
    | wal_records         | bigint      | The total number of WAL (Write Ahead Logs) generated by the query
    | wal_fpi             | bigint      | The total number of WAL FPI (Full Page Images) generated by the query
    | wal_bytes           | numeric     | Total number of bytes used for the WAL generated by the query
    | bucket_done        | boolean      | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications
    | plans              | bigint  | The number of times the statement was planned
    | total_plan_time    | double precision | The total time (in ms) spent on planning the statement
    | min_plan_time      | double precision  | Minimum time (in ms) spent on planning the statement
    | max_plan_time      | double precision  | Maximum time (in ms) spent on planning the statement
    | mean_plan_time     | double precision  | The mean (average) time (in ms) spent on planning the statement
    | stddev_plan_time   | double precision  | The standard deviation of time (in ms) spent on planning the statement


## PostgreSQL 11 - 12

The following is the view for PostgreSQL 11 and 12:

??? admonition "PostgreSQL 11 and 12"

    | Column            |  Type                    | Description
    |-------------------|--------------------------|------------------
    | bucket            | bigint   | Data collection unit. The number shows what bucket in a chain a record belongs to|
    | bucket_start_time | timestamp with timezone  | The start time of the bucket|
    | userid            | oid      | An ID of the user who run a query |
    | username          | regrole  | The name of the user who run a query
    | dbid              | oid      | the ID of the database where the query was executed
    | datname           | text     | The name of a database where the query was executed
    | client_ip         | inet     | The IP address of a client that run the query|
    | pgsm_query_id     | bigint   | Generates a hash code to uniquely identify a query. The hash is independent of PostgreSQL server version, constants within the query, database, user or schema. This provides insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas. This also leads to more visibility into query performance behavior | 
    | queryid           | bigint   | The internal hash code serving to identify every query in a statement
    | top_queryid       | bigint   | The internal hash code serving to identify a top query in a statement|
    | query             | text     | The actual text of the query |
    | comments          | text     | Comments about the query |
    | planid            | bigint   | An internally generated ID of a query plan|
    | query_plan        | text     | The sequence of steps used to execute a query. This parameter is available only when the `pgsm_enable_query_plan` is enabled.|
    | top_query         | text     | Shows the top query used in a statement |
    | application_name  | text     | Shows the name of the application connected to the database|
    | relations         | text[]   | The list of tables involved in the query|
    | cmd_type          | integer  | The ID of the query type. <br> Supported values: 1 - SELECT; 2 - UPDATE; 3 - INSERT; 4 - DELETE.
    | cmd_type_text     | text     | The type of the query executed|
    | elevel            | integer                    | Shows the error level of a query (WARNING, ERROR, LOG)|
    | sqlcode           | text     | SQL error code
    | message           | text     | The error message
    | calls             | bigint   | The number of times a particular query was executed|
    | total_time        | double precision| The total time (in ms) spent on the statement
    | min_time          | double precision| Minimum time (in ms) spent on the statement
    | max_time          | double precision| Maximum time (in ms) spent on the statement
    | mean_time         | double precision| The mean (average) time (in ms) spent on the statement
    | stddev_time       | double precision| The standard deviation of time (in ms) spent on the statement|
    | rows              | bigint   | The number of rows retrieved when executing a query|
    | shared_blks_hit   | bigint   | Shows the total number of shared memory blocks returned from the cache|
    | shared_blks_read  | bigint   | Shows the total number of shared blocks returned not from the cache
    | shared_blks_dirtied| bigint  | Shows the number of shared memory blocks "dirtied" by the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)|
    | shared_blks_written| bigint  | Shows the number of shared memory blocks written simultaneously to a drive during the query execution|
    | local_blks_hit    | bigint   | The number of blocks which are considered as local by the backend and thus are used for temporary tables
    | local_blks_read   | bigint   | Total number of local blocks read during the query execution
    | local_blks_dirtied| bigint   | Total number of local blocks "dirtied" during the query execution (i.e. a query modified at least one tuple in a block and this block must be written to a drive)
    | local_blks_written | bigint  | Total number of local blocks  written simultaneously to a drive during the query execution
    | temp_blks_read    | bigint   | Total number of blocks of temporary files read from a drive. Temporary files are used when there's not enough memory to execute a query
    | temp_blks_written | bigint   | Total number of blocks of temporary files written to a drive
    | blk_read_time     | double precision | Total waiting time (in ms) for reading blocks
    | blk_write_time    | double precision | Total waiting time (in ms) for writing blocks to a drive
    | resp_calls        | text[]   | Call histogram
    |cpu_user_time      | double precision | The time (in ms) the CPU spent on running the query
    | cpu_sys_time      | double precision | The time (in ms) the CPU spent on executing the kernel code
    | bucket_done       | boolean  | Indicates whether the bucket is still active (false) or completed (true). If the bucket is active, more queries and stats could be added to this bucket. If the bucket is completed, the bucket is not active and no more queries nor stats can be added there, thus allowing accurate data display for monitoring applications

