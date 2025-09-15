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

## Parameters description

The following section describes each `pg_stat_monitor` configuration parameter, including its default value, allowed range, and the context in which it can be used.

For more details on PostgreSQL configuration parameters and their contexts, see the [pg_settings](https://www.postgresql.org/docs/current/view-pg-settings.html) reference sheet.

### pg_stat_monitor.pgsm_max

**Default**: 256

**Min / Max**: 10 / 10240

**Context**: postmaster

Limits the shared memory (in MB) used by `pg_stat_monitor`. Memory is divided equally across buckets and allocated at PostgreSQL startup.

### pg_stat_monitor.pgsm_query_max_len

**Default**: 2048

**Min / Max**: 1024 / 2147483647

**Context**: postmaster

Sets the maximum length of a query string (in characters) that `pg_stat_monitor` will track. Queries longer than this value are truncated to the specified length. This helps avoid excessive shared memory usage.

### pg_stat_monitor.pgsm_max_buckets

**Default**: 10

**Min / Max**: 1 / 20000

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

**Context**: postmaster

Defines how long each bucket remains active (in seconds). When the time expires, `pg_stat_monitor` switches to the next bucket and resets its counters.

### pg_stat_monitor.pgsm_histogram_min

**Default**: 1

**Min / Max**: 0 / 50000000

**Context**: postmaster

Defines the minimum execution time for a query to appear in histogram output (in ms).
Defines how long each bucket remains active (in seconds). When the time expires, `pg_stat_monitor` switches to the next bucket.
!!! note
    Starting with version 2.0.0 you can set this parameter as a decimal value which allows query output with execution time less than 1 ms.

### pg_stat_monitor.pgsm_histogram_max

**Default**: 10000

**Min / Max**: 10 / 50000000

**Context**: postmaster

Defines the maximum execution time for a query to appear in histogram output (in ms).

!!! note
    Starting with version 2.0.0, you can set a decimal value which allows fine-tuning the output with more precision.
Defines how long each bucket remains active (in seconds). When the time expires, `pg_stat_monitor` switches to the next bucket.

### pg_stat_monitor.pgsm_histogram_buckets

**Default**: 20

**Min / Max**: 2 / 50

**Context**: postmaster

Sets the maximum number of buckets used to generate the histogram output.

!!! note
    - Starting with version 1.1.0, the maximum allowed value is 50.
    - Starting with version 2.0.0, on server startup `pg_stat_monitor` calculates the maximum number of buckets that can be created from the time range specified in `pgsm_histogram_min` and `pgsm_histogram_max`. If the calculated maximum is lower than your configured value, a warning is written to the server log.

### pg_stat_monitor.pgsm_query_shared_buffer

**Default**: 20

**Min / Max**: 1 / 10000

**Context**: postmaster

Defines the maximum shared memory (in MB) that `pg_stat_monitor` allocates for tracking queries.

### pg_stat_monitor.pgsm_overflow_target (deprecated)

**Default**: on

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

**Context**: userset

Sets tracking of utility commands by `pg_stat_monitor`. Ignored utility commands are `SELECT`, `INSERT`, `UPDATE`, and `DELETE`.

**Historical defaults:**

- Version 1.1.0 and later: values now displayed as YES / NO instead of 1 / 0

### pg_stat_monitor.pgsm_track_application_names

**Default**: 1

**Min / Max**: 0 / 1

**Context**: userset

Controls whether `pg_stat_monitor` records the application name that executes the query. If enabled, the application name becomes a part of the entry key.

!!! note
    Tracking application names is resource-intensive and may cause performance degradation, especially with a large number of connections.

Disabling this feature can improve performance by consolidating statistics for the same query across different applications.

### pg_stat_monitor.pgsm_enable_pgsm_query_id

**Default**: 1

**Min / Max**: 0 / 1

**Context**: userset

Enable or disable the generation of a unique hash code that identifies each query. This hash code is independent of the PostgreSQL server version, constants within the query, database, user or schema.

It allows you to get insights into how the query is being planned and executed across PostgreSQL versions, database, users or schemas.

!!! note
    Enabling this parameter results in additional load on the database.

### pg_stat_monitor.pgsm_normalized_query

**Default**: 0

**Min / Max**: 0 / 1

**Context**: userset

Controls whether queries are saved in normalized form (with placeholders for constants) or in their literal form (with actual parameter values).

!!! note
    Disabling normalization can expose some sensitive data.

**Historical defaults:**

- Version 1.0.0 and earlier: the parameter type was `integer` with the default value `1` (placeholders used).
- Version 1.1.0 and later: the parameter type changed to `boolean` with the default value `0` (actual parameter used).

### pg_stat_monitor.pgsm_enable_overflow

**Default**: 1

**Min / Max**: 0 / 1

**Context**: postmaster

Controls whether `pg_stat_monitor` is allowed to exceed beyond the shared memory and use swap space.

### pg_stat_monitor.pgsm_enable_query_plan

**Default**: 0

**Min / Max**: 0 / 1

**Context**: userset

Controls whether  `pg_stat_monitor` captures query plans. When disabled, the query plan is not captured by `pg_stat_monitor`.

!!! note
    Enabling this parameter may negatively impact database performance.

### pg_stat_monitor.pgsm_extract_comments

**Default**: 0

**Min / Max**: 0 / 1

**Context**: userset

Controls whether `pg_stat_monitor` extracts comments from queries.

### pg_stat_monitor.pgsm_track

**Default**: top

**Context**: userset

Controls which statements are tracked by `pg_stat_monitor`.

Values:

- `top`: Track only top-level queries issued directly by clients excluding nested statements (e.g., queries inside functions).
- `all`: Track both top-level and nested queries. Some `SELECT` statements may appear as duplicates.
- `none`: Disables query monitoring. The module is still loaded and uses shared memory, but does not capture query data.

### pg_stat_monitor.pgsm_track_planning

**Default**: 0

**Min / Max**: 0 / 1

**Context**: userset

Controls query planning statistics monitoring in `pg_stat_monitor`.

!!! note
    This parameter is available only for for PostgreSQL versions 14 and above.
