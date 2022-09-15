# Setting up `pg_stat_monitor`


`pg_stat_monitor` needs to be loaded at the start time. The extension requires additional shared memory; therefore,  add the `pg_stat_monitor` value for the `shared_preload_libraries` parameter and restart the `postgresql` instance.

Use the [ALTER SYSTEM](https://www.postgresql.org/docs/current/sql-altersystem.html) command from `psql` terminal to modify the `shared_preload_libraries` parameter.

```sql
ALTER SYSTEM SET shared_preload_libraries = 'pg_stat_monitor';
```

> **NOTE**: If you’ve added other modules to the `shared_preload_libraries` parameter (for example, `pg_stat_statements`), list all of them separated by commas for the `ALTER SYSTEM` command. 
>
>`pg_stat_monitor` **must** follow `pg_stat_statements`. For example, `ALTER SYSTEM SET shared_preload_libraries = 'foo, pg_stat_statements, pg_stat_monitor'`.

Start or restart the `postgresql` instance to apply the changes.

=== "On Debian and Ubuntu"

    ```sh
    sudo systemctl restart postgresql.service
    ```

=== "On Red Hat Enterprise Linux and derivatives"

    ```sh
    sudo systemctl restart postgresql-XXX
    ```
    
    Replace the `XXX` with the PostgreSQL version you are using.

Create the extension using the [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) command. To use this command, your user must have the privileges of a superuser or a database owner. Connect to `psql` as a superuser for a database and run the following command:


```sql
CREATE EXTENSION pg_stat_monitor;
```


This allows you to see the stats collected by `pg_stat_monitor`.

By default, `pg_stat_monitor` is created for the `postgres` database. To access the statistics from other databases, you need to create the extension for every database.

To learn more about `pg_stat_monitor` features and usage, see [User Guide](user_guide.md). To view all other data elements provided by `pg_stat_monitor`, please see the [`pg_stat_monitor` view reference](reference.md).


## Configuration

You can find the configuration parameters of the `pg_stat_monitor` extension in the `pg_stat_monitor_settings` view. To change the default configuration, specify new values for the desired parameters using the GUC (Grant Unified Configuration) system. To learn more, refer to the [Configuration parameters reference](configuration.md).

