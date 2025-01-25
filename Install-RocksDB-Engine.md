## RocksDB Engine Installation

**Step 1:** Install percona RocksDB
```sh 
sudo apt install -y percona-server-rocksdb;
```

**Step 2:** Enable the RocksDB storage engine in Percona Server

Syntax:
```sh
ps-admin --enable-rocksdb -u <mysql_admin_user> -p[mysql_admin_pass] [-S <socket>] [-h <host> -P <port>]
```
Example:
```sh
ps-admin --enable-rocksdb -u root -p

Checking if RocksDB plugin is available for installation ...
INFO: ha_rocksdb.so library for RocksDB found at /usr/lib/mysql/plugin/ha_rocksdb.so.

Checking RocksDB engine plugin status...
INFO: RocksDB engine plugin is not installed.

Installing RocksDB engine...
INFO: Successfully installed RocksDB engine plugin.
```

**Step 3:** Verify RocksDB installation
```sql
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                                    | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| ndbcluster         | NO      | Clustered, fault-tolerant tables                                           | NULL         | NULL | NULL       |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO   | NO         |
| ROCKSDB            | YES     | RocksDB storage engine                                                     | YES          | YES  | YES        |
| InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                             | NULL         | NULL | NULL       |
| ndbinfo            | NO      | MySQL Cluster system information storage engine                            | NULL         | NULL | NULL       |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                                         | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
12 rows in set (0.00 sec)

mysql> create database mydb;
Query OK, 1 row affected (0.01 sec)

mysql> use mydb;
Database changed

mysql> create table mytable1
    -> (id int primary key auto_increment)
    -> ENGINE=RocksDB;
Query OK, 0 rows affected (0.01 sec)

mysql> insert into mytable1 values ();
Query OK, 1 row affected (0.02 sec)

mysql> insert into mytable1 values ();
Query OK, 1 row affected (0.00 sec)

mysql> select * from mytable1;
+----+
| id |
+----+
|  1 |
|  2 |
+----+
2 rows in set (0.00 sec)
```