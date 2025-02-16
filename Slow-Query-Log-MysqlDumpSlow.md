## Slow Query Log - mysqldumpslow utility
The MySQL slow query log contains information about queries that take a long time to execute. mysqldumpslow parses MySQL slow query log files and summarizes their contents.

### mysqldumpslow Options

#### Option Name and Description
-a	Do not abstract all numbers to N and strings to 'S'
-n	Abstract numbers with at least the specified digits
--debug	Write debugging information
-g	Only consider statements that match the pattern
--help	Display help message and exit
-h	Host name of the server in the log file name
-i	Name of the server instance
-l	Do not subtract lock time from total time
-r	Reverse the sort order
-s	How to sort output
-t	Display only first num queries
--verbose	Verbose mode

```sql
mysql> select 'start', sleep(25), 'end';
+-------+-----------+-----+
| start | sleep(25) | end |
+-------+-----------+-----+
| start |         0 | end |
+-------+-----------+-----+
1 row in set (25.00 sec)

mysql> select * from city order by district limit 5;
+------+---------------+-------------+--------------------+------------+
| ID   | Name          | CountryCode | District           | Population |
+------+---------------+-------------+--------------------+------------+
| 3285 | Taiping       | TWN         |                    |     165524 |
| 3293 | Taliao        | TWN         |                    |     115897 |
| 3294 | Kueishan      | TWN         |                    |     112195 |
| 3563 | Ciudad Losada | VEN         |                    |     134501 |
|  909 | Sohumi        | GEO         | Abhasia [Aphazeti] |     111700 |
+------+---------------+-------------+--------------------+------------+
5 rows in set (0.01 sec)
```

With above we got some data in slow query log.
```sh
ls -lrth /var/log/mysql/slowqueries.log
-rw-r--r-- 1 mysql mysql 2.1K Feb 16 21:58 /var/log/mysql/slowqueries.log
```

Run mysqldumpslow on sloq query log. It should show the problematic queries, time they took, any locks on them, rows they fetched and which user ran these queries.
```sh
mysqldumpslow /var/log/mysql/slowqueries.log
```

Though, when I ran it, I had got below message. 
```sh
root@ip:~# mysqldumpslow /var/log/mysql/slowqueries.log
"mysqldumpslow.sh" is not currently compatible with Percona extended slow query
log format. Please use "pt-query-digest" from Percona Toolkit instead
(https://www.percona.com/doc/percona-toolkit/2.2/pt-query-digest.html).
```

### Example output
```sh
Reading mysql slow query log from /var/log/mysql/slowqueries.log
Count: 1  Time=4.32s (4s)  Lock=0.00s (0s)  Rows=0.0 (0), root[root]@localhost
 insert into t2 select * from t1

Count: 3  Time=2.53s (7s)  Lock=0.00s (0s)  Rows=0.0 (0), root[root]@localhost
 insert into t2 select * from t1 limit N

Count: 3  Time=2.13s (6s)  Lock=0.00s (0s)  Rows=0.0 (0), root[root]@localhost
 insert into t1 select * from t1
```