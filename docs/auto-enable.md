# Automatically create `pg_stat_monitor` for every newly created database

In PostgreSQL, a database is created by copying the [system template database](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) `template1`. To automatically create the `pg_stat_monitor` extension for every newly created database, you can do the following:

* create a new system template database, create the extension for it and define this template when you create the database.  
* modify the `template1` template database and create the extension for it. Then all databases you create will have the `pg_stat_monitor` view available.

For either option, your user must have the superuser privileges.

The following steps show how to create the new template database it as the `postgres` user:

1. Log in as the `postgres` user

    ```{.bash data-prompt="$"}
    $ sudo su postgres
    ```

2. Create a database to serve as the template.

    ```{.bash data-prompt="$"}
    $ createdb my-template
    ```

3. Connect to this database and create the `pg_stat_monitor` extension

    ```{.bash data-prompt="$"}
    $ psql -d my-template -c 'create extension pg_stat_monitor;'
    ```

    !!! note

        If you opt to modify the `template1` database, specify it instead of `my-template`.

4. Set the database as the template database. 

   ```{.bash data-prompt="$"}
    $ psql -d my-template -c 'update pg_database set datistemplate=true where datname='my-template''
   ```

5. Create a database using the new template

    ```{.bash data-prompt="$"}
    $ createdb -T my-template dbtest
    ```

6. Check that `pg_stat_monitor` is accessible from the database

    ```{.bash data-prompt="$"}
    $ psql -d dbtest -c '\dx';
    ```

