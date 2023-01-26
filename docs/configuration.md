# Configuration parameters

Use the following command to view available configuration parameters in the `pg_stat_monitor_settings` view:

```sql
SELECT * FROM pg_stat_monitor_settings;
```

To amend the `pg_stat_monitor` configuration, use the General Configuration Unit (GCU) system. 

There are two types of GUC variables:

The first type can only be set in the `postgresql.conf` configuration file and comes into an effect on the start of the `postgres` server. You can set the same variable using the [`ALTER SYSTEM`](https://www.postgresql.org/docs/current/sql-altersystem.html) command. In this case the value of this variable is written into the `postgresql.auto.conf` which has preference over `postgresql.conf`. You also must restart the `postgres` server to apply the values from the `postgresql.auto.conf` file.  The user can change the variable using the [`SET`](https://www.postgresql.org/docs/current/sql-set.html) command without any error, but that does not affect the variable settings.

The second type of GUC variables can be set by the user from the client (`psql`) using the SET command. These variables are session-based, and their values can only be visible on that sessions. These variables can also be set with the or ALTER SYSTEM command and in the configuration file, but in that case, the effect of these variables is on all new sessions.


The following table shows setup options for each configuration parameter and whether the server restart is required to apply the parameter's value:

| Parameter name                                | SET | ALTER SYSTEM SET  |  server restart   | configuration reload
| ----------------------------------------------|-----|-------------------|-------------------|---------------------
| [pg_stat_monitor.pgsm_max](#pg_stat_monitorpgsm_max) | :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_query_max_len](#pg_stat_monitorpgsm_query_max_len)            | :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_track_utility](#pg_stat_monitorpgsm_track_utility)            | :white_check_mark: |:white_check_mark: |:x: | :white_check_mark:
| [pg_stat_monitor.pgsm_normalized_query](#pg_stat_monitorpgsm_normalized_query)         | :white_check_mark: |:white_check_mark: |:x: | :white_check_mark:
| [pg_stat_monitor.pgsm_max_buckets](#pg_stat_monitorpgsm_max_buckets)              | :x:                |:x:                |:white_check_mark: | :white_check_mark:
| [pg_stat_monitor.pgsm_bucket_time](#pg_stat_monitorpgsm_bucket_time)              |  :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_min](#pg_stat_monitorpgsm_histogram_min) |  :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_max](#pg_stat_monitorpgsm_histogram_max)        |  :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_buckets](#pg_stat_monitorpgsm_histogram_buckets)   | :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_query_shared_buffer](#pg_stat_monitorpgsm_query_shared_buffer)      | :x:                |:x:                |:white_check_mark: | :x:
| [pg_stat_monitor.pgsm_overflow_target](#pg_stat_monitorpgsm_overflow_target) |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_enable_query_plan](#pg_stat_monitorpgsm_enable_query_plan)  |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_track](#pg_stat_monitorpgsm_track) |  :x:  |  :x:  |   :x:  | :white_check_mark: |
| [pg_stat_monitor.pgsm_extract_comments](#pg_stat_monitorpgsm_extract_comments)|  :x:  |  :x:  |   :x:  | :white_check_mark: |
| [pg_stat_monitor.pgsm_track_planning](#pg_stat_monitorpgsm_track_planning) |   :x:  |  :x:  |   :white_check_mark: |  :x:  |

## Parameters description:

### pg_stat_monitor.pgsm_max

Values:

- Min: 1
- Max: 1000
- Default: 100

Server restart - YES.

This parameter defines the limit of shared memory (in MB) for ``pg_stat_monitor``. This memory is used by buckets in a circular manner. The memory is divided between the buckets equally, at the start of the PostgreSQL. 

### pg_stat_monitor.pgsm_query_max_len

Values:

- Min: 1024
- Max: 2147483647
- Default: 2048

Server restart - YES.

Sets the maximum size of the query. This parameter can only be set at the start of PostgreSQL. For long queries, the query is truncated to that particular length. This is to avoid unnecessary usage of shared memory. 


### pg_stat_monitor.pgsm_track_utility

Type: boolean (YES / NO). Default: YES

Server restart - NO

This parameter controls whether utility commands are tracked by the module. Utility commands are all those other than ``SELECT``, ``INSERT``, ``UPDATE``, and ``DELETE``. Starting with version 1.1.0, the values are changed from 1 / 0 to YES / NO.

### pg_stat_monitor.pgsm_normalized_query

Type: boolean. Default: NO

Server restart - NO

Starting with version 1.1.0, the data type changed to boolean and the query shows the actual parameter instead of the placeholder by default. It is quite useful when users want to use that query and try to run that query to check the abnormalities. Users should be aware, however, that running queries with disabled normalization can expose some sensitive data. 

But in most cases users like the queries with a placeholder. This parameter is used to toggle between the two said options.

In version 1.0.0 and earlier, the parameter type is "integer" and the default value is 1. This means that the query shows placeholders instead of the actual data

### pg_stat_monitor.pgsm_max_buckets

Values:

- Min: 1
- Max: 10
- Default: 10

Server restart - YES.

``pg_stat_monitor`` accumulates the information in the form of buckets. All the aggregated information is bucket based. This parameter is used to set the number of buckets the system can have. For example, if this parameter is set to 2, then the system will create two buckets. First, the system will add all the information into the first bucket. After its lifetime (defined in the [pg_stat_monitor.pgsm_bucket_time](#pg-stat-monitorpgsm-bucket-time) parameter) expires, it will switch to the second bucket, reset all the counters and repeat the process.


### pg_stat_monitor.pgsm_bucket_time

Values:

- Min: 1
- Max: 2147483647
- Default: 60

Server restart - YES.

This parameter is used to set the lifetime of the bucket. System switches between buckets on the basis of [pg_stat_monitor.pgsm_bucket_time](#pg-stat-monitorpgsm-bucket-time).

### pg_stat_monitor.pgsm_histogram_min

Values:

- Min: 1
- Max: 2147483647
- Default: 1

Server restart - YES.

``pg_stat_monitor`` also stores the execution time histogram. This parameter is used to set the lower bound of the histogram (in ms).

### pg_stat_monitor.pgsm_histogram_max

Values:

- Min: 10
- Max: 2147483647
- Default: 10000

Server restart - YES.

This parameter sets the upper bound of the execution time histogram (in ms). 

### pg_stat_monitor.pgsm_histogram_buckets

Values:

- Min: 2
- Max: 50
- Default: 20

Server restart - YES.

This parameter sets the maximum number of histogram buckets. Starting with version 1.1.0, the maximum value is changed to 50.

Starting with version 2.0.0, on server startup `pg_stat_monitor` calculates the maximum number of buckets that can be created for the time range specified in `pgsm_histogram_min`/`pgsm_histogram_max` and compares it with the number of buckets specified by the user. If the calculated number falls below the user configuration, the warning is written to the server log.

### pg_stat_monitor.pgsm_query_shared_buffer

Values:

- Min: 1
- Max: 10000
- Default: 20

Server restart - YES.

This parameter defines the shared memory limit (in MB) allocated for a query tracked by ``pg_stat_monitor``. 

### pg_stat_monitor.pgsm_overflow_target

Type: boolean (YES / NO). Default: NO
Server restart - NO.

Sets the overflow target for the `pg_stat_monitor`. Starting with version 1.10, the default value is NO. In version 1.0.0 and earlier, the default value was 1 (YES).

### pg_stat_monitor.pgsm_enable_query_plan

Type: boolean. Default: no

Server restart - NO.

Enables or disables query plan monitoring. When the `pgsm_enable_query_plan` is disabled (no), the query plan will not be captured by `pg_stat_monitor`. Enabling it may adversely affect the database performance. 

### pg_stat_monitor.pgsm_track

Default: top.

Server restart - NO.

This parameter controls which statements are tracked by `pg_stat_monitor`. 

Values: 

- `top`: Default, track only top level queries (those issued directly by clients) and excludes listing nested statements (those called within a function).
- `all`: Track top along with sub/nested queries. As a result, some SELECT statement may be shown as duplicates. 
- `none`: Disable query monitoring. The module is still loaded and is using shared memory, etc. It only silently ignores the capturing of data.


### pg_stat_monitor.pgsm_extract_comments

Type: boolean (YES/NO). Default: NO

Server restart - NO

This parameter controls whether to enable or disable extracting comments from queries.

### pg_stat_monitor.pgsm_track_planning

Type: boolean. Default: no

Server restart - YES

Available for PostgreSQL 14 and later versions. 

This parameter instructs ``pg_stat_monitor`` to monitor query planning statistics. 
