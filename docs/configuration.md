# Configuration parameters

Use the following command to view available configuration parameters in the `pg_stat_monitor_settings` view:

```sql
SELECT * FROM pg_stat_monitor_settings;
```

To amend the `pg_stat_monitor` configuration, use the General Configuration Unit (GCU) system. Some configuration parameters require the server restart and should be set before the server startup. These must be set in the `postgresql.conf` file. Other parameters do not require server restart and can be set permanently either in the `postgresql.conf` or from the client (`psql`) using the SET or ALTER SYSTEM SET commands.

The following table shows setup options for each configuration parameter and whether the server restart is required to apply the parameter's value:

| Parameter Name                                |  postgresql.conf   | SET | ALTER SYSTEM SET  |  server restart   | configuration reload
| ----------------------------------------------|--------------------|-----|-------------------|-------------------|---------------------
| [pg_stat_monitor.pgsm_max](#pg_stat_monitorpgsm_max) | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_query_max_len](#pg_stat_monitorpgsm_query_max_len)            | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_track_utility](#pg_stat_monitorpgsm_track_utility)            | :heavy_check_mark: | :heavy_check_mark: |:heavy_check_mark: |:x: | :heavy_check_mark:
| [pg_stat_monitor.pgsm_normalized_query](#pg_stat_monitorpgsm_normalized_query)         | :heavy_check_mark: | :heavy_check_mark: |:heavy_check_mark: |:x: | :heavy_check_mark:
| [pg_stat_monitor.pgsm_max_buckets](#pg_stat_monitorpgsm_max_buckets)              | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :heavy_check_mark:
| [pg_stat_monitor.pgsm_bucket_time](#pg_stat_monitorpgsm_bucket_time)              | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_min](#pg_stat_monitorpgsm_histogram_min) | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_max](#pg_stat_monitorpgsm_histogram_max)        | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_histogram_buckets](#pg_stat_monitorpgsm_histogram_buckets)   | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_query_shared_buffer](#pg_stat_monitorpgsm_query_shared_buffer)      | :heavy_check_mark: | :x:                |:x:                |:heavy_check_mark: | :x:
| [pg_stat_monitor.pgsm_overflow_target](#pg_stat_monitorpgsm_overflow_target) | :heavy_check_mark:   |  :x:  |  :x:  |   :heavy_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_enable_query_plan](#pg_stat_monitorpgsm_enable_query_plan)  | :heavy_check_mark:   |  :x:  |  :x:  |   :heavy_check_mark: |  :x:  |
| [pg_stat_monitor.track](#pg_stat_monitortrack) | :heavy_check_mark:   |  :x:  |  :x:  |   :x:  | :heavy_check_mark: |
| [pg_stat_monitor.extract_comments](#pg_stat_monitorextract_comments)| :heavy_check_mark:   |  :x:  |  :x:  |   :x:  | :heavy_check_mark: |
| [pg_stat_monitor.pgsm_track_planning](#pg_stat_monitorpgsm_track_planning) | :heavy_check_mark:   |  :x:  |  :x:  |   :heavy_check_mark: |  :x:  |

## Parameters description:

### pg_stat_monitor.pgsm_max

Values:
- Min: 1
- Max: 1000
- Default: 100


This parameter defines the limit of shared memory (in MB) for ``pg_stat_monitor``. This memory is used by buckets in a circular manner. The memory is divided between the buckets equally, at the start of the PostgreSQL. Requires the server restart.

### pg_stat_monitor.pgsm_query_max_len

Values:
- Min: 1024
- Max: 2147483647
- Default: 1024

Sets the maximum size of the query. This parameter can only be set at the start of PostgreSQL. For long queries, the query is truncated to that particular length. This is to avoid unnecessary usage of shared memory. Requires the server restart.


### pg_stat_monitor.pgsm_track_utility

Type: boolean. Default: 1

This parameter controls whether utility commands are tracked by the module. Utility commands are all those other than ``SELECT``, ``INSERT``, ``UPDATE``, and ``DELETE``.

### pg_stat_monitor.pgsm_normalized_query

Type: boolean. Default: 0

By default, the query shows the actual parameter instead of the placeholder. It is quite useful when users want to use that query and try to run that query to check the abnormalities. Users should be aware, however, that running queries with disabled normalization can expose some sensitive data. 

But in most cases users like the queries with a placeholder. This parameter is used to toggle between the two said options.

### pg_stat_monitor.pgsm_max_buckets

Values:
- Min: 1
- Max: 10
- Default: 10

``pg_stat_monitor`` accumulates the information in the form of buckets. All the aggregated information is bucket based. This parameter is used to set the number of buckets the system can have. For example, if this parameter is set to 2, then the system will create two buckets. First, the system will add all the information into the first bucket. After its lifetime (defined in the [pg_stat_monitor.pgsm_bucket_time](#pg-stat-monitorpgsm-bucket-time) parameter) expires, it will switch to the second bucket, reset all the counters and repeat the process.

Requires the server restart.

### pg_stat_monitor.pgsm_bucket_time

Values:
- Min: 1
- Max: 2147483647
- Default: 60

This parameter is used to set the lifetime of the bucket. System switches between buckets on the basis of [pg_stat_monitor.pgsm_bucket_time](#pg-stat-monitorpgsm-bucket-time).

Requires the server restart.

### pg_stat_monitor.pgsm_histogram_min

Values:
- Min: 0
- Max: 2147483647
- Default: 0

``pg_stat_monitor`` also stores the execution time histogram. This parameter is used to set the lower bound of the histogram (in ms).

Requires the server restart.

### pg_stat_monitor.pgsm_histogram_max

Values:
- Min: 10
- Max: 2147483647
- Default: 100000

This parameter sets the upper bound of the execution time histogram (in ms). Requires the server restart.

### pg_stat_monitor.pgsm_histogram_buckets

Values:
- Min: 2
- Max: 2147483647
- Default: 10

This parameter sets the maximum number of histogram buckets. Requires the server restart.

### pg_stat_monitor.pgsm_query_shared_buffer

Values:
- Min: 1
- Max: 10000
- Default: 20

This parameter defines the shared memory limit (in MB) allocated for a query tracked by ``pg_stat_monitor``. Requires the server restart.

### pg_stat_monitor.pgsm_overflow_target

Type: boolean. Default: 1

Sets the overflow target for the `pg_stat_monitor`. Requires the server restart.

### pg_stat_monitor.pgsm_enable_query_plan

Type: boolean. Default: 1

Enables or disables query plan monitoring. When the `pgsm_enable_query_plan` is disabled (0), the query plan will not be captured by `pg_stat_monitor`. Enabling it may adversely affect the database performance. Requires the server restart.

### pg_stat_monitor.track

This parameter controls which statements are tracked by `pg_stat_monitor`. 

Values: 

- `top`: Default, track only top level queries (those issued directly by clients) and excludes listing nested statements (those called within a function).
- `all`: Track top along with sub/nested queries. As a result, some SELECT statement may be shown as duplicates. 
- `none`: Disable query monitoring. The module is still loaded and is using shared memory, etc. It only silently ignores the capturing of data.

### pg_stat_monitor.extract_comments

Type: boolean. Default: 0

This parameter controls whether to enable or disable extracting comments from queries.

### pg_stat_monitor.pgsm_track_planning

Type: boolean. Default: 0

Available for PostgreSQL 14 and later versions. 

This parameter instructs ``pg_stat_monitor`` to monitor query planning statistics. Requires the server restart.