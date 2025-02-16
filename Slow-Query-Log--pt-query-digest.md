## Slow Query Log - pt-query-digest utility from Percona Toolkit

pt-query-digest utility comes with Percona Toolkit. It can read the queries from the slow query log and SHOW PROCESSLIST statement.

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

### Usage
```sh
pt-query-digest /var/log/mysql/slowqueries.log
```

We can see that report provided by it is quite detailed. "Query_time distribution" is important section, that focuses on offending queries.

We can see in "Exec time" that query that took maximum time is 25 seconds. Rows fetched are 11. In "Profile" we can see most expensive queries and how many calls were made to it.
### Example
```sh
root@ip-172-31-92-192:~# pt-query-digest /var/log/mysql/slowqueries.log

# 150ms user time, 20ms system time, 28.25M rss, 34.29M vsz
# Current date: Sun Feb 16 22:19:02 2025
# Hostname: ip-172-31-92-192
# Files: /var/log/mysql/slowqueries.log
# Overall: 3 total, 3 unique, 0.02 QPS, 0.17x concurrency ________________
# Time range: 2025-02-16T21:56:17 to 2025-02-16T21:58:44
# Attribute          total     min     max     avg     95%  stddev  median
# ============     ======= ======= ======= ======= ======= ======= =======
# Exec time            25s     4ms     25s      8s     25s     12s     6ms
# Lock time            8us       0     4us     2us     3us     1us     3us
# Rows sent             11       1       5    3.67    4.96    1.87    4.96
# Rows examine       3.99k       1   3.99k   1.33k   3.88k   1.83k    4.96
# Rows affecte           0       0       0       0       0       0       0
# Bytes sent         1.00k     127     454     343  441.81  149.70  441.81
# Query size           102      26      44      34   42.48    7.26   31.70
# Bytes receiv         123      33      51      41   49.17    7.19   38.53
# Created tmp            0       0       0       0       0       0       0
# Created tmp            0       0       0       0       0       0       0
# Errno                  0       0       0       0       0       0       0
# Read first             2       0       1    0.67    0.99    0.47    0.99
# Read key               2       0       1    0.67    0.99    0.47    0.99
# Read last              0       0       0       0       0       0       0
# Read next              0       0       0       0       0       0       0
# Read prev              0       0       0       0       0       0       0
# Read rnd               0       0       0       0       0       0       0
# Read rnd nex       3.99k       0   3.98k   1.33k   3.88k   1.83k    4.96
# Sort merge p           0       0       0       0       0       0       0
# Sort range c           0       0       0       0       0       0       0
# Sort rows              5       0       5    1.67    4.96    2.34       0
# Sort scan co           1       0       1    0.33    0.99    0.47       0

# Profile
# Rank Query ID                           Response time  Calls R/Call  V/M
# ==== ================================== ============== ===== ======= ===
#    1 0x8840CF6C7CD71A40075584B3B447F1C3 25.0004 100.0%     1 25.0004  0.00 SELECT
# MISC 0xMISC                               0.0100  0.0%     2  0.0050   0.0 <2 ITEMS>

# Query 1: 0 QPS, 0x concurrency, ID 0x8840CF6C7CD71A40075584B3B447F1C3 at byte 0
# This item is included in the report because it matches --limit.
# Scores: V/M = 0.00
# Time range: all events occurred at 2025-02-16T21:56:17
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count         33       1
# Exec time     99     25s     25s     25s     25s     25s       0     25s
# Lock time      0       0       0       0       0       0       0       0
# Rows sent      9       1       1       1       1       1       0       1
# Rows examine   0       1       1       1       1       1       0       1
# Rows affecte   0       0       0       0       0       0       0       0
# Bytes sent    12     127     127     127     127     127       0     127
# Query size    31      32      32      32      32      32       0      32
# Bytes receiv  31      39      39      39      39      39       0      39
# Created tmp    0       0       0       0       0       0       0       0
# Created tmp    0       0       0       0       0       0       0       0
# Errno          0       0       0       0       0       0       0       0
# Read first     0       0       0       0       0       0       0       0
# Read key       0       0       0       0       0       0       0       0
# Read last      0       0       0       0       0       0       0       0
# Read next      0       0       0       0       0       0       0       0
# Read prev      0       0       0       0       0       0       0       0
# Read rnd       0       0       0       0       0       0       0       0
# Read rnd nex   0       0       0       0       0       0       0       0
# Sort merge p   0       0       0       0       0       0       0       0
# Sort range c   0       0       0       0       0       0       0       0
# Sort rows      0       0       0       0       0       0       0       0
# Sort scan co   0       0       0       0       0       0       0       0
# String:
# End          2025-02-16T21:56:17.604022Z
# Hosts        localhost
# Start        2025-02-16T21:55:52.603616Z
# Users        root
# Query_time distribution
#   1us
#  10us
# 100us
#   1ms
#  10ms
# 100ms
#    1s
#  10s+  ################################################################
# EXPLAIN /*!50100 PARTITIONS*/
select 'start', sleep(25), 'end'\G
```

"#  10s+  ################################################################" above shows that query took more than 10 seconds. We can see bars similarly for other queries and query times.