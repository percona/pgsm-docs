# Set up `pg_stat_monitor`

After you [installed `pg_stat_monitor`](install.md), you must set it up to use it.

## 1. Load the module

Load `pg_stat_monitor` at the start time by adding it to the `shared_preload_libraries` configuration parameter. This is because `pg_stat_monitor` requires additional shared memory.

1. Connect to `psql` and modify the `shared_preload_libraries` parameter using the [ALTER SYSTEM](https://www.postgresql.org/docs/current/sql-altersystem.html) command. 

    ```sql
    ALTER SYSTEM SET shared_preload_libraries = 'pg_stat_monitor';
    ```

    > **NOTE**: If you’ve added other modules to the `shared_preload_libraries` parameter (for example, `pg_stat_statements`), list all of them separated by commas for the `ALTER SYSTEM` command. 
    >
    >`pg_stat_monitor` **must** follow `pg_stat_statements`. For example, `ALTER SYSTEM SET shared_preload_libraries = 'foo, pg_stat_statements, pg_stat_monitor'`.

2. Start or restart the `postgresql` instance to apply the changes.

    === "On Debian and Ubuntu"

        ```{.bash data-prompt="$"}
        $ sudo systemctl restart postgresql.service
        ```

    === "On Red Hat Enterprise Linux and derivatives"

        ```{.bash data-prompt="$"}
        $ sudo systemctl restart postgresql-XXX
        ```
        
        Replace the `XXX` with the PostgreSQL version you are using.

## 2. Create the extension 

Create the extension with the user that has the privileges of a superuser or a database owner. Connect to `psql` as a superuser for a database and run the [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) command:


```sql
CREATE EXTENSION pg_stat_monitor;
```

After the setup is complete, you can see the stats collected by `pg_stat_monitor`.

By default, `pg_stat_monitor` is created for the `postgres` database. To access the statistics from other databases, you need to create the extension for every database.

## Next steps

* [Use `pg_stat_monitor`](user_guide.md). 


## Useful links

* [Configuration](configuration.md)
* [`pg_stat_monitor` view reference](reference.md)

