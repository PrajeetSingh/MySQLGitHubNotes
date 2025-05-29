# Position Based Replication (Info is based on MariaDB but relevant to Percona MySQL too)

## Primary Server Setup

* Good to create replication.cnf file and enter below information in that and then "include" it in my.cnf file, otherwise use server.cnf.
* Enable Binary Logging
  * log_bin = /var/log/mysql/binlog/prod-bin
  * log_bin_index = /var/log/mysql/binlog/prod-bin.index
* Setup unique server ID
  * server-id = 1
* Create dedicated replication user
  * CREATE USER 'replicator'@'%' IDENTIFIED BY 'mySecretPassword';
  * GRANT REPLICATOIN SLAVE ON *.* TO 'replicator'@'%';
  * FLUSH PRIVILEGES;
* Set binary log file format
  * binlog_format = ROW

## Replica Server Setup

* Good to create replication.cnf and enter below information in that and then include it in my.cnf file, otherwise use server.cnf file in /etc/my.cnf.d/.
* Enable Relay Logs
  * relay_log = /var/log/mysql/relay/replica-bin
  * relay_log_index = /var/log/mysql/relay/replica-bin.index
* Set unique server ID
  * server_id = 2
* Skip Replica to auto-start
  * skip_replica_start
* Replica should be read only
  * read_only

## Replication Configuration

* Take backup on Primary and get binary log file and position information from xtrabackup (or mariabackup for MariaDB). You can get this info from xtrabackup_binlog_info file in backup directory.

```sh
mariabackup --backup --target-dir=/path/to/your/backup/directory --user=your_backup_user --password=your_backup_password
mariabackup --prepare --target-dir=/path/to/your/backup/directory
cat /path/to/your/backup/directory/xtrabackup_binlog_info
```

"mariabackup --prepare" applies the transaction logs that were captured during the hot backup process to make the data files consistent.

* Transfer the prepared backup to Replica VM

```sh
# On Primary:
rsync -av --progress /tmp/mariadb_backup/ replica_user@10.128.0.5:/tmp/mariadb_restore/
# You might need to configure SSH keys for passwordless sudo if you want to run this automatically
# Or use scp if rsync is not convenient
# sudo scp -r /tmp/mariadb_backup/ replica_user@10.128.0.5:/tmp/mariadb_restore/
```

* Clear existing data directory on Replica

```sh
rm -rf /var/lib/mysql/*
```

* Restore the prepared backpu on Replica

```sh
mariabackup --copy-back --target-dir=/tmp/mariadb_restore
chown -R mysql:mysql /var/lib/mysql
```

*NOTE*: Ensure that output ends with `completed OK`. Above command copies the prepared backup files into the MariaDB data directory.

* Start MariaDB service on Replica

```sh
systemctl start mariadb
```

* Point to replica to primary to start pulling changes from the primary
  * CHANGE MASTER STATEMENT / CHANGE REPLICATION SOURCE TO STATEMENT (MariaDB 10.5+)

Example

```sh
mariadb -u root -p
```

```sql
stop slave;
-- or 
stop replica;

CHANGE MASTER TO
  MASTER_HOST='192.168.1.10',             -- Primary's IP (on-prem) or resolvable hostname
  MASTER_USER='replica_user',
  MASTER_PASSWORD='my_secure_replication_password',
  MASTER_PORT=3306,
  MASTER_LOG_FILE='mariadb-bin.000001',   -- File from xtrabackup_binlog_info
  MASTER_LOG_POS=123;                     -- Position from xtrabackup_binlog_info
```

* Start replication on Replica and check the status

```sql
start replica;
-- or 
start slave;

show replica status\G
```

## Verification of Replica

* On Replica, check the replica status.

```sql
show replica status\G
-- or
show slave status\G
```

Crucial lines are `Slave_IO_Running: Yes` and `Slave_SQL_Running: Yes`. Also check `Seconds_Behind_Master`, it should be 0 or close to 0.

* Check Primary, to see if the Replica is connected.

```sql
show processlist;
```

Look for process with `User='replica_user'` and  `Command='Binlog Dump`. This confirms that the replica is connected and pulling binary logs.

