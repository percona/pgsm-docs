# Remove `pg_stat_monitor`

To uninstall `pg_stat_monitor`, do the following:

1. Disable statistics collection. Establish the `psql` session and run the following command :

    ```sql
    ALTER SYSTEM SET pg_stat_monitor.track = none;
    ```

2. Drop `pg_stat_monitor` extension:

    ```sql
    DROP EXTENSION pg_stat_monitor;
    ```

3. Remove `pg_stat_monitor` from the `shared_preload_libraries` configuration parameter:

    ```sql 
    ALTER SYSTEM SET shared_preload_libraries = '';
    ```

    !!! important

        If the `shared_preload_libraries` parameter includes other modules, specify them all for the `ALTER SYSTEM SET` command to keep using them.

4. Restart the `postgresql` instance to apply the changes. The following command restarts PostgreSQL 15. Replace the version value with the one you are using. 


    * On Debian and Ubuntu:

    ```{.bash data-prompt="$"}
    $ sudo systemctl restart postgresql.service
    ```

    * On Red Hat Enterprise Linux and CentOS:


    ```{.bash data-prompt="$"}
    $ sudo systemctl restart postgresql-15
    ```