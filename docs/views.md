# Views

`pg_stat_monitor` provides the following views:

* `pg_stat_monitor` is the view where statistics data is presented.
* `pg_stat_monitor_settings` view shows available configuration options which you can change.

## pg_stat_monitor view

The statistics gathered by the module are made available via the view named `pg_stat_monitor`. This view contains one row for each distinct combination of metrics and whether it is a top-level statement or not (up to the maximum number of distinct statements that the module can track). For details about available counters, refer to the [`pg_stat_monitor` view reference](reference.md).

The following are the primary keys for `pg_stat_monitor`:

*  `bucket`
*  `userid`
*  `datname`
*  `queryid`
*  `client_ip`
*  `planid`
*  `application_name`
*  `toplevel`.

!!! note

    The `toplevel` key is considered starting with PostgreSQL 14 and above. For PostgreSQL 13 and earlier versions, the `toplevel` value is set to 1 by default, and thus, ignored.

A new row is created for each key in the `pg_stat_monitor` view. 

`pg_stat_monitor` inherits the metrics available in `pg_stat_statements`, plus provides additional ones. See the [`pg_stat_monitor` vs `pg_stat_statements` comparison](comparison.md) for details.

For security reasons, only superusers and members of the `pg_read_all_stats` role are allowed to see the SQL text, `client_ip` and `queryid` of queries executed by other users. Other users can see the statistics, however, if the view has been installed in their database.

## pg_stat_monitor_settings view (dropped)

Starting with version 2.0.0, the `pg_stat_monitor_settings` view is deprecated and removed. All `pg_stat_monitor` configuration parameters are now available though the `pg_settings` view using the following query: 

```sql
SELECT name, setting, unit, context, vartype, source, min_val, max_val, enumvals, boot_val, reset_val, pending_restart FROM pg_settings WHERE name LIKE '%pg_stat_monitor%';
```

For backward compatibility, you can create the `pg_stat_monitor_settings` view using the following SQL statement:

```sql
CREATE VIEW pg_stat_monitor_settings

AS

SELECT *

FROM pg_settings

WHERE name like 'pg_stat_monitor.%';
```

In `pg_stat_monitor` version 1.1.1 and earlier, the `pg_stat_monitor_settings` view shows one row per `pg_stat_monitor` configuration parameter. It displays configuration parameter name, value, default value, description, minimum and maximum values, and whether a restart is required for a change in value to be effective.
