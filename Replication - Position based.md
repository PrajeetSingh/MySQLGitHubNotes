# Position Based Replication (Info is based on MariaDB but relevant to Percona MySQL too)

## Primary Server Setup

* Good to create replication.cnf file and enter below information in that and then "include" it in my.cnf file.
* Enable Binary Logging
  * log-bin = /var/log/mysql/binlog/prod-bin
  * log-bin-index = /var/log/mysql/binlog/prod-bin.index
* Setup unique server ID
  * server-id = 1
* Create dedicated replication user
  * CREATE USER replicator IDENTIFIED BY 'mySecretPassword';
  * GRANT REPLICATOIN SLAVE ON *.* TO replicator;
* Set binary log file format
  * binlog_format = ROW

## Replica Server Setup

* Good to create replication.cnf and enter below information in that.
* Enable Relay Logs
  * relay-log = /var/log/mysql/relay/replica-bin
  * relay-log-index = /var/log/mysql/relay/replica-bin.index
* Set unique server ID
  * server-id = 2
* Skip Replica to auto-start
  * skip-replica-start
* Replica should be read only
  * read-only

## Replication Configuration

* Get binary log file and position information from xtrabackup (or mariabackup for MariaDB). You can get this info from xtrabackup_binlog_info file in backup directory.
  * mariabackup --backup --target-dir=/path/to/your/backup/directory --user=your_backup_user --password=your_backup_password
  * mariabackup --prepare --target-dir=/path/to/your/backup/directory
  * cat /path/to/your/backup/directory/xtrabackup_binlog_info
* Point to replica to primary to start pulling changes from the primary
  * CHANGE MASTER STATEMENT / CHANGE REPLICATION SOURCE TO STATEMENT (MariaDB 10.5+)

Example

```sql
CHANGE MASTER TO
  MASTER_HOST='primary_ip_or_hostname',
  MASTER_USER='replication_user',
  MASTER_PASSWORD='replication_password',
  MASTER_LOG_FILE='mariadb-bin.000001',  -- From xtrabackup_binlog_info
  MASTER_LOG_POS=330;                   -- From xtrabackup_binlog_info
```

* Start Replica
  * START REPLICA;
* Verify replication status
  * show replica status\G

"mariabackup --prepare" applies the transaction logs that were captured during the hot backup process to make the data files consistent.
