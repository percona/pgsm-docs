# Automatically create `pg_stat_monitor` for every newly created database

In PostgreSQL, a database is created by copying the [system template database](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) `template1`. To automatically create the `pg_stat_monitor` extension for every newly created database, you can create a new system template database, create the extension for it and define this template when you create the database.  

Your user must have the superuser privileges.

The following steps show how to do it as the `postgres` user

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

4. Set the database as the template database. 

   ```sql
   UPDATE pg_database SET datistemplate=true WHERE datname='my-template' 
   ```

5. Create a database using the new template

    ```sql
    CREATE DATABASE dbtest TEMPLATE my-template;
    ```

6. Check that `pg_stat_monitor` is accessible from the database

    ```{.bash data-prompt="$"}
    $ psql -d dbtest -c '\dx';
    ```