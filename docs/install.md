# Install `pg_stat_monitor`

## Supported platforms

The PostgreSQL YUM repository supports `pg_stat_monitor` for all [supported versions](#supported-versions) for the following platforms:

* Red Hat Enterprise/Rocky/CentOS/Oracle Linux 7 and 8
* Fedora 33 and 34

Find the list of supported platforms for `pg_stat_monitor` within [Percona Distribution for PostgreSQL](https://www.percona.com/software/postgresql-distribution) on the [Percona Release Lifecycle Overview](https://www.percona.com/services/policies/percona-software-support-lifecycle#pgsql) page.


## Installation guidelines

Choose the installation source:

=== "Percona repositories"

    To install `pg_stat_monitor` from Percona repositories, you need to use the `percona-release` repository management tool.

    1. [Install percona-release](https://www.percona.com/doc/percona-repo-config/installing.html).
    2. Enable Percona repository:

        ``` sh
        sudo percona-release setup ppgXX
        ```

        Replace `XX` with the desired PostgreSQL version. For example, to install `pg_stat_monitor ` for PostgreSQL 13, specify `ppg13`.

    3. Install `pg_stat_monitor` package

        === "On Debian and Ubuntu"

            ``` sh
            sudo apt-get install percona-pg-stat-monitor13
            ```

        === "On Red Hat Enterprise Linux"

            ``` sh
            yum install percona-pg-stat-monitor13
            ``` 

 
=== "PostgreSQL PGDG yum repositories"

    Install the PostgreSQL repositories following the instructions in the [Linux downloads (Red Hat family)](https://www.postgresql.org/download/linux/redhat/) chapter in PostgreSQL documentation.

    Install `pg_stat_monitor`:

    ```sh
    dnf install -y pg_stat_monitor_<VERSION>
    ```

    Replace the `<VERSION>` variable with the PostgreSQL version you are using (e.g. specify `pg_stat_monitor_13` for PostgreSQL 13)

=== "PGXN"

    You can install `pg_stat_monitor` from PGXN (PostgreSQL Extensions Network) using the [PGXN client](https://pgxn.github.io/pgxnclient/).

    Use the following command:

    ```sh
    pgxn install pg_stat_monitor
    ```

=== "Build from source code"

    To build `pg_stat_monitor` from source code, you require the following:

    * git
    * make
    * gcc
    * pg_config

    You can download the source code of the latest release of `pg_stat_monitor` from [the releases page on GitHub](https://github.com/Percona/pg_stat_monitor/releases) or using git:


    ```sh
    git clone git://github.com/Percona/pg_stat_monitor.git
    ```

    Compile and install the extension

    ```sh
    cd pg_stat_monitor
    make USE_PGXS=1
    make USE_PGXS=1 install
    ```


## Remove `pg_stat_monitor`

To uninstall `pg_stat_monitor`, do the following:

1. Disable statistics collection. Establish the `psql` session and run the following command :

    ```sql
    ALTER SYSTEM SET pg_stat_monitor.pgsm_enable = 0;
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

4. Restart the `postgresql` instance to apply the changes. The following command restarts PostgreSQL 13. Replace the version value with the one you are using. 

    * On Debian and Ubuntu:

    ```sh
    sudo systemctl restart postgresql.service
    ```

    * On Red Hat Enterprise Linux and CentOS:


    ```sh
    sudo systemctl restart postgresql-13
    ```


