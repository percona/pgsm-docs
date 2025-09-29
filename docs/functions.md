# Functions

All database users can use the following functions directly:

## pg_stat_monitor_version()

This function provides the version of `pg_stat_monitor`.

```sql
SELECT pg_stat_monitor_version();
 pg_stat_monitor_version
-------------------------
 devel
(1 row)
```

## histogram(bucket id, query id)

It is used to generate the histogram, you can refer to [histogram sections](user_guide.md#histogram).

## pg_stat_monitor_reset()

This function resets all the statistics and clears the view. The function will delete all the previous data. By default, this function can only be executed by superusers. Access may be granted to others using GRANT.

## range()

## decode_error_level()

## get_histogram_timings()

## get_cmd_type()

## pg_stat_monitor_internal()

The following **internal** functions are also visible to superusers. We don't recommend users to use them directly nor to make any changes to them since these functions are used by the `pg_stat_monitor` internally:

```
 routine_schema |       routine_name       | routine_type | data_type 
----------------+--------------------------+--------------+-----------
 public         | pgsm_create_view         | FUNCTION     | integer
 public         | pgsm_create_13_view      | FUNCTION     | integer
 public         | pgsm_create_14_view      | FUNCTION     | integer
 public         | pgsm_create_15_view      | FUNCTION     | integer
 public         | pgsm_create_17_view      | FUNCTION     | integer
 public         | pgsm_create_18_view      | FUNCTION     | integer

```