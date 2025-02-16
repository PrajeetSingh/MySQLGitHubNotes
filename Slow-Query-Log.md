## MySQL Slow Query Log

If we want to identify the queries that run slow and we want to fine tune them, then to get those queries, we have Slow Query Log in MySQL.

As it is a parameter and it puts some additional load on MySQL, it is by default disabled. Once it is enabled, we can use default tool **mysqldumpslow** to get the report or we can use **pt-query-digest** tool provided in Percona Toolkit to get some detailed report.

To log these slow queries, we specify number of seconds in parameter/variable **long_query_time** and any query that takes longer than this give time, is logged.

Not only slow queries, if we want to get list of those queries too, that do not use Indexes, we can list of those queries too by enabling parameter **log-queries-not-using-indexes**

### Some important configuration variables
* slow-query-log  (by default it is disabled)
* long-query-time  (value is specified in seconds)
* slow-query-log-file (location and the log file, to store slow running queries)
* log-queries-not-using-indexes  (if we want to get queries that are not using indexes)
* log-slow-replica-statements  (Setup only on Replica)
* log-slow-admin-statements
* log-slow-extra

**NOTE**: By default, slow query log file is created in the Data Directory (/var/lib/mysql), which is not a good thing, as it may fill up unnecessary space in it and can cause issues. So, better to have log files on a separate file system.

### Check whether Slow Query Log is enabled
```sql
show global variables like '%slow%';

+-----------------------------------+------------------------------------------+
| Variable_name                     | Value                                    |
+-----------------------------------+------------------------------------------+
| log_slow_admin_statements         | OFF                                      |
| log_slow_extra                    | OFF                                      |
| log_slow_filter                   |                                          |
| log_slow_rate_limit               | 1                                        |
| log_slow_rate_type                | session                                  |
| log_slow_replica_statements       | OFF                                      |
| log_slow_slave_statements         | OFF                                      |
| log_slow_sp_statements            | ON                                       |
| log_slow_verbosity                |                                          |
| max_slowlog_files                 | 0                                        |
| max_slowlog_size                  | 0                                        |
| slow_launch_time                  | 2                                        |
| slow_query_log                    | OFF                                      |
| slow_query_log_always_write_time  | 10.000000                                |
| slow_query_log_file               | /var/lib/mysql/ip-172-31-92-192-slow.log |
| slow_query_log_use_global_control |                                          |
+-----------------------------------+------------------------------------------+
16 rows in set (0.00 sec)
```

By running below command, you can find details about these "slow" parameters too.
```sh
mysqld --help --verbose | grep -Ei "slow|long-query|not-using-indexes"
```

### Setup Slow Query Log
Create file and directory (optional) to store the slow query log file and change required permissions
```sh
touch /var/log/mysql/slowqueries.log
chown -R mysql:mysql /var/log/mysql/
```

### Enable Slow Query
Enable required parameters in my.cnf
```sh
vi /etc/mysql/my.cnf
[mysqld]
### Slow Query Log Settings
slow-query-log
long-query-time = 20
slow-query-log-file = /var/log/mysql/slowqueries.log
log-queries-not-using-indexes
log-slow-admin-statements
log-slow-extra
###

mysqld --validate-config
```

### Restart MySQL Service
```sh
systemctl restart mysql
```

### Verify if Slow Query Log is enabled
```sql
show global variables like '%slow%';
+-----------------------------------+--------------------------------+
| Variable_name                     | Value                          |
+-----------------------------------+--------------------------------+
| log_slow_admin_statements         | ON                             |
| log_slow_extra                    | ON                             |
| log_slow_filter                   |                                |
| log_slow_rate_limit               | 1                              |
| log_slow_rate_type                | session                        |
| log_slow_replica_statements       | OFF                            |
| log_slow_slave_statements         | OFF                            |
| log_slow_sp_statements            | ON                             |
| log_slow_verbosity                |                                |
| max_slowlog_files                 | 0                              |
| max_slowlog_size                  | 0                              |
| slow_launch_time                  | 2                              |
| slow_query_log                    | ON                             |
| slow_query_log_always_write_time  | 10.000000                      |
| slow_query_log_file               | /var/log/mysql/slowqueries.log |
| slow_query_log_use_global_control |                                |
+-----------------------------------+--------------------------------+
16 rows in set (0.01 sec)

 show global variables like '%long_query%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 20.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)
```