# Position Based Replication

## Replica Server Setup

* Good to create replication.cnf
* Enable Relay Logs
  * relay-log = /var/log/mysql/relay/replica-bin
  * relay-log-index = /var/log/mysql/relay/replica-bin.index
* Set unique server ID
  * server-id = 2
* Skip Replica to auto-start
  * skip-replica-start
* Replica should be read only
  * read-only
