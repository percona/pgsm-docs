# Functions

## pg_stat_monitor_reset()

This function resets all the statistics and clears the view. The function will delete all the previous data.

## pg_stat_monitor_version()

This function provides the version of `pg_stat_monitor`.

```
postgres=# select pg_stat_monitor_version();
 pg_stat_monitor_version
-------------------------
 devel
(1 row)
```

## histogram(bucket id, query id)

It is used to generate the histogram, you can refer to [histogram sections](user_guide.md#histogram).