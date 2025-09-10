# Configuration

Use the following command to view available configuration parameters in the `pg_stat_monitor_settings` view:

```sql
SELECT *

FROM pg_settings

WHERE name like 'pg_stat_monitor.%';
```

To amend the `pg_stat_monitor` configuration, use the General Configuration Unit (GCU) system.

!!! admonition "GUC variable types"

    There are two types of GUC variables:

    The first type can only be set in the `postgresql.conf` configuration file and comes into an effect on the start of the `postgres` server. You can set the same variable using the [`ALTER SYSTEM`](https://www.postgresql.org/docs/current/sql-altersystem.html) command. In this case the value of this variable is written into the `postgresql.auto.conf` which has preference over `postgresql.conf`. You also must restart the `postgres` server to apply the values from the `postgresql.auto.conf` file.  The user can change the variable using the [`SET`](https://www.postgresql.org/docs/current/sql-set.html) command without any error, but that does not affect the variable settings.

    The second type of GUC variables can be set by the user from the client (`psql`) using the SET command. These variables are session-based, and their values can only be visible on that sessions. These variables can also be set with the or ALTER SYSTEM command and in the configuration file, but in that case, the effect of these variables is on all new sessions.

## Parameters

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
| [pg_stat_monitor.pgsm_enable_overflow](#pg_stat_monitorpgsm_enable_overflow) |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_overflow_target](#pg_stat_monitorpgsm_overflow_target) |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_enable_pgsm_query_id](#pg_stat_monitorpgsm_enable_pgsm_query_id) |   :x:  |  :x:  |   :white_check_mark: |  :x:  | 
| [pg_stat_monitor.pgsm_enable_query_plan](#pg_stat_monitorpgsm_enable_query_plan)  |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_track](#pg_stat_monitorpgsm_track) |  :x:  |  :x:  |   :x:  | :white_check_mark: |
| [pg_stat_monitor.pgsm_extract_comments](#pg_stat_monitorpgsm_extract_comments)|  :x:  |  :x:  |   :x:  | :white_check_mark: |
| [pg_stat_monitor.pgsm_track_planning](#pg_stat_monitorpgsm_track_planning) |   :x:  |  :x:  |   :white_check_mark: |  :x:  |
| [pg_stat_monitor.pgsm_track_application_names](#pg_stat_monitorpgsm_track_application_names) |   :white_check_mark:  |  :white_check_mark:  |   :x: |  :x:  |

## Parameters description

### pg_stat_monitor.pgsm_max

**Default**: 256
**Min / Max**: 10 / 10240
**Restart required**: YES
**Context**: postmaster

Limits the shared memory (in MB) used by `pg_stat_monitor`. Memory is divided equally across buckets and allocated at PostgreSQL startup.

### pg_stat_monitor.pgsm_query_max_len

**Default**: 2048
**Min / Max**: 1024 / 2147483647
**Restart required**: YES
**Context**: postmaster

Sets the maximum length of a query string (in characters) that `pg_stat_monitor` will track. Queries longer than this value are truncated to the specified length. This helps avoid excessive shared memory usage.

### pg_stat_monitor.pgsm_max_buckets

**Default**: 10
**Min / Max**: 1 / 20000
**Restart required**: YES
**Context**: postmaster

Defines how many buckets `pg_stat_monitor` uses for collecting and aggregating information.  
Each bucket stores query information for the duration defined by [`pg_stat_monitor.pgsm_bucket_time`](#pg_stat_monitorpgsm_bucket_time).  

**Example**:

If set to `2`, `pg_stat_monitor` will:

 1. Collect query information in the first bucket
 2. After the bucket's time expires, defined by the [pg_stat_monitor.pgsm_bucket_time](#pg_stat_monitorpgsm_bucket_time) parameter, switch to the second bucket, reset all counters, and repeat

### pg_stat_monitor.pgsm_bucket_time

**Default**: 60
**Min / Max**: 1 / 2147483647
**Restart required**: YES
**Context**: postmaster

Defines how long each bucket remains active (in seconds). When the time expires, `pg_stat_monitor` switches to the next bucket and resets its counters.

### pg_stat_monitor.pgsm_histogram_min

**Default**: 1
**Min / Max**: 0 / 50000000
**Restart required**: YES
**Context**: postmaster

Defines the minimum execution time for a query to appear in histogram output (in ms).

!!! note
    Starting with version 2.0.0 you can set this parameter as a decimal value which allows query output with execution time less than 1 ms.

### pg_stat_monitor.pgsm_histogram_max

**Default**: 10000
**Min / Max**: 10 / 50000000
**Restart required**: YES
**Context**: postmaster

Defines the maximum execution time for a query to appear in histogram output (in ms).

!!! note
    Starting with version 2.0.0, you can set a decimal value which allows fine-tuning the output with more precision.

### pg_stat_monitor.pgsm_histogram_buckets

**Default**: 20
**Min / Max**: 2 / 50
**Restart required**: YES
**Context**: postmaster

Sets the maximum number of buckets used to generate the histogram output.

!!! note
    - Starting with version 1.1.0, the maximum allowed value is 50.
    - Starting with version 2.0.0, on server startup `pg_stat_monitor` calculates the maximum number of buckets that can be created from the time range specified in `pgsm_histogram_min` and `pgsm_histogram_max`. If the calculated maximum is lower than your configured value, a warning is written to the server log.

### pg_stat_monitor.pgsm_query_shared_buffer

**Default**: 20
**Min / Max**: 1 / 10000
**Restart required**: YES
**Context**: postmaster

Defines the maximum shared memory (in MB) that `pg_stat_monitor` allocates for tracking queries.

### pg_stat_monitor.pgsm_overflow_target (deprecated)

**Default**: 1
**Min / Max**: 0 / 1
**Restart required**: YES
**Context**: postmaster

Sets the overflow target for `pg_stat_monitor`.

!!! note
    Starting with version 2.0.0, this option is deprecated. Use the [pg_stat_monitor.pgsm_enable_overflow](#pg_stat_monitorpgsm_enable_overflow) instead.

**Historical defaults:**

- Version 1.0.0 and earlier: `1`
- Version 1.1.1: `0`

### pg_stat_monitor.pgsm_track_utility

**Default**: 1
**Min / Max**: 0 / 1
**Restart required**: NO
**Context**: client

Sets tracking of utility commands by `pg_stat_monitor`. Ignored utility commands are `SELECT`, `INSERT`, `UPDATE`, and `DELETE`.

**Historical defaults:**

- Version 1.1.0 and later: values now displayed as YES / NO instead of 1 / 0

### pg_stat_monitor.pgsm_track_application_names

**Default**: 1
**Min / Max**: 0 / 1
**Restart required**: NO
**Context**: client

Controls whether `pg_stat_monitor` records the application name that executes the query. If enabled, the application name becomes a part of the entry key.

!!! note
    Tracking application names is resource-intensive and may cause performance degradation, especially with a large number of connections.

Disabling this feature can improve performance by consolidating statistics for the same query across different applications.

### pg_stat_monitor.pgsm_enable_pgsm_query_id

**Default**: 1
**Min / Max**: 0 / 1
**Restart required**: NO
**Context**: client

Controls the generation of a unique hash code that identifies the query. This hash code is independent of PostgreSQL server version, constants within the query, database, user or schema. Its usage allows getting insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas.

!!! note
    Enabling this parameter results in additional load on the database.

### pg_stat_monitor.pgsm_normalized_query

Type: boolean. Default: NO

Server restart - NO

Starting with version 1.1.0, the data type changed to boolean and the query shows the actual parameter instead of the placeholder by default. It is quite useful when users want to use that query and try to run that query to check the abnormalities. Users should be aware, however, that running queries with disabled normalization can expose some sensitive data. 

But in most cases users like the queries with a placeholder. This parameter is used to toggle between the two said options.

In version 1.0.0 and earlier, the parameter type is "integer" and the default value is 1. This means that the query shows placeholders instead of the actual data

### pg_stat_monitor.pgsm_enable_overflow

Type: boolean. Default: YES

Server restart - YES

Controls whether pg_stat_monitor can grow beyond shared memory into swap space.

### pg_stat_monitor.pgsm_enable_query_plan

Type: boolean. Default: NO

Server restart - NO

Enables or disables query plan monitoring. When the `pgsm_enable_query_plan` is disabled (no), the query plan will not be captured by `pg_stat_monitor`. Enabling it may adversely affect the database performance. 

### pg_stat_monitor.pgsm_track

Default: top

Server restart - NO

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

Type: boolean. Default: NO

Server restart - YES

Available for PostgreSQL 14 and later versions. 

This parameter instructs ``pg_stat_monitor`` to monitor query planning statistics. 
