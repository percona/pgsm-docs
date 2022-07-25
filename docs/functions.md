# Functions

## pg_stat_monitor_reset()

This function resets all the statistics and clears the view. Eventually, the function will delete all the previous data.

## pg_stat_monitor_version()
This function provides the build version of `pg_stat_monitor` version.

```
postgres=# select pg_stat_monitor_version();
 pg_stat_monitor_version
-------------------------
 devel
(1 row)
```

## histogram(bucket id, query id)

It is used to generate the histogram, you can refer to histogram sections.