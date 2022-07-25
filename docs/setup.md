# Setting up `pg_stat_monitor`


You can enable `pg_stat_monitor` when your `postgresql` instance is not running.

`pg_stat_monitor` needs to be loaded at the start time. The extension requires additional shared memory; therefore,  add the `pg_stat_monitor` value for the `shared_preload_libraries` parameter and restart the `postgresql` instance.

Use the [ALTER SYSTEM](https://www.postgresql.org/docs/current/sql-altersystem.html) command from `psql` terminal to modify the `shared_preload_libraries` parameter.

```sql
ALTER SYSTEM SET shared_preload_libraries = 'pg_stat_monitor';
```

> **NOTE**: If you’ve added other modules to the `shared_preload_libraries` parameter (for example, `pg_stat_statements`), list all of them separated by commas for the `ALTER SYSTEM` command. 
>
>:warning: For PostgreSQL 13 and earlier versions,`pg_stat_monitor` **must** follow `pg_stat_statements`. For example, `ALTER SYSTEM SET shared_preload_libraries = 'foo, pg_stat_statements, pg_stat_monitor'`.
>
>In PostgreSQL 14, you can specify `pg_stat_statements` and `pg_stat_monitor` in any order. However, due to the extensions' architecture, if both `pg_stat_statements` and `pg_stat_monitor` are loaded, only the last listed extension captures utility queries, CREATE TABLE, Analyze, etc. The first listed extension captures  most common queries like SELECT, UPDATE, INSERT, but does not capture utility queries.
>
>Thus, to collect the whole statistics with pg_stat_monitor, we recommend to specify the extensions as follows: ALTER SYSTEM SET shared_preload_libraries = 'pg_stat_statements, pg_stat_monitor'.

Start or restart the `postgresql` instance to apply the changes.

=== "On Debian and Ubuntu"

    ```sh
    sudo systemctl restart postgresql.service
    ```

=== "On Red Hat Enterprise Linux and derivatives"

    ```sh
    sudo systemctl restart postgresql-13
    ```

Create the extension using the [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) command. To use this command, your user must have the privileges of a superuser or a database owner. Connect to `psql` as a superuser for a database and run the following command:


```sql
CREATE EXTENSION pg_stat_monitor;
```


This allows you to see the stats collected by `pg_stat_monitor`.

By default, `pg_stat_monitor` is created for the `postgres` database. To access the statistics from other databases, you need to create the extension for every database.

```
-- Select some of the query information, like client_ip, username and application_name etc.

postgres=# SELECT application_name, userid AS user_name, datname AS database_name, substr(query,0, 50) AS query, calls, client_ip
           FROM pg_stat_monitor;
 application_name | user_name | database_name |                       query                       | calls | client_ip
------------------+-----------+---------------+---------------------------------------------------+-------+-----------
 psql             | vagrant   | postgres      | SELECT application_name, userid::regrole AS user_ |     1 | 127.0.0.1
 psql             | vagrant   | postgres      | SELECT application_name, userid AS user_name, dat |     3 | 127.0.0.1
 psql             | vagrant   | postgres      | SELECT application_name, userid AS user_name, dat |     1 | 127.0.0.1
 psql             | vagrant   | postgres      | SELECT application_name, userid AS user_name, dat |     8 | 127.0.0.1
 psql             | vagrant   | postgres      | SELECT bucket, substr(query,$1, $2) AS query, cmd |     1 | 127.0.0.1
(5 rows)
```

To learn more about `pg_stat_monitor` features and usage, see [User Guide](user_guide.md). To view all other data elements provided by `pg_stat_monitor`, please see the [`pg_stat_monitor` view reference](reference.md).


## Configuration

You can find the configuration parameters of the `pg_stat_monitor` extension in the `pg_stat_monitor_settings` view. To change the default configuration, specify new values for the desired parameters using the GUC (Grant Unified Configuration) system. To learn more, refer to the [Configuration parameters reference](configuration.md).

