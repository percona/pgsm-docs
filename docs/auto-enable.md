# Make `pg_stat_monitor` automatically accessible for every newly created database

In PostgreSQL, a database is created by copying the [system template database](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) `template1`. To automatically make `pg_stat_monitor` available for every newly created database, you can do the following:

* modify the `template1` template database and enable the extension for it. Then all databases you create have the `pg_stat_monitor` view available.
* create a new system template database and enable the extension for it. You need to define this template when you create a database to have the `pg_stat_monitor` view available for it.

For either option, your user must have the superuser privileges; the instructions below assume your are working as the `postgres` user:

=== "Modify `template1` database"

    1. Log in as the `postgres` user    

        ```{.bash data-prompt="$"}
        $ sudo su postgres
        ``` 

    2. Create the extension for the `template1` database

        ```{.bash data-prompt="$"}
        $ psql -d template1 -c 'create extension pg_stat_monitor;'
        ```

    3. Create a new database

        ```{.bash data-prompt="$"}
        $ createdb -T my_template dbtest
        ```    

    4. Check that `pg_stat_monitor` is accessible from the database    

        ```{.bash data-prompt="$"}
        $ psql -d dbtest -c '\dx';
        ```
        
        Output:    

        ```{.text .no-copy}
              Name       | Version |   Schema   |
                   Description
        -----------------+---------+------------+-------------------------------------------------------------------------------------------------------------
        ----------------------------------------------------------------------------------------------------------------------------------------------
         pg_stat_monitor | 2.0     | public     | The pg_stat_monitor is a PostgreSQL Query Performance Monitoring tool, based on PostgreSQL contrib module pg
        _stat_statements. pg_stat_monitor provides aggregated statistics, client information, plan details including plan, and histogram information.
         plpgsql         | 1.0     | pg_catalog | PL/pgSQL procedural language
        (2 rows)
        ```


=== "Create a new template database"    

    1. Log in as the `postgres` user    

        ```{.bash data-prompt="$"}
        $ sudo su postgres
        ```    

    2. Create a database to serve as the template.    

        ```{.bash data-prompt="$"}
        $ createdb my_template
        ```    

    3. Connect to this database and create the `pg_stat_monitor` extension    

        ```{.bash data-prompt="$"}
        $ psql -d my_template -c 'create extension pg_stat_monitor;'
        ```     

    4. Set the database as the template database.     

        * Connect to the database:    

           ```{.bash data-prompt="$"}
           $ psql -d my_template
           ```
        
        * Update the database    

           ```sql
           UPDATE pg_database SET datistemplate=true where datname='my_template';
           ```    

    5. Create a database using the new template    

        ```{.bash data-prompt="$"}
        $ createdb -T my_template dbtest
        ```    

    6. Check that `pg_stat_monitor` is accessible from the database    

        ```{.bash data-prompt="$"}
        $ psql -d dbtest -c '\dx';
        ```
        
        Output:    

        ```{.text .no-copy}
              Name       | Version |   Schema   |
                   Description
        -----------------+---------+------------+-------------------------------------------------------------------------------------------------------------
        ----------------------------------------------------------------------------------------------------------------------------------------------
         pg_stat_monitor | 2.0     | public     | The pg_stat_monitor is a PostgreSQL Query Performance Monitoring tool, based on PostgreSQL contrib module pg
        _stat_statements. pg_stat_monitor provides aggregated statistics, client information, plan details including plan, and histogram information.
         plpgsql         | 1.0     | pg_catalog | PL/pgSQL procedural language
        (2 rows)
        ```
