## Upgrade Major version of MySQL (5.7 TO 8.0)

### mysqlcheck - Check tables before upgrade

The mysqlcheck client performs table maintenance. It checks, repairs, optimizes or analyzes tables.
It creates locks on tables, therefore tables are unavailable to other sessions while it is being processed, although for check operatoins, the table is locked with a READ lock only. Table maintenance operatoins can be time consuming for large tables. 

mysqlcheck basically uses the SQL statements CHECK TABLE, REPAIR TABLE, ANALYZE TABLE and OPTIMZE TABLE in a convenient way for the user. All storage engines do not necessarily support all four maintenance operations. In such cases, an error message is displayed. 

There are multiple options for it like --check, --check-only-changed, --check-upgrade, --analyze, --auto-repair, etc.

Below command will check all tables in databases, whether they are ready for upgrade. If there is any table, that can have problems during upgrade, it'll show that.

```sh
ls /usr/bin/mysql*
mysqlcheck -u root -p --all-databases --check-upgrade
```
### MySQL Shell - Check MySQL server before upgrade
There is a script provided by Oracle MySQL, that performs checks before we do the actual upgrade. It is run using MySQL Shell, in this we provide our Target version of MySQL to upgrade to and it performs checks, whether it can be successful or fail. If it fails, then we may think to use some lower minor version.

https://dev.mysql.com/downloads/shell/

### Download MySQL Shell
```sh
wget https://dev.mysql.com/get/Downloads/MySQL-Shell/mysql-shell_8.0.41-1ubuntu24.04_amd64.deb
```

### Install MySQL Shell
```sh
sudo dpkg -i mysql-shell_8.0.41-1ubuntu24.04_amd64.deb
which mysqlsh
```

After running below command, check "errorCount", it should be 0, check "serverVersion", "summary", "warningCount", etc. Mostly, warnings are fine, for example, we may get warning for authentication plugin, as current version may be using "mysql_native_password" but it in higher version, it may be "caching_sha2_password", which can be take care of.
Go through rest of the output of below command too.

### Run checks before Version Upgrade
```sh
mysqlsh -- util check-for-server-upgrade root@localhost --target-version=8.0.30 --output-format=JSON --config-path=/etc/my.cnf
```

### Check currently installed version
```sh
dpkg -l  | grep -i percona
```

### Download Newer Major version

```sh
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/percona-server-common_8.0.30-22-1.jammy_amd64.deb
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/libperconaserverclient21_8.0.30-22-1.jammy_amd64.deb
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/libperconaserverclient21-dev_8.0.30-22-1.jammy_amd64.deb
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/percona-server-client_8.0.30-22-1.jammy_amd64.deb
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/percona-server-server_8.0.30-22-1.jammy_amd64.deb
wget https://downloads.percona.com/downloads/Percona-Server-8.0/Percona-Server-8.0.30-22/binary/debian/jammy/x86_64/percona-server-rocksdb_8.0.30-22-1.jammy_amd64.deb

# Or RPMs for Red Hat based o/s.
wget https://downloads.percona.com/downloads/Percona-Server-LATEST/Percona-Server-8.0.30-22/binary/redhat/7/x86_64/percona-server-shared-compat-8.0.30-22.1.el7.x86_64.rpm
wget https://downloads.percona.com/downloads/Percona-Server-LATEST/Percona-Server-8.0.30-22/binary/redhat/7/x86_64/percona-server-shared-8.0.30-22.1.el7.x86_64.rpm
wget https://downloads.percona.com/downloads/Percona-Server-LATEST/Percona-Server-8.0.30-22/binary/redhat/7/x86_64/percona-server-client-8.0.30-22.1.el7.x86_64.rpm
wget https://downloads.percona.com/downloads/Percona-Server-LATEST/Percona-Server-8.0.30-22/binary/redhat/7/x86_64/percona-server-server-8.0.30-22.1.el7.x86_64.rpm
wget https://downloads.percona.com/downloads/Percona-XtraBackup-LATEST/Percona-XtraBackup-8.0.30-23/binary/redhat/7/x86_64/percona-xtrabackup-80-8.0.30-23.1.el7.x86_64.rpm

ls -lrth
```


### Download, Install and Enable MySQL 8 Repo
```sh
# Download Percona Release repository and install it
wget  https://repo.percona.com/apt/percona-release_latest.generic_all.deb

# Below command will add the repository to /etc/apt/sources.list.d./ directory
sudo apt install gnupg2 lsb-release ./percona-release_latest.generic_all.deb

# Enable the repository
sudo percona-release setup -y ps80
```

### Set environment variable for Major Version (Optional)
```sh
VERSION=8.0.30-22-1
echo $VERSION
```

### Shutdown MySQL Service
```sh
sudo systemctl stop mysql
sudo systemctl status mysql
```

In case of minor upgrade, we don't need to remove the current version, we perform the in-place upgrade but in case of major upgrade, we remove the current version.
Remember, once this version is removed, we can't go back to this version, unless we have full cold backup.

Once we remove the percona-server-shared and percona-server-shared-compat, it removed dependencies too i.e., percona-server-server and the percona-server-client.

### Remove old/current MySQL 5.7 version
```sh
# REMEMBER: When prompted to remove database directory, pick No for that.
sudo apt-get remove libperconaserverclient20:amd64 libperconaserverclient20-dev percona-release percona-server-client-5.7 percona-server-common-5.7 percona-server-server-5.7
dpkg -l  | grep -i percona

# REMEMBER NOT TO USE --purge OPTION, OTHERWISE, YOU MAY LOOSE YOUR DATABASES
```

### Install new Major Version 8.0
```sh
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openldap/libldap-2.5-0_2.5.11+dfsg-1~exp1ubuntu3_amd64.deb
sudo dpkg -i libldap-2.5-0_2.5.11+dfsg-1~exp1ubuntu3_amd64.deb

sudo dpkg -i percona-server-common_8.0.30-22-1.jammy_amd64.deb
sudo dpkg -i libperconaserverclient21_8.0.30-22-1.jammy_amd64.deb
sudo dpkg -i libperconaserverclient21-dev_8.0.30-22-1.jammy_amd64.deb
sudo dpkg -i percona-server-client_8.0.30-22-1.jammy_amd64.deb
sudo dpkg -i percona-server-server_8.0.30-22-1.jammy_amd64.deb
```

#### Verify new version of Percona MySQL
```sh
dpkg -l  | grep -i percona
```

Now, upgrade is automatically performed when we start mysql instance here. It'll go through all datafiles, system files, etc and upgrade them.
### Start MySQL Service
```sh
sudo systemctl status mysql
```

### Verify MySQL Instance
```sh
pidof mysqld
netstat -ntlp | grep 3306
mysql --version
```

### Go through mysql logs and check Notes and Warnings in it.
less /var/log/mysql/error.log
or
less /var/log/mysqld.log

### Check of Databases and Tables
I had created World DB and "table1" table in it before upgrade i.e., in version 5.7 and I can see that they are still there.

#### BEFORE UPGRADE
```sql
~# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.44-48 Percona Server (GPL), Release '48', Revision '497f936a373'

Copyright (c) 2009-2023 Percona LLC and/or its affiliates
Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| world              |
+--------------------+
5 rows in set (0.00 sec)

mysql> use world;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------+
| Tables_in_world |
+-----------------+
| city            |
| country         |
| countrylanguage |
| table1          |
+-----------------+
4 rows in set (0.00 sec)

mysql> select * From table1;
+----+
| id |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  5 |
+----+
5 rows in set (0.00 sec)

mysql> exit
```


#### AFTER UPGRADE
```sql
:~# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.30-22 Percona Server (GPL), Release '22', Revision '7e301439b65'

Copyright (c) 2009-2022 Percona LLC and/or its affiliates
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| world              |
+--------------------+
5 rows in set (0.00 sec)

mysql> use world;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------+
| Tables_in_world |
+-----------------+
| city            |
| country         |
| countrylanguage |
| table1          |
+-----------------+
4 rows in set (0.00 sec)

mysql> select * from table1;
+----+
| id |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
|  5 |
+----+
5 rows in set (0.01 sec)

mysql> show engines;
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                                    | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                             | NULL         | NULL | NULL       |
| MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                                         | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.00 sec)

mysql>
```