# `pg_stat_monitor` Documentation

**pg_stat_monitor** is a **_Query Performance Monitoring_** tool for PostgreSQL. `pg_stat_monitor` collects performance statistics and provides query performance insights in a single view and graphically in histogram. 
These insights allow database users to understand query origins, execution, planning statistics and details, query information, and metadata. This significantly improves observability, enabling users to debug and tune query performance. 

!!! admonition ""

    This is the documentation for the latest release, **`pg_stat_monitor` {{release}}** ([Release notes](release-notes/{{release}}.md)). 

## How `pg_stat_monitor` works?

`pg_stat_monitor` is developed on the basis of `pg_stat_statements` as its more advanced replacement. While `pg_stat_statements` provides ever-increasing metrics, `pg_stat_monitor` aggregates the collected data, saving user efforts for doing it themselves. `pg_stat_monitor`  stores statistics in configurable time-based units – _buckets_. Such bucket-based data collection allows focusing on statistics generated for shorter time periods and makes query timing information such as max/min/mean time more accurate.

`pg_stat_monitor` tracks the following operations:

* statements
* queries
* functions
* stored procedures and other non-utility statements

[Compare extensions](comparison.md){.md-button}

## Features

* **Time Interval Grouping:** Instead of supplying one set of ever-increasing counts, `pg_stat_monitor` computes stats for a configured number of time intervals - time buckets. This allows for much better data accuracy, especially in the case of high resolution or unreliable networks.
* **Multi-Dimensional Grouping:** While `pg_stat_statements` groups counters by userid, dbid, queryid,  `pg_stat_monitor` uses a more detailed group for higher precision. This allows a user to drill down into the performance of queries.
* **Capture Actual Parameters in the Queries:** `pg_stat_monitor` allows you to choose if you want to see queries with placeholders for parameters or actual parameter data. This simplifies debugging and analysis processes by enabling users to execute the same query.
* **Query Plan:** Each SQL is now accompanied by its actual plan that was constructed for its execution. That's a huge advantage if you want to understand why a particular query is slower than expected.
* **Tables Access Statistics for a Statement:** This allows us to easily identify all queries that accessed a given table. This set is at par with the information provided by the `pg_stat_statements`.
* **Histogram:** Visual representation is very helpful as it can help identify issues. With the help of the histogram function, one can now view a timing/calling data histogram in response to an SQL query. And yes, it even works in psql.


## Availability 

`pg_stat_monitor` is compatible with:

* PostgreSQL provided by PostgreSQL Global Development Group (PGDG) 
* [Percona Distribution for PostgreSQL](https://www.percona.com/software/postgresql-distribution).

### Supported  versions

The `pg_stat_monitor` should work on the latest version of both [Percona Distribution for PostgreSQL](https://www.percona.com/software/postgresql-distribution) and PostgreSQL, but is only tested with these versions:

| **Distribution** | **Version**     | **Provider** |
| ---------------- | --------------- | ------------ |
|[Percona Distribution for PostgreSQL](https://www.percona.com/software/postgresql-distribution)| [11](https://www.percona.com/downloads/percona-postgresql-11/LATEST/), [12](https://www.percona.com/downloads/postgresql-distribution-12/LATEST/), [13](https://www.percona.com/downloads/postgresql-distribution-13/LATEST/), [14](https://www.percona.com/downloads/postgresql-distribution-14/LATEST/), [15](https://www.percona.com/downloads/postgresql-distribution-15/LATEST/), [16](https://www.percona.com/downloads/postgresql-distribution-16/LATEST/) and [17](https://www.percona.com/downloads/postgresql-distribution-17/LATEST/)| Percona|
| [PostgreSQL](https://www.postgresql.org/download/)       | 11, 12, 13, 14, 15, 16 and 17 | PostgreSQL Global Development Group (PGDG) |

The RPM (for RHEL and CentOS) and the DEB (for Debian and Ubuntu) packages are available from Percona repositories for PostgreSQL versions [11](https://www.percona.com/downloads/percona-postgresql-11/LATEST/), [12](https://www.percona.com/downloads/postgresql-distribution-12/LATEST/), [13](https://www.percona.com/downloads/postgresql-distribution-13/LATEST/), [14](https://www.percona.com/downloads/postgresql-distribution-14/LATEST/), [15](https://www.percona.com/downloads/postgresql-distribution-15/LATEST/), [16](https://www.percona.com/downloads/postgresql-distribution-16/LATEST/) and [17](https://www.percona.com/downloads/postgresql-distribution-17/LATEST/).

The RPM packages are also available in the official PostgreSQL (PGDG) YUM repositories.

[Install and get started](install.md){.md-button}


## Get engaged

* [Become a contributor](contributing.md).
* [Reach out to the community on forum](https://forums.percona.com/c/postgresql/pg-stat-monitor/69).


