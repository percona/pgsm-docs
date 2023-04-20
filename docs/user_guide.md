# Features

This document describes the `pg_stat_monitor` features and gives the usage examples. For how to install and set up ``pg_stat_monitor``, see the [Installation instructions](install.md).

!!! note
    
    Column names differ depending on the PostgreSQL version you are using. The following usage examples are provided for PostgreSQL version 15. For earlier versions of PostgreSQL, please consult the [pg_stat_monitor reference](reference.md).


## Time buckets

Instead of supplying one set of ever-increasing counts, `pg_stat_monitor` computes stats for a configured number of time intervals; time buckets. This allows for much better data accuracy, especially in the case of high-resolution or unreliable networks. 

!!! example

    ```sql
    SELECT bucket, bucket_start_time, query,calls FROM pg_stat_monitor ORDER BY bucket;
    ```

    Output:

    ```{.sql .no-copy}
    -[ RECORD 1 ]-----+-----------------------------------------------------------------------------------------------------------------------------------
    ------------------------------------------------------------------------------------------------------------------------------------------------------
    ----------------------------------
    bucket            | 1
    bucket_start_time | 2023-02-21 08:20:00+00
    query             | INSERT INTO players (id, about, age) VALUES (generate_series($1, $2),  repeat($3, $4) || $5 || trunc(random()*$6), trunc(random()*
    $7 * $8 + $9))
    calls             | 1
    ```

    The `bucket` parameter shows the number of a bucket for which a given record belongs.

    The `bucket_start_time` shows the start time of the bucket.
    `query` shows the actual query text.
    `calls` shows how many times a given query was called.

## Table and index access statistics per statement

`pg_stat_monitor` collects the information about what tables were accessed by a statement. This allows you to identify all queries which access a given table easily.


## Query and client information

`pg_stat_monitor` provides  additional metrics for detailed analysis of query performance from various perspectives, including client connection details like user name, application name, IP address to name a few relevant columns.
With this information, `pg_stat_monitor` enables users to track a query to the originating application. More details about the application or query may be incorporated in the SQL query in a [Google’s Sqlcommenter](https://google.github.io/sqlcommenter/) format.

!!! example "Examples"

    **1. Query information**

    The following example shows the usename, database name, unique queryid hash, query, and the total number of calls of that query.

    ```sql
    SELECT userid,  datname, queryid, substr(query,0, 50) AS query, calls FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
     userid  | datname  |     queryid      |                       query                       | calls
    ---------+----------+------------------+---------------------------------------------------+-------
     10 | postgres | 939C2F56E1F6A174 | END                                               |   561
     10 | postgres | 2A4437C4905E0E23 | SELECT abalance FROM pgbench_accounts WHERE aid = |   561
     10 | postgres | 4EE9ED0CDF143477 | SELECT userid,  datname, queryid, substr(query,$1 |     1
     10 | postgres | 8867FEEB8A5388AC | vacuum pgbench_branches                           |     1
     10 | postgres | 41D1168FB0733CAB | select count(*) from pgbench_branches             |     1
     10 | postgres | E5A889A8FF37C2B1 | UPDATE pgbench_accounts SET abalance = abalance + |   561
     10 | postgres | 4876BBA9A8FCFCF9 | truncate pgbench_history                          |     1
     10 | postgres | 22B76AE84689E4DC | INSERT INTO pgbench_history (tid, bid, aid, delta |   561
     10 | postgres | F6DA9838660825CA | vacuum pgbench_tellers                            |     1
     10 | postgres | 214646CE6F9B1A85 | BEGIN                                             |   561
     10 | postgres | 27462943E814C5B5 | UPDATE pgbench_tellers SET tbalance = tbalance +  |   561
     10 | postgres | 4F66D46F3D4151E  | SELECT userid,  dbid, queryid, substr(query,0, 50 |     1
     10 | postgres | 6A02C123488B95DB | UPDATE pgbench_branches SET bbalance = bbalance + |   561
    (13 rows)
    ```

    **2. Application details**

    ```sql
    SELECT application_name, client_ip, substr(query,0,100) as query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
     application_name | client_ip |                                                query
    ------------------+-----------+-----------------------------------------------------------------------------------------------------
     psql             | 127.0.0.1 | [200~INSERT INTO DOCUMENT_TEMPLATE(id,name, short_description, author,                             +
                      |           |
     psql             | 127.0.0.1 | SELECT c.relchecks, c.relkind, c.relhasindex, c.relhasrules, c.relhastriggers, c.relrowsecurity, c.
     psql             | 127.0.0.1 | SELECT a.attname,                                                                                  +
                      |           |   pg_catalog.format_type(a.atttypid, a.atttypmod),                                                 +
                      |           |   (SELECT pg_catalog.pg_get_ex
     psql             | 127.0.0.1 | INSERT INTO document_template (stringColumn) VALUES (repeat("Wow ", 3));
     psql             | 127.0.0.1 | create table DOCUMENT_TEMPLATE;
     psql             | 127.0.0.1 | INSERT INTO players (id, about, age) VALUES (generate_series($1, $2),  repeat($3, $4) || $5 || trun
     psql             | 127.0.0.1 | SELECT d.datname as "Name",                                                                        +
                      |           |        pg_catalog.pg_get_userbyid(d.datdba) as "Owner",                                            +
                      |           |        pg_catal
     psql             | 127.0.0.1 | Select * from DOCUMENT_TEMPLATE;
     psql             | 127.0.0.1 | ~                                                                                                  +
                      |           | INSERT INTO DOCUMENT_TEMPLATE(id,name, short_description, author,                                  +
                      |           |                               d
     psql             | 127.0.0.1 | INSERT INTO DOCUMENT_TEMPLATE(id,name, short_description, author,                                  +
                      |           |                               des
     psql             | 127.0.0.1 | SELECT c.oid,                                                                                      +
                      |           |   n.nspname,                                                                                       +
                      |           |   c.relname                                                                                        +
                      |           | FROM pg_catalog.pg_class c                                                                         +
                      |           |      LEFT JOIN pg_catalog.pg_name
     psql             | 127.0.0.1 | INSERT INTO DOCUMENT_TEMPLATE(id,name, short_description, author,                                  +
                      |           |                               des
     psql             | 127.0.0.1 | SELECT bucket, bucket_start_time, query,calls FROM pg_stat_monitor ORDER BY bucket
     psql             | 127.0.0.1 | INSERT INTO test (stringColumn) VALUES (repeat("Wow ", 3));
    (13 rows)
    ```

## Query timing information

Understanding query execution time stats helps you identify what affects query performance and take measures to optimize it. `pg_stat_monitor` collects the total, min, max and average (mean) time it took to execute a particular query and provides this data in separate columns. 

!!! example

    ```sql
    SELECT  userid,  total_exec_time, min_exec_time, max_exec_time, mean_exec_time, query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
    userid | total_exec_time | min_exec_time | max_exec_time | mean_exec_time |                                            query
    --------+-----------------+---------------+---------------+----------------+----------------------------------------------------------------------------------------------
         10 |        1.532168 |      0.749108 |       0.78306 |       0.766084 | SELECT userid,  datname, queryid, substr(query,$1, $2) AS query, calls FROM pg_stat_monitor
         10 |        0.755857 |      0.755857 |      0.755857 |       0.755857 | SELECT application_name, client_ip, substr(query,$1,$2) as query FROM pg_stat_monitor
         10 |               0 |             0 |             0 |              0 | SELECT  userid,  total_time, min_time, max_time, mean_time, query FROM pg_stat_monitor;
         10 |               0 |             0 |             0 |              0 | SELECT  userid,  total_exec_time, min_time, max_time, mean_time, query FROM pg_stat_monitor;
    (4 rows)
    ```


## Query execution plan information

Every query has a plan that was constructed for its execution. Collecting the query plan information as well as monitoring query plan timing helps you understand how you can modify the query to optimize its execution. It also helps to make communication about the query clearer when discussing query performance with other DBAs and application developers.

!!! example

    ```sql
    SELECT substr(query,0,50), query_plan from pg_stat_monitor limit 10;
    ```

    Output:

    ```{.sql .no-copy}
                          substr                       |                                                  query_plan
    ---------------------------------------------------+---------------------------------------------------------------------------------------------------------------
     select o.n, p.partstrat, pg_catalog.count(i.inhpa | Limit                                                                                                        +
                                                       |   ->  GroupAggregate                                                                                         +
                                                       |         Group Key: (array_position(current_schemas(true), n.nspname)), p.partstrat                           +
                                                       |         ->  Sort                                                                                             +
                                                       |               Sort Key: (array_position(current_schemas(true), n.nspname)), p.partstrat                      +
                                                       |               ->  Nested Loop Left Join                                                                      +
                                                       |                     ->  Nested Loop Left Join                                                                +
                                                       |                           ->  Nested Loop                                                                    +
                                                       |                                 Join Filter: (c.relnamespace = n.oid)                                        +
                                                       |                                 ->  Index Scan using pg_class_relname_nsp_index on pg_class c                +
                                                       |                                       Index Cond: (relname = 'pgbench_accounts'::name)                       +
                                                       |                                 ->  Seq Scan on pg_namespace n                                               +
                                                       |                                       Filter: (array_position(current_schemas(true), nspname) IS NOT NULL)   +
                                                       |                           ->  Index Scan using pg_partitioned_table_partrelid_index on pg_partitioned_table p+
                                                       |                                 Index Cond: (partrelid = c.oid)                                              +
                                                       |                     ->  Bitmap Heap Scan on pg_inherits i                                                    +
                                                       |                           R
     SELECT abalance FROM pgbench_accounts WHERE aid = | Index Scan using pgbench_accounts_pkey on pgbench_accounts                                                   +
                                                       |   Index Cond: (aid = 102232)
     BEGIN;                                            |
     END;                                              |
     SELECT substr(query,$1,$2), query_plan from pg_st |
     SELECT substr(query,$1,$2),calls, planid,query_pl | Limit                                                                                                        +
                                                       |   ->  Subquery Scan on pg_stat_monitor                                                                       +
                                                       |         ->  Result                                                                                           +
                                                       |               ->  Sort                                                                                       +
                                                       |                     Sort Key: p.bucket_start_time                                                            +
                                                       |                     ->  Hash Join                                                                            +
                                                       |                           Hash Cond: (p.dbid = d.oid)                                                        +
                                                       |                           ->  Function Scan on pg_stat_monitor_internal p                                    +
                                                       |                           ->  Hash                                                                           +
                                                       |                                 ->  Seq Scan on pg_database d                                                +
                                                       |               SubPlan 1                                                                                      +
                                                       |                 ->  Function Scan on pg_stat_monitor_internal s                                              +
                                                       |                       Filter: (queryid = p.top_queryid)
     select count(*) from pgbench_branches             | Aggregate                                                                                                    +
                                                       |   ->  Seq Scan on pgbench_branches
     UPDATE pgbench_tellers SET tbalance = tbalance +  |
     vacuum pgbench_tellers                            |
     UPDATE pgbench_accounts SET abalance = abalance + |
    (10 rows)
    ```

    The `plan` column does not contain costing, width and other values. This is an expected behavior as each row is an accumulation of statistics based on `plan` and among other key columns. Plan is only available when the [`pgsm_enable_query_plan`](configuration.md#pg_stat_monitorpgsm_enable_query_plan) configuration parameter is enabled.

## Use of actual data or parameters placeholders in queries

You can select whether to see queries with parameters placeholders or actual query data. The benefit of having the full query example is in being able to run the [EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html) command on it to see how its execution was planned. As a result, you can modify the query to make it run better.

## Query type filtering

`pg_stat_monitor` monitors queries per type (``SELECT``, `INSERT`, `UPDATE` or `DELETE`). It classifies the queries in the `cmd_type` and `cmd_type_text` columns. This way you can separate the queries you are interested in and focus on identifying the issues and optimizing query performance.

!!! example
    
    ```sql
    SELECT bucket, substr(query,0, 50) AS query, cmd_type, cmd_type_text FROM pg_stat_monitor WHERE elevel = 0;
    ```

    Output:

    ```{.sql .no-copy}
     bucket |                       query                       | cmd_type | cmd_type_text
    --------+---------------------------------------------------+----------+---------------
          1 | SELECT $2 FROM ONLY "public"."users" x WHERE "id" |        1 | SELECT
          1 | INSERT INTO players (id, about, age) VALUES (gene |        3 | INSERT
          1 | SELECT datname FROM pg_catalog.pg_database  WHERE |        1 | SELECT
          1 | INSERT INTO users(email)                         +|        3 | INSERT
            | SELECT                                           +|          |
            |   $1 || seq || $2                                 |          |
          1 | SELECT * FROM posts LIMIT $1                      |        1 | SELECT
          1 | SELECT * FROM comments LIMIT $1                   |        1 | SELECT
          1 | INSERT INTO posts(user_id, title)                +|        3 | INSERT
            | WITH expanded A                                   |          |
          1 | INSERT INTO comments(user_id, post_id, body)     +|        3 | INSERT
            | WITH                                              |          |
          1 | SELECT bucket, substr(query,$1, $2) AS query, cmd |        1 | SELECT
          1 | SELECT bucket, substr(query,$1, $2) AS query, cmd |        1 | SELECT
          1 | SELECT substr(query,$1,$2), query_plan from pg_st |        1 | SELECT
          1 | SELECT $2 FROM ONLY "public"."posts" x WHERE "id" |        1 | SELECT
          1 | SELECT * FROM users                               |        1 | SELECT
          2 | Update users SET email= $1 where id=$2            |        2 | UPDATE
    (15 rows)
    ```

## Query metadata

Google’s [Sqlcommenter](https://google.github.io/sqlcommenter/) is a useful tool that in a way bridges that gap between ORM libraries and understanding the database performance. And ``pg_stat_monitor`` supports it. 

You can put any key-value data like what client executed a query or if it is a testing vs a production query in the comments using the `/* … */` syntax in your SQL statements. `pg_stat_monitor` parses this information and outputs it in the `comments` column in the `pg_stat_monitor` view. 

Please note that only the latest comment value is preserved per row. The comments may be put in any format that can be parsed by a tool.

For more information about the comments' syntax, refer to the [Sqlcommenter documentation](https://google.github.io/sqlcommenter/).

!!! example

    To store key-value pairs in a single value, the `hstore` data type is used. To start working with hstore, enable the `hstore` extension

    ```sql
    CREATE EXTENSION hstore;
    ```
   
    Create a function to convert data from text form to hstore.

    ```sql
    CREATE FUNCTION text_to_hstore(s text) RETURNS hstore AS $$
    BEGIN
        RETURN hstore(s::text[]);
    EXCEPTION WHEN OTHERS THEN
        RETURN NULL;
    END; $$ LANGUAGE plpgsql STRICT;
    ```
    
    Add comments.

    ```sql
    SELECT 1 AS num /* { "application", java_app, "real_ip", 192.168.1.1} */;
    ```

    Output:

    ```{.sql .no-copy}
     num
    -----
       1
    (1 row)
    ```

    ```sql
    SELECT 1 AS num1,2 AS num2 /* { "application", java_app, "real_ip", 192.168.1.2} */;
    ```

    Output: 

    ```{.sql .no-copy}
     num1 | num2
    ------+------
        1 |    2
    (1 row)
    ```

    ```sql
    SELECT 1 AS num1,2 AS num2, 3 AS num3 /* { "application", java_app, "real_ip", 192.168.1.3} */;
    ```

    Output:

    ```{.sql .no-copy}
     num1 | num2 | num3
    ------+------+------
        1 |    2 |    3
    (1 row)
    ```

    ```sql
    SELECT 1 AS num1,2 AS num2, 3 AS num3, 4 AS num4 /* { "application", psql_app, "real_ip", 192.168.1.3} */;
    ```

    Output:

    ```{.sql .no-copy}
     num1 | num2 | num3 | num4
    ------+------+------+------
        1 |    2 |    3 |    4
    (1 row)
    ```
    
    View query metadata

    ```sql
    SELECT query, text_to_hstore(comments) as comments_tags FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
                                                         query                                                     |                    comments_tags
    ---------------------------------------------------------------------------------------------------------------+-----------------------------------------------------
     SELECT $1 AS num /* { "application", psql_app, "real_ip", 192.168.1.3) */                                     | "real_ip"=>"192.168.1.1", "application"=>"java_app"
     SELECT pg_stat_monitor_reset();                                                                               |
     SELECT query, comments, text_to_hstore(comments) FROM pg_stat_monitor;                                        |
     SELECT $1 AS num1,$2 AS num2, $3 AS num3 /* { "application", java_app, "real_ip", 192.168.1.3} */             | "real_ip"=>"192.168.1.3", "application"=>"java_app"
     select query, text_to_hstore(comments) as comments_tags from pg_stat_monitor;                                 |
     SELECT $1 AS num1,$2 AS num2 /* { "application", java_app, "real_ip", 192.168.1.2} */                         | "real_ip"=>"192.168.1.2", "application"=>"java_app"
     SELECT $1 AS num1,$2 AS num2, $3 AS num3, $4 AS num4 /* { "application", psql_app, "real_ip", 192.168.1.3} */ | "real_ip"=>"192.168.1.3", "application"=>"psql_app"
    (7 rows)
    ```

    ```sql
    select query, text_to_hstore(comments)->'application' as application_name from pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
                                                         query                                                     | application_name
    ---------------------------------------------------------------------------------------------------------------+----------
     SELECT $1 AS num /* { "application", psql_app, "real_ip", 192.168.1.3) */                                     | java_app
     SELECT pg_stat_monitor_reset();                                                                               |
     select query, text_to_hstore(comments)->"real_ip" as comments_tags from pg_stat_monitor;                      |
     select query, text_to_hstore(comments)->$1 from pg_stat_monitor                                               |
     select query, text_to_hstore(comments) as comments_tags from pg_stat_monitor;                                 |
     select query, text_to_hstore(comments)->"application" as comments_tags from pg_stat_monitor;                  |
     SELECT $1 AS num1,$2 AS num2 /* { "application", java_app, "real_ip", 192.168.1.2} */                         | java_app
     SELECT $1 AS num1,$2 AS num2, $3 AS num3 /* { "application", java_app, "real_ip", 192.168.1.3} */             | java_app
     select query, comments, text_to_hstore(comments) from pg_stat_monitor;                                        |
     SELECT $1 AS num1,$2 AS num2, $3 AS num3, $4 AS num4 /* { "application", psql_app, "real_ip", 192.168.1.3} */ | psql_app
    (10 rows)
    ```

    ```sql
    select query, text_to_hstore(comments)->'real_ip' as real_ip from pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
                                                         query                                                     |  real_ip
    ---------------------------------------------------------------------------------------------------------------+-------------
     SELECT $1 AS num /* { "application", psql_app, "real_ip", 192.168.1.3) */                                     | 192.168.1.1
     SELECT pg_stat_monitor_reset();                                                                               |
     select query, text_to_hstore(comments)->"real_ip" as comments_tags from pg_stat_monitor;                      |
     select query, text_to_hstore(comments)->$1 from pg_stat_monitor                                               |
     select query, text_to_hstore(comments) as comments_tags from pg_stat_monitor;                                 |
     select query, text_to_hstore(comments)->"application" as comments_tags from pg_stat_monitor;                  |
     SELECT $1 AS num1,$2 AS num2 /* { "application", java_app, "real_ip", 192.168.1.2} */                         | 192.168.1.2
     SELECT $1 AS num1,$2 AS num2, $3 AS num3 /* { "application", java_app, "real_ip", 192.168.1.3} */             | 192.168.1.3
     select query, comments, text_to_hstore(comments) from pg_stat_monitor;                                        |
     SELECT $1 AS num1,$2 AS num2, $3 AS num3, $4 AS num4 /* { "application", psql_app, "real_ip", 192.168.1.3} */ | 192.168.1.3
    (10 rows)
    ```    

## Top query tracking

Using functions is common. While running, functions can execute queries internally. `pg_stat_monitor` not only keeps track of all executed queries within a function, but also marks that function as top query.

Top query indicates the main query. To illustrate, for the SELECT query that is invoked within a function, the top query is calling this function.

This enables you to backtrack to the originating function and thus simplifies the tracking and analysis.

`pg_stat_monitor` doesn't track top query for [Stored procedures](https://www.postgresql.org/docs/current/xproc.html) and other non-utility statements. To see the statistics data about stored procedures, enable the [`pg_stat_monitor.pgsm_track_utility`](configuration.md#pg_stat_monitorpgsm_track_utility) configuration parameter.

To track subqueries within a function, set the [`pg_stat_monitor.pgsm_track`](configuration.md#pg_stat_monitorpgsm_track) configuration parameter to `all`.

!!! example

    In the following example we create a function `add2` that adds one parameter value to another one and call this function to calculate 1+2.


    ```sql
    CREATE OR REPLACE function add2(int, int) RETURNS int as
    $$
    BEGIN
       return (select $1 + $2);
    END;
    $$ language plpgsql;
    ```

    ```sql
    SELECT add2(1,2);
    ```

    Output:

    ```{.sql .no-copy}
     add2
    -----
       3
    (1 row)
    ```

    The ``pg_stat_monitor`` view shows all executed queries and shows the very first query in a row - calling the `add2` function.

    ```sql
    SELECT queryid, top_queryid, query, top_query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
         queryid      |   top_queryid    |                       query.                           |     top_query
    ------------------+------------------+-------------------------------------------------------------------------+-------------------
     3408CA84B2353094 |                  | select add2($1,$2)                                     |
     762B99349F6C7F31 | 3408CA84B2353094 | SELECT (select $1 + $2)                                | select add2($1,$2)
    (2 rows)
    ```

## Relations

`pg_stat_monitor` provides the list of tables involved in the query in the relations column. This reduces time on identifying the tables and simplifies the analysis. 

!!! example

    **1. List all the table names involved in the query.**

    ```sql
    SELECT relations,query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
                              relations                            |
         query
    ----------------------------------------------------------------+-------------------------------------------------------------------------------------
    -----------------------------------------------------------------------------------------------------
                                                                    | SELECT add2($1,$2)
                                                                    | select pg_stat_monitor_reset()
     {public.pg_stat_monitor*,pg_catalog.pg_database}               | SELECT queryid, top_queryid, query, top_query FROM pg_stat_monitor
                                                                    | (select $1 + $2)
     {public.users}                                                 | select * from users
     {public.posts}                                                 | Select * from posts where user_id=$1
                                                                    | Select & from posts where user_id=3;
     {pg_catalog.pg_database}                                       | SELECT datname FROM pg_catalog.pg_database  WHERE datname LIKE $1                                                                                                                       +
    (4 rows)
    ```

    **Example 2: List all the views and the name of the table in the view. Here we have a view "test_view"**

    ```sql
    \d+ test_view
                              View "public.test_view"
     Column |  Type   | Collation | Nullable | Default | Storage | Description 
    --------+---------+-----------+----------+---------+---------+-------------
     foo_a  | integer |           |          |         | plain   | 
     bar_a  | integer |           |          |         | plain   | 
    View definition:
     SELECT f.a AS foo_a,
        b.a AS bar_a
       FROM foo f,
        bar b;
    ```

    Now when we query the ``pg_stat_monitor``, it will show the view name and also all the table names in the view. Note that the view name is followed by an asterisk (*).

    ```sql
    SELECT relations, query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
          relations      |    query                                                 
    ---------------------+----------------------------------------------------
     {test_view*,foo,bar} | select * from test_view
     {foo,bar}           | select * from foo,bar
    (2 rows)
    ```

## Monitoring queries terminated with ERROR, WARNING and LOG error levels

Monitoring queries that terminated with ERROR, WARNING, LOG states can give useful information to debug an issue. Such messages have the error level (`elevel`), [error code](https://www.postgresql.org/docs/current/errcodes-appendix.html) (`sqlcode`), and error message (`message`). 

`pg_stat_monitor` collects all this information and aggregates it so that you can measure performance for successful and failed queries separately, as well as understand why a particular query failed to execute successfully.

!!! example

    ```sql
    SELECT substr(query,0,50) AS query, decode_error_level(elevel) AS elevel,sqlcode, calls, substr(message,0,50) message
    FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
                           query                       | elevel | sqlcode | calls |                      message
    ---------------------------------------------------+--------+---------+-------+---------------------------------------------------
     select substr(query,$1,$2) as query, decode_error |        |       0 |     1 |
     select bucket,substr(query,$1,$2),decode_error_le |        |       0 |     3 |
                                                       | LOG    |       0 |     1 | database system is ready to accept connections
     select 1/0;                                       | ERROR  |     130 |     1 | division by zero
                                                       | LOG    |       0 |     1 | database system was shut down at 2020-11-11 11:37
     select $1/$2                                      |        |       0 |     1 |
    (6 rows)
              11277.79 | SELECT * FROM foo
    ```

## Histogram

Histogram (the `resp_calls` parameter) provides a visual representation of query performance data distributed across buckets. With the help of the histogram function, you can view a timing/calling data graph in response to an SQL query.

The histogram output is controlled by the following configuration parameters:

* `pgsm_histogram_min` – the minimum execution time for a query, in ms (default 1)
* `pgsm_histogram_max` – the maximum execution time for a query, in ms (default 10000)
* `pgsm_histogram_buckets` – the maximum number of buckets to be shown in histogram (default 20)

!!! example
    
    ```sql
    SELECT bucket, queryid, resp_calls, query FROM pg_stat_monitor;
    ```

    Output:

    ```{.sql .no-copy}
    bucket |       queryid        |                  resp_calls                   |             query
    --------+----------------------+-----------------------------------------------+--------------------------------
          1 | -7911769593731908992 | {1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0} | SELECT pg_stat_monitor_reset()
          3 |  2920803561901199087 | {0,0,0,0,0,0,0,0,0,1,0,0,0,1,1,1,2,1,0,0,0,0} | SELECT pg_sleep($1)
    (2 rows)                                        |                  |       |
    ```

    ```sql
    SELECT * FROM histogram(3, '2920803561901199087') AS a(range TEXT, freq INT, bar TEXT);
    ```

    Output:

    {% raw %}
    ```{.sql .no-copy}
               range           | freq |                                            bar
    ---------------------------+------+--------------------------------------------------------------------------------------------
     {{0.000 - 1.000}          |    0 |
      (1.000 - 2.778}          |    0 |
      (2.778 - 4.162}          |    0 |
      (4.162 - 6.623}          |    0 |
      (6.623 - 11.000}         |    0 |
      (11.000 - 18.783}        |    0 |
      (18.783 - 32.623}        |    0 |
      (32.623 - 57.234}        |    0 |
      (57.234 - 101.000}       |    0 |
      (101.000 - 178.827}      |    1 | ■■■■■■■■■■■■■■■
      (178.827 - 317.226}      |    0 |
      (317.226 - 563.338}      |    0 |
      (563.338 - 1000.994}     |    0 |
      (1000.994 - 1779.268}    |    1 | ■■■■■■■■■■■■■■■
      (1779.268 - 3163.256}    |    1 | ■■■■■■■■■■■■■■■
      (3163.256 - 5624.371}    |    1 | ■■■■■■■■■■■■■■■
      (5624.371 - 10000.920}   |    2 | ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
      (10000.920 - 17783.643}  |    1 | ■■■■■■■■■■■■■■■
      (17783.643 - 31623.492}  |    0 |
      (31623.492 - 56234.598}  |    0 |
      (56234.598 - 100000.000} |    0 |
      (100000.000 - ...}}      |    0 |
    (22 rows)
    ```
    {% endraw %}
    
For pg_stat_monitor version 1.1.1 and earlier, the output shows the time generated automatically based on the total number of buckets in the resp_calls field which corresponds to the value specified by the user in the pgsm_histogram_buckets configuration parameter (defaults to 10).

Starting with version 2.0.0 the histogram output includes two additional buckets for queries whose execution time falls out of the min/max time range specified by the user. The first bucket starts with 0 and ends with the pgsm_histogram_min value. Another bucket starts from the pgsm_histogram_max time and lasts to the infinity.

These additional buckets enable users to see the how the data is distributed within the full range of values, not only the values specified within the min and max time range.

!!! note 

    Additional buckets are created only if:

    * the `pgsm_histogram_min` value is greater than `0` 
    * the `pgsm_histogram_max` value is less than the maximum possible value (`2147483647`)

The output shows to which bucket a value belongs to:

* `()` show that the data is excluded form the range 
* `{}` indicate the data as included in the range. 

For example, in the data range (1.000 - 2.778} and (2.778 - 4.162}, the value 2.778 belongs to the first bucket.




