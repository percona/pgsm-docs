# Upgrade `pg_stat_monitor`

This document describes how to upgrade `pg_stat_monitor`.

Before upgrading `pg_stat_monitor`, learn about changes for the desired version in the [release notes](release-notes/release_notes.md). 

Check what version of `pg_stat_monitor` is running in your database:

```sql
select pg_stat_monitor_version();
```

Output:

```
 pg_stat_monitor_version
-------------------------
 1.1.1
(1 row)
```

To upgrade the extension, use the [ALTER EXTENSION](https://www.postgresql.org/docs/current/sql-alterextension.html) command with the `UPDATE TO` option. Your PostgreSQL user must have the privileges of a superuser or a database owner to run it.

```sql
ALTER EXTENSION pg_stat_monitor UPDATE TO '2.0';
``` 
