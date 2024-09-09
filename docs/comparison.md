# Comparison with pg_stat_statements 

The `pg_stat_monitor` extension is developed on the basis of `pg_stat_statements`  as its more advanced replacement.

Thus, `pg_stat_monitor` inherits the columns available in `pg_stat_statements` plus provides additional ones.

Note that [`pg_stat_monitor` and `pg_stat_statements` process statistics data differently](index.md#how-pg_stat_monitor-works). Because of these differences, memory blocks and WAL (Write Ahead Logs) related statistics data are displayed inconsistently when both extensions are used together. 


To see all available columns, run the following command from the `psql` terminal:

```sql
\d pg_stat_monitor;
```

The following table compares the `pg_stat_monitor` (PGSM) view with that of `pg_stat_statements` (PGSS).

Note that the column names differ depending on the PostgreSQL version you are running.


| PGSM | PGSS | PostgreSQL ≥ 17 | PostgreSQL 15 - 16 | PostgreSQL 13 - 14 | PostgreSQL 11 - 12 |
|------|------|----------------|--------------------|--------------------|--------------------|
| :white_check_mark: | :x: | bucket              | bucket              | bucket              | bucket              |
| :white_check_mark: | :x: | bucket_start_time   | bucket_start_time   | bucket_start_time   | bucket_start_time   |
| :white_check_mark: | :white_check_mark: | userid       | userid            | userid             | userid             |
| :white_check_mark: | :x: | user                | user                | username            | username            |
| :white_check_mark: | :white_check_mark: | dbid         | dbid               | dbid                | dbid                |
| :white_check_mark: | :x: | datname             | datname             | datname             | datname             |
| :white_check_mark: | :x: | client_ip           | client_ip           | client_ip           | client_ip           |
| :white_check_mark: | :x: | pgsm_query_id       | pgsm_query_id       | pgsm_query_id       | pgsm_query_id       |
| :white_check_mark: | :white_check_mark: | queryid      | queryid            | queryid             | queryid             |
| :white_check_mark: | :white_check_mark: | toplevel     | toplevel           | toplevel            |                     |
| :white_check_mark: | :x: | top_queryid         | top_queryid         | top_queryid         | top_queryid         |
| :white_check_mark: | :x: | top_query           | top_query           | top_query           | top_query           |
| :white_check_mark: | :white_check_mark: | query        | query              | query               | query               |
| :white_check_mark: | :x: | planid              | planid              | planid              | planid              |
| :white_check_mark: | :x: | comments            | comments            | comments            | comments            |
| :white_check_mark: | :x: | query_plan          | query_plan          | query_plan          | query_plan          |
| :white_check_mark: | :x: | application_name    | application_name    | application_name    | application_name    |
| :white_check_mark: | :x: | relations           | relations           | relations           | relations           |
| :white_check_mark: | :x: | cmd_type            | cmd_type            | cmd_type            | cmd_type            |
| :white_check_mark: | :x: | cmd_type_text       | cmd_type_text       | cmd_type_text       | cmd_type_text       |
| :white_check_mark: | :x: | elevel              | elevel              | elevel              | elevel              |
| :white_check_mark: | :x: | sqlcode             | sqlcode             | sqlcode             | sqlcode             |
| :white_check_mark: | :x: | message             | message             | message             | message             |
| :white_check_mark: | :white_check_mark: | calls        | calls              | calls               | calls               |
| :white_check_mark: | :white_check_mark: | total_exec_time | total_exec_time    | total_exec_time     | total_time          |
| :white_check_mark: | :white_check_mark: | min_exec_time   | min_exec_time      | min_exec_time       | min_time            |
| :white_check_mark: | :white_check_mark: | max_exec_time   | max_exec_time      | max_exec_time       | max_time            |
| :white_check_mark: | :white_check_mark: | mean_exec_time  | mean_exec_time     | mean_exec_time      | mean_time           |
| :white_check_mark: | :white_check_mark: | stddev_exec_time| stddev_exec_time   | stddev_exec_time    | stddev_time         |
| :white_check_mark: | :white_check_mark: | rows           | rows               | rows                | rows                |
| :white_check_mark: | :white_check_mark: | plans          | plans              | plans               |                     |
| :white_check_mark: | :white_check_mark: | shared_blks_hit | shared_blks_hit    | shared_blks_hit     | shared_blks_hit     |
| :white_check_mark: | :white_check_mark: | shared_blks_read | shared_blks_read   | shared_blks_read    | shared_blks_read    |
| :white_check_mark: | :white_check_mark: | shared_blks_dirtied | shared_blks_dirtied | shared_blks_dirtied | shared_blks_dirtied |
| :white_check_mark: | :white_check_mark: | shared_blks_written | shared_blks_written | shared_blks_written | shared_blks_written |
| :white_check_mark: | :white_check_mark: | local_blks_hit | local_blks_hit      | local_blks_hit      | local_blks_hit      |
| :white_check_mark: | :white_check_mark: | local_blks_read | local_blks_read    | local_blks_read     | local_blks_read     |
| :white_check_mark: | :white_check_mark: | local_blks_dirtied | local_blks_dirtied | local_blks_dirtied | local_blks_dirtied |
| :white_check_mark: | :white_check_mark: | local_blks_written | local_blks_written | local_blks_written | local_blks_written |
| :white_check_mark: | :white_check_mark: | temp_blks_read   | temp_blks_read     | temp_blks_read      | temp_blks_read      |
| :white_check_mark: | :white_check_mark: | temp_blks_written | temp_blks_written | temp_blks_written   | temp_blks_written   |
| :white_check_mark: | :white_check_mark: | shared_blk_read_time | blk_read_time    | blk_read_time       | blk_read_time       |
| :white_check_mark: | :white_check_mark: | shared_blk_write_time | blk_write_time  | blk_write_time      | blk_write_time      |
| :white_check_mark: | :white_check_mark: | local_blk_read_time |                    |                     |                     |
| :white_check_mark: | :white_check_mark: | local_blk_write_time |                   |                     |                     |
| :white_check_mark: | :x: | resp_calls         | resp_calls          | resp_calls          | resp_calls          |
| :white_check_mark: | :white_check_mark: | cpu_user_time   | cpu_user_time      | cpu_user_time       | cpu_user_time       |
| :white_check_mark: | :white_check_mark: | cpu_sys_time    | cpu_sys_time       | cpu_sys_time        | cpu_sys_time        |
| :white_check_mark: | :x: | bucket_done        | bucket_done         | bucket_done         | bucket_done         |
| :white_check_mark: | :white_check_mark: | wal_records     | wal_records        | wal_records         |                     |
| :white_check_mark: | :white_check_mark: | wal_fpi         | wal_fpi            | wal_fpi             |                     |
| :white_check_mark: | :white_check_mark: | wal_bytes       | wal_bytes          | wal_bytes           |                     |
| :white_check_mark: | :white_check_mark: | total_plan_time | total_plan_time    | total_plan_time     |                     |
| :white_check_mark: | :white_check_mark: | min_plan_time   | min_plan_time      | min_plan_time       |                     |
| :white_check_mark: | :white_check_mark: | max_plan_time   | max_plan_time      | max_plan_time       |                     |
| :white_check_mark: | :white_check_mark: | mean_plan_time  | mean_plan_time     | mean_plan_time      |                     |
| :white_check_mark: | :white_check_mark: | stddev_plan_time| stddev_plan_time   | stddev_plan_time    |                     |
| :white_check_mark: | :white_check_mark: | temp_blk_read_time | temp_blk_read_time |                    |                     |
| :white_check_mark: | :white_check_mark: | temp_blk_write_time | temp_blk_write_time |                   |                     |
| :white_check_mark: | :white_check_mark: | jit_functions  | jit_functions     |                    |                     |
| :white_check_mark: | :white_check_mark: | jit_generation_time | jit_generation_time |                  |                     |
| :white_check_mark: | :white_check_mark: | jit_inlining_count | jit_inlining_count |                   |                     |
| :white_check_mark: | :white_check_mark: | jit_inlining_time | jit_inlining_time |                    |                     |
| :white_check_mark: | :white_check_mark: | jit_optimization_count | jit_optimization_count |                |                     |
| :white_check_mark: | :white_check_mark: | jit_optimization_time | jit_optimization_time |                 |                     |
| :white_check_mark: | :white_check_mark: | jit_emission_count | jit_emission_count |                    |                     |
| :white_check_mark: | :white_check_mark: | jit_emission_time | jit_emission_time |                     |                     |
| :white_check_mark: | :white_check_mark: | jit_deform_count |                     |                     |                     |
| :white_check_mark: | :white_check_mark: | jit_deform_time |                      |                     |                     |
| :white_check_mark: | :white_check_mark: | stats_since    |                      |                     |                     |
| :white_check_mark: | :white_check_mark: | minmax_stats_since|                   |                     |

To learn more about the features in `pg_stat_monitor`, please see the [User guide](user_guide.md).


Additional reading: [pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html)




[^1]: Available starting from PostgreSQL 14 and above
