# Position Based Replication

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
