## Install Percona MySQL 5.7 on Ubuntu

This installation is done using Tarball method.

I tried Percona Repo method on Ubuntu 24 (EC2 instance) but it was failing because of o/s incompatibility issue.

```sh 
# Login as Root
sudo su -

# Run o/s update
apt update 

# Download Percona MySQL 5.7 tarball and extract it
wget https://downloads.percona.com/downloads/Percona-Server-5.7/Percona-Server-5.7.44-48/binary/debian/jammy/x86_64/Percona-Server-5.7.44-48-r497f936a373-jammy-x86_64-bundle.tar
tar -xvf Percona-Server-5.7.44-48-r497f936a373-jammy-x86_64-bundle.tar 

# Install dependencies
apt install -y mysql-common debsums zlib1g-dev libwrap0 libmecab2 libjemalloc2
wget http://archive.ubuntu.com/ubuntu/pool/main/liba/libaio/libaio1_0.3.112-5_amd64.deb
dpkg -i libaio1_0.3.112-5_amd64.deb

# Run o/s udpate again (optional)
apt update 

# Install Percona MySQL 5.7 packages
dpkg -i percona-server-common-5.7_5.7.44-48-1.jammy_amd64.deb 
dpkg -i libperconaserverclient20_5.7.44-48-1.jammy_amd64.deb 
dpkg -i libperconaserverclient20-dev_5.7.44-48-1.jammy_amd64.deb 
dpkg -i percona-server-client-5.7_5.7.44-48-1.jammy_amd64.deb 
dpkg -i percona-server-server-5.7_5.7.44-48-1.jammy_amd64.deb
dpkg -i percona-server-tokudb-5.7_5.7.44-48-1.jammy_amd64.deb
dpkg -i percona-server-rocksdb-5.7_5.7.44-48-1.jammy_amd64.deb

# Ensure that MySQL database is running
systemctl status mysql

# Enabel the RocksDB storage engine
ps-admin --enable-rocksdb -u root -p

# Login to MySQL instance
mysql -u root -p
```

```sql
# Check current databases
show databases;

# Check engines available;
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                                    | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| ROCKSDB            | YES     | RocksDB storage engine                                                     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                                         | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                             | NULL         | NULL | NULL       |
| MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
10 rows in set (0.00 sec)
```