# User Guide

This document describes the features, functions and configuration of the ``pg_stat_monitor`` extension and gives some usage examples. For how to install and set up ``pg_stat_monitor``, see the [Installation instructions](install.md).

## Features

The following are the key features of pg_stat_monitor:

* [Time buckets](#time-buckets),
* [Table and index access statistics per statement](#table-and-index-access-statistics-per-statement),
* Query statistics:
   * [Query and client information](#query-and-client-information),
   * [Query timing information](#query-timing-information),
   * [Query execution plan information](#query-execution-plan-information),
   * [Use of actual data or parameters placeholders in queries](#use-of-actual-data-or-parameters-placeholders-in-queries),
   * [Query type filtering](#query-type-filtering),
   * [Query metadata supporting Google’s Sqlcommentor](#query-metadata),
* [Top query tracking](#top-query-tracking),
* [Relations](#relations) - showing tables involved in a query,
* [Monitoring of queries terminated with ERROR, WARNING and LOG error levels](#monitoring-of-queries-terminated-with-error-warning-and-log-error-levels),
* [Integration with Percona Monitoring and Management (PMM) tool](#integration-with-pmm),
* [Histograms](#histogram) - visual representation of query performance.


### Time buckets

Instead of supplying one set of ever-increasing counts, `pg_stat_monitor` computes stats for a configured number of time intervals; time buckets. This allows for much better data accuracy, especially in the case of high-resolution or unreliable networks. 

### Table and index access statistics per statement

`pg_stat_monitor` collects the information about what tables were accessed by a statement. This allows you to identify all queries which access a given table easily.


### Query and client information

`pg_stat_monitor` provides  additional metrics for detailed analysis of query performance from various perspectives, including client connection details like user name, application name, IP address to name a few relevant columns.
With this information, `pg_stat_monitor` enables users to track a query to the originating application. More details about the application or query may be incorporated in the SQL query in a [Google’s Sqlcommenter](https://google.github.io/sqlcommenter/) format.

To see how it works, refer to the [usage example](#query-information)

### Query timing information

Understanding query execution time stats helps you identify what affects query performance and take measures to optimize it. `pg_stat_monitor` collects the total, min, max and average (mean) time it took to execute a particular query and provides this data in separate columns. See the [Query timing information](#query-timing-information_1) example for the sample output.


### Query execution plan information

Every query has a plan that was constructed for its executing. Collecting the query plan information as well as monitoring query plan timing helps you understand how you can modify the query to optimize its execution. It also helps make communication about the query clearer when discussing query performance with other DBAs and application developers.

See the [Query execution plan](#query-execution-plan) example for the sample output.

### Use of actual data or parameters placeholders in queries

You can select whether to see queries with parameters placeholders or actual query data. The benefit of having the full query example is in being able to run the [EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html) command on it to see how its execution was planned. As a result, you can modify the query to make it run better.

### Query type filtering

`pg_stat_monitor` monitors queries per type (``SELECT``, `INSERT`, `UPDATE` or `DELETE`) and classifies them accordingly in the `cmd_type` column. This way you can separate the queries you are interested in and focus on identifying the issues and optimizing query performance.

See the [Query type filtering example](#query-type-filtering_1) for the sample output.

### Query metadata

Google’s Sqlcommenter is a useful tool that in a way bridges that gap between ORM libraries and understanding database performance. And ``pg_stat_monitor`` supports it. So, you can now put any key-value data (like what client executed a query or if it is testing vs production query) in the comments in `/* … */` syntax in your SQL statements, and the information will be parsed by `pg_stat_monitor` and made available in the comments column in the `pg_stat_monitor` view. For details on the comments’ syntax, see [Sqlcommenter documentation](https://google.github.io/sqlcommenter/).

To see how it works, see the [Query metadata](#query-metadata_1) example.

### Top query tracking

Using functions is common. While running, functions can execute queries internally. `pg_stat_monitor` not only keeps track of all executed queries within a function, but also marks that function as top query.

Top query indicates the main query. To illustrate, for the SELECT query that is invoked within a function, the top query is calling this function.

This enables you to backtrack to the originating function and thus simplifies the tracking and analysis.

Find more details in the [usage example](#top-query-tracking_1).

### Relations

`pg_stat_monitor` provides the list of tables involved in the query in the relations column. This reduces time on identifying the tables and simplifies the analysis. To learn more, see the [usage examples](#relations_1)

### Monitoring queries terminated with ERROR, WARNING and LOG error levels

Monitoring queries that terminate with ERROR, WARNING, LOG states can give useful information to debug an issue. Such messages have the error level (`elevel`), error code (`sqlcode`), and error message (`message`).  `pg_stat_monitor` collects all this information and aggregates it so that you can measure performance for successful and failed queries separately, as well as understand why a particular query failed to execute successfully.

Find details in the [usage example](#queries-terminated-with-errors)

### Histogram

Histogram (the `resp_calls` parameter) provides a visual representation of query performance data distributed across buckets. With the help of the histogram function, you can view a timing/calling data graph in response to an SQL query.

The histogram output is controlled by the following configuration parameters:

* `pgsm_histogram_min` – the minimum execution time for a query, in ms (default 1)
* `pgsm_histogram_max` – the maximum execution time for a query, in ms (default 10000)
* `pgsm_histogram_buckets` – the maximum number of buckets to be shown in histogram (default 20)


Learn more about using histograms from the [usage example](#histogram_1).

## Usage examples

Note that the column names differ depending on the PostgreSQL version you are using. The following usage examples are provided for PostgreSQL version 13.
For versions 12 and 11, please consult the [pg_stat_monitor reference](reference.md).

### Querying buckets

```sql
SELECT bucket, bucket_start_time, query,calls FROM pg_stat_monitor ORDER BY bucket;
```

Output:
```
-[ RECORD 1 ]-----+------------------------------------------------------------------------------------
bucket | 0
bucket_start_time | 2021-10-22 11:10:00
query | select bucket, bucket_start_time, query,calls from pg_stat_monitor order by bucket;
calls | 1
```

The `bucket` parameter shows the number of a bucket for which a given record belongs.

The `bucket_start_time` shows the start time of the bucket.
`query` shows the actual query text.
`calls` shows how many times a given query was called.

### Query information

**Example 1: Shows the usename, database name, unique queryid hash, query, and the total number of calls of that query.**

```sql
SELECT userid,  datname, queryid, substr(query,0, 50) AS query, calls FROM pg_stat_monitor;
```

Output:

```
 userid  | datname  |     queryid      |                       query                       | calls
---------+----------+------------------+---------------------------------------------------+-------
 vagrant | postgres | 939C2F56E1F6A174 | END                                               |   561
 vagrant | postgres | 2A4437C4905E0E23 | SELECT abalance FROM pgbench_accounts WHERE aid = |   561
 vagrant | postgres | 4EE9ED0CDF143477 | SELECT userid,  datname, queryid, substr(query,$1 |     1
 vagrant | postgres | 8867FEEB8A5388AC | vacuum pgbench_branches                           |     1
 vagrant | postgres | 41D1168FB0733CAB | select count(*) from pgbench_branches             |     1
 vagrant | postgres | E5A889A8FF37C2B1 | UPDATE pgbench_accounts SET abalance = abalance + |   561
 vagrant | postgres | 4876BBA9A8FCFCF9 | truncate pgbench_history                          |     1
 vagrant | postgres | 22B76AE84689E4DC | INSERT INTO pgbench_history (tid, bid, aid, delta |   561
 vagrant | postgres | F6DA9838660825CA | vacuum pgbench_tellers                            |     1
 vagrant | postgres | 214646CE6F9B1A85 | BEGIN                                             |   561
 vagrant | postgres | 27462943E814C5B5 | UPDATE pgbench_tellers SET tbalance = tbalance +  |   561
 vagrant | postgres | 4F66D46F3D4151E  | SELECT userid,  dbid, queryid, substr(query,0, 50 |     1
 vagrant | postgres | 6A02C123488B95DB | UPDATE pgbench_branches SET bbalance = bbalance + |   561
(13 rows)

```

**Example 2: Shows the connected application details.**

```sql
SELECT application_name, client_ip, substr(query,0,100) as query FROM pg_stat_monitor;
```

Output:

```
 application_name | client_ip |                                                query
------------------+-----------+-----------------------------------------------------------------------------------------------------
 pgbench          | 127.0.0.1 | truncate pgbench_history
 pgbench          | 127.0.0.1 | SELECT abalance FROM pgbench_accounts WHERE aid = $1
 pgbench          | 127.0.0.1 | UPDATE pgbench_accounts SET abalance = abalance + $1 WHERE aid = $2
 pgbench          | 127.0.0.1 | BEGIN;
 pgbench          | 127.0.0.1 | INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES ($1, $2, $3, $4, CURRENT_TIMESTAMP
 pgbench          | 127.0.0.1 | END;
 pgbench          | 127.0.0.1 | vacuum pgbench_branches
 pgbench          | 127.0.0.1 | UPDATE pgbench_tellers SET tbalance = tbalance + $1 WHERE tid = $2
 pgbench          | 127.0.0.1 | vacuum pgbench_tellers
 pgbench          | 127.0.0.1 | UPDATE pgbench_branches SET bbalance = bbalance + $1 WHERE bid = $2
 pgbench          | 127.0.0.1 | select o.n, p.partstrat, pg_catalog.count(i.inhparent) from pg_catalog.pg_class as c join pg_catalo
 psql             | 127.0.0.1 | SELECT application_name, client_ip, substr(query,$1,$2) as query FROM pg_stat_monitor
 pgbench          | 127.0.0.1 | select count(*) from pgbench_branches
(13 rows)
```

### Query timing information

```sql
SELECT  userid,  total_time, min_time, max_time, mean_time, query FROM pg_stat_monitor;
```

Output:

```
 userid |     total_time     |      min_time      |      max_time      |     mean_time      |                              query
--------+--------------------+--------------------+--------------------+--------------------+------------------------------------------------------------------
     10 |               0.14 |               0.14 |               0.14 |               0.14 | select * from pg_stat_monitor_reset()
     10 |               0.19 |               0.19 |               0.19 |               0.19 | select userid,  dbid, queryid, query from pg_stat_monitor
     10 |               0.30 |               0.13 |               0.16 |               0.15 | select bucket, bucket_start_time, query from pg_stat_monitor
     10 |               0.29 |               0.29 |               0.29 |               0.29 | select userid,  dbid, queryid, query, calls from pg_stat_monitor
     10 |           11277.79 |           11277.79 |           11277.79 |
```

### Query execution plan

```sql
SELECT substr(query,0,50), query_plan from pg_stat_monitor limit 10;
```

Output:

```
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

The `plan` column does not contain costing, width and other values. This is an expected behavior as each row is an accumulation of statistics based on `plan` and amongst other key columns. Plan is only available when the `pgsm_enable_query_plan` configuration parameter is enabled.

### Query type filtering

``pg_stat_monitor`` monitors queries per type (`SELECT`, `INSERT`, `UPDATE` or `DELETE`) and classifies them accordingly in the ``cmd_type`` column thus reducing your efforts.

```sql
SELECT bucket, substr(query,0, 50) AS query, cmd_type FROM pg_stat_monitor WHERE elevel = 0;
```

Output:

```
 bucket |                       query                       | cmd_type
--------+---------------------------------------------------+----------
      4 | END                                               |
      4 | SELECT abalance FROM pgbench_accounts WHERE aid = | SELECT
      4 | vacuum pgbench_branches                           |
      4 | select count(*) from pgbench_branches             | SELECT
      4 | UPDATE pgbench_accounts SET abalance = abalance + | UPDATE
      4 | truncate pgbench_history                          |
      4 | INSERT INTO pgbench_history (tid, bid, aid, delta | INSERT
      5 | SELECT relations query FROM pg_stat_monitor       | SELECT
      9 | SELECT bucket, substr(query,$1, $2) AS query, cmd |
      4 | vacuum pgbench_tellers                            |
      4 | BEGIN                                             |
      5 | SELECT relations,query FROM pg_stat_monitor       | SELECT
      4 | UPDATE pgbench_tellers SET tbalance = tbalance +  | UPDATE
      4 | UPDATE pgbench_branches SET bbalance = bbalance + | UPDATE
(14 rows)
```

### Query metadata

The `comments` column contains any text wrapped in `“/*”` and `“*/”` comment tags. The `pg_stat_monitor` extension picks up these comments and makes them available in the comments column. Please note that only the latest comment value is preserved per row. The comments may be put in any format that can be parsed by a tool.

```sql
CREATE EXTENSION hstore;
CREATE FUNCTION text_to_hstore(s text) RETURNS hstore AS $$
BEGIN
    RETURN hstore(s::text[]);
EXCEPTION WHEN OTHERS THEN
    RETURN NULL;
END; $$ LANGUAGE plpgsql STRICT;

SELECT 1 AS num /* { "application", java_app, "real_ip", 192.168.1.1} */;
```

Output:

```
 num
-----
   1
(1 row)
```

```sql
SELECT 1 AS num1,2 AS num2 /* { "application", java_app, "real_ip", 192.168.1.2} */;
```

Output: 

```
 num1 | num2
------+------
    1 |    2
(1 row)
```

```sql
SELECT 1 AS num1,2 AS num2, 3 AS num3 /* { "application", java_app, "real_ip", 192.168.1.3} */;
```

Output:

```
 num1 | num2 | num3
------+------+------
    1 |    2 |    3
(1 row)
```

```sql
SELECT 1 AS num1,2 AS num2, 3 AS num3, 4 AS num4 /* { "application", psql_app, "real_ip", 192.168.1.3} */;
```

Output:

```
 num1 | num2 | num3 | num4
------+------+------+------
    1 |    2 |    3 |    4
(1 row)
```

```sql
SELECT query, text_to_hstore(comments) as comments_tags FROM pg_stat_monitor;
```

Output:

```
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

```
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
```
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

### Top query tracking

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

```
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

```
     queryid      |   top_queryid    |                       query.                           |     top_query
------------------+------------------+-------------------------------------------------------------------------+-------------------
 3408CA84B2353094 |                  | select add2($1,$2)                                     |
 762B99349F6C7F31 | 3408CA84B2353094 | SELECT (select $1 + $2)                                | select add2($1,$2)
(2 rows)
```

### Relations

**Example 1: List all the table names involved in the query.**

```sql
SELECT relations,query FROM pg_stat_monitor;
```

Output:

```
           relations                  |                                                query
-------------------------------+------------------------------------------------------------------------------------------------------
                                      | END
 {public.pgbench_accounts}            | SELECT abalance FROM pgbench_accounts WHERE aid = $1
                                      | vacuum pgbench_branches
 {public.pgbench_branches}            | select count(*) from pgbench_branches
 {public.pgbench_accounts}            | UPDATE pgbench_accounts SET abalance = abalance + $1 WHERE aid = $2
                                      | truncate pgbench_history
 {public.pgbench_history}             | INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES ($1, $2, $3, $4, CURRENT_TIMESTAMP)
 {public.pg_stat_monitor,pg_catalog.pg_database} | SELECT relations query FROM pg_stat_monitor
                                      | vacuum pgbench_tellers
                                      | BEGIN
 {public.pgbench_tellers}             | UPDATE pgbench_tellers SET tbalance = tbalance + $1 WHERE tid = $2
 {public.pgbench_branches}            | UPDATE pgbench_branches SET bbalance = bbalance + $1 WHERE bid = $2
(12 rows)
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

```
      relations      |    query                                                 
---------------------+----------------------------------------------------
 {test_view*,foo,bar} | select * from test_view
 {foo,bar}           | select * from foo,bar
(2 rows)
```

### Queries terminated with errors

```sql
SELECT substr(query,0,50) AS query, decode_error_level(elevel) AS elevel,sqlcode, calls, substr(message,0,50) message
FROM pg_stat_monitor;
```

Output:

```
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

### Histogram

Histogram (the `resp_calls` parameter) provides a visual representation of query performance. With the help of the histogram function, you can view a timing/calling data histogram in response to a SQL query.


```sql
SELECT substr(query, 0,50) as query, queryid, calls, resp_calls FROM pg_stat_monitor ORDER BY query COLLATE "C";
```

Output:

```
                       query                       |     queryid      | calls | resp_calls
---------------------------------------------------+------------------+-------+------------
 SELECT * FROM histogram($1, $2) AS a(range TEXT,  | 69FEFA985E701794 |     1 | {1,0,0}
 SELECT * FROM histogram($1, $2) AS a(range TEXT,  | 69FEFA985E701794 |     1 | {1,0,0}
 SELECT a.attname,                                +| 96F8E4B589EF148F |     1 | {1,0,0}
   pg_catalog.format_type(a.attt                   |                  |       |
 SELECT bucket, bucket_start_time, query,calls FRO | EB658FD0FE5E4203 |     2 | {2,0,0}
 SELECT c.oid,                                    +| 34B888E5C844519C |     1 | {1,0,0}
   n.nspname,                                     +|                  |       |
   c.relname                                      +|                  |       |
 FROM pg_ca                                        |                  |       |
```

```sql
SELECT * FROM histogram(0, 'AA25AA7E49F081F4') AS a(range TEXT, freq INT, bar TEXT);
```

Output:

```text
       range        | freq |              bar
--------------------+------+--------------------------------
  {{0 - 3}          |    2 | ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  (3 - 10}          |    0 |
  (10 - 31}         |    1 | ■■■■■■■■■■■■■■■
  (31 - 100}        |    0 |
  (100 - 316}       |    0 |
  (316 - 100}       |    0 |
  (1000 - 3162}     |    0 |
  (3162 - 10000}    |    0 |
  (10000 - 31622}   |    0 |
  (31622 - 100000}} |    0 |
(10 rows)
```

For `pg_stat_monitor` version 1.1.1 and earlier, the output shows the time generated automatically based on the total number of buckets in the `resp_calls` field which corresponds to the value specified by the user in the `pgsm_histogram_buckets` configuration parameter (defaults to 10).

Starting with version 2.0.0 the histogram output includes two additional buckets for queries whose execution time falls out of the min/max time range specified by the user. The first bucket starts with 0 and ends with the histogram_min value. Another bucket starts from the histogram_max time and lasts to the infinity.  

These additional buckets enable users to see the how the data is distributed within the full range of values, not only the values specified within the min and max time range. 

!!! note 

    Additional buckets are created only if the `pgsm_histogram_min` value is greater than `0` if the `pgsm_histogram_max` value is less than the maximum possible value (`2147483647`)

The output clearly shows to which bucket a value belongs to:

* `()` show that the data is excluded form the range 
* `{}` indicate the data as included in the range. 

For example, in the data range {{0 – 3} and (3 – 10}}, the value 3 belongs to the first bucket.


