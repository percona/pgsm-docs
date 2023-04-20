# Comparison with pg_stat_statements 

The `pg_stat_monitor` extension is developed on the basis of `pg_stat_statements`  as its more advanced replacement.

Thus, `pg_stat_monitor` inherits the columns available in `pg_stat_statements` plus provides additional ones.

Note that [`pg_stat_monitor` and `pg_stat_statements` process statistics data differently](index.md#how-pg_stat_monitor-works). Because of these differences, memory blocks and WAL (Write Ahead Logs) related statistics data are displayed inconsistently when both extensions are used together. 


To see all available columns, run the following command from the `psql` terminal:

```sql
 postgres=# \d pg_stat_monitor;
```

The following table compares the `pg_stat_monitor` view with that of `pg_stat_statements`.

Note that the column names differ depending on the PostgreSQL version you are running.


|PostgreSQL 15 and above| PostgreSQL 13 - 14 | PostgreSQL 11 - 12 |  pg_stat_monitor | pg_stat_statements
|-----------------------|--------------------|--------------------------|-----------------------------|----------------------
| bucket                | bucket              | bucket                  |  :white_check_mark: | :x:
| bucket_start_time |bucket_start_time    | bucket_start_time |  :white_check_mark: | :x:
| bucket_done           | bucket_done           | bucket_done  | :white_check_mark: | :x:
| userid                | userid             | userid        | :white_check_mark: | :white_check_mark:
| user  |   |    | :white_check_mark: | :x:
|dbid   |   |    | :white_check_mark: | :white_check_mark: 
| datname               | datname            | datname           |  :white_check_mark: | :white_check_mark:
| toplevel              | toplevel[^1]       |                   | :white_check_mark: | :white_check_mark:
| client_ip             | client_ip          | client_ip         | :white_check_mark:| :x:
| pgsm_query_id         | pgsm_query_id      | pgsm_query_id     | :white_check_mark:| :x:
| queryid               | queryid            | queryid            | :white_check_mark: | :white_check_mark:
| planid                | planid             | planid             | :white_check_mark:| :x:
| query_plan            |query_plan         | query_plan          | :white_check_mark: | :x:
| top_query             |top_query          | top_query         | :white_check_mark: | :x:
| top_queryid           | top_queryid        | top_queryid        | :white_check_mark: | :x:
| query                 | query              | query                 | :white_check_mark: | :white_check_mark:
| application_name      | application_name   | application_name   | :white_check_mark:| :x:
| relations             | relations          | relations          | :white_check_mark: | :x:
| cmd_type              | cmd_type           | cmd_type           | :white_check_mark: | :x:
| cmd_type_text         | cmd_type_text      | cmd_type_text      | :white_check_mark: | :x:
| elevel                | elevel             | elevel           | :white_check_mark: | :x:
| sqlcode               | sqlcode            | sqlcode | :white_check_mark: | :x:
| message               | message            | message | :white_check_mark: | :x:
| plans                 | plans_calls        | plans_calls  | :white_check_mark: | :white_check_mark:
| total_plan_time       | total_plan_time    |               | :white_check_mark: | :white_check_mark:
| min_plan_time         | min_plan_time      |               | :white_check_mark: | :white_check_mark:
| max_plan_time         | max_plan_time      |               | :white_check_mark: | :white_check_mark:
| mean_plan_time        | mean_plan_time     |               | :white_check_mark:  | :white_check_mark:
| stddev_plan_time      | stddev_plan_time   |               | :white_check_mark:  | :white_check_mark:
| calls                 | calls              | calls          | :white_check_mark: | :white_check_mark:
| total_exec_time       | total_exec_time    | total_time     | :white_check_mark: | :white_check_mark:
| min_exec_time         | min_exec_time      | min_time       | :white_check_mark: | :white_check_mark:
| max_exec_time         | max_exec_time      | max_time       | :white_check_mark: | :white_check_mark:
| mean_exec_time        | mean_exec_time     | mean_time      | :white_check_mark: | :white_check_mark:
| stddev_exec_time      | stddev_exec_time   | stddev_time | :white_check_mark: | :white_check_mark:
| rows                  | rows_retrieved     | rows_retrieved | :white_check_mark: | :white_check_mark:
| shared_blks_hit       | shared_blks_hit    | shared_blks_hit | :white_check_mark: | :white_check_mark:
| shared_blks_read      | shared_blks_read   | shared_blks_read | :white_check_mark: | :white_check_mark:
| shared_blks_dirtied   | shared_blks_dirtied | shared_blks_dirtied | :white_check_mark: | :white_check_mark:
| shared_blks_written   | shared_blks_written | shared_blks_written | :white_check_mark: | :white_check_mark:
| local_blks_hit        | local_blks_hit     | local_blks_hit | :white_check_mark: | :white_check_mark:
| local_blks_read       | local_blks_read    | local_blks_read  | :white_check_mark: | :white_check_mark:
| local_blks_dirtied    | local_blks_dirtied | local_blks_dirtied  | :white_check_mark: | :white_check_mark:
| local_blks_written    | local_blks_written | local_blks_written | :white_check_mark: | :white_check_mark:
| temp_blks_read        | temp_blks_read     | temp_blks_read | :white_check_mark: | :white_check_mark:
| temp_blks_written     | temp_blks_written  | temp_blks_written | :white_check_mark:  | :white_check_mark:
| blk_read_time         | blk_read_time      | blk_read_time | :white_check_mark: | :white_check_mark:
| blk_write_time        | blk_write_time     | blk_write_time | :white_check_mark: | :white_check_mark:
| temp_blk_read_time    |   |   | :white_check_mark: | :white_check_mark:
| temp_blk_write_time   |   |   | :white_check_mark: | :white_check_mark:
| resp_calls            | resp_calls         | resp_calls    | :white_check_mark: | :x:
| cpu_user_time      | cpu_user_time      | cpu_user_time | :white_check_mark: | :x:
| cpu_sys_time       | cpu_sys_time       | cpu_sys_time | :white_check_mark: | :x:
| wal_records         | wal_records         | wal_records | :x:  | :white_check_mark:
| wal_fpi             | wal_fpi             | wal_fpi     | :x:  | :white_check_mark:
| wal_bytes           | wal_bytes           | wal_bytes   | :x:  | :white_check_mark:
| comments            | comments    | comments           | :white_check_mark: | :x:
| jit_functions        |    |   | :white_check_mark: | :white_check_mark:
| jit_generation_time  |    |   | :white_check_mark: | :white_check_mark:
| jit_inlining_count   |    |   | :white_check_mark: | :white_check_mark:
| jit_inlining_time    |    |   | :white_check_mark: | :white_check_mark:
| jit_optimization_count |  |   | :white_check_mark: | :white_check_mark:
| jit_optimization_time  |  |   | :white_check_mark: | :white_check_mark:
| jit_emission_count     |  |   | :white_check_mark: | :white_check_mark:
| jit_emission_time    |    |   | :white_check_mark: | :white_check_mark:

To learn more about the features in `pg_stat_monitor`, please see the [User guide](user_guide.md).


Additional reading: [pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html)




[^1]: Available starting from PostgreSQL 14 and above
